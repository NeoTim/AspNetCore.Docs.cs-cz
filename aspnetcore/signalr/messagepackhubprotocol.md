---
title: Použití protokolu MessagePack hub v SignalR pro ASP.NET Core
author: bradygaster
description: Přidejte k ASP.NET Core protokol MessagePack hub SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/13/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: ab9bd11e37182f5b24db5595d5d050f4cc0e32da
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88626646"
---
# <a name="use-messagepack-hub-protocol-in-no-locsignalr-for-aspnet-core"></a><span data-ttu-id="4d235-103">Použití protokolu MessagePack hub v SignalR pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4d235-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="4d235-104">Tento článek předpokládá, že čtenář je obeznámen s tématy popsanými v tématu [Začínáme](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="4d235-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="4d235-105">Co je MessagePack?</span><span class="sxs-lookup"><span data-stu-id="4d235-105">What is MessagePack?</span></span>

<span data-ttu-id="4d235-106">[MessagePack](https://msgpack.org/index.html) je rychlý a kompaktní binární formát serializace.</span><span class="sxs-lookup"><span data-stu-id="4d235-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="4d235-107">To je užitečné v případě, že je důležité mít vliv na výkon a šířku pásma, protože vytváří menší zprávy ve srovnání se [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="4d235-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="4d235-108">Binární zprávy jsou nečitelný při prohlížení síťových trasování a protokolů, pokud nejsou bajty předávány pomocí MessagePack analyzátoru.</span><span class="sxs-lookup"><span data-stu-id="4d235-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="4d235-109">SignalR má integrovanou podporu formátu MessagePack a poskytuje rozhraní API pro použití klientem a serverem.</span><span class="sxs-lookup"><span data-stu-id="4d235-109">SignalR has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="4d235-110">Konfigurace MessagePack na serveru</span><span class="sxs-lookup"><span data-stu-id="4d235-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="4d235-111">Pokud chcete na serveru povolit protokol MessagePack hub, nainstalujte `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` balíček do své aplikace.</span><span class="sxs-lookup"><span data-stu-id="4d235-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="4d235-112">V `Startup.ConfigureServices` metodě přidejte `AddMessagePackProtocol` do `AddSignalR` volání, aby bylo možné povolit podporu MessagePack na serveru.</span><span class="sxs-lookup"><span data-stu-id="4d235-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="4d235-113">Ve výchozím nastavení je povolený formát JSON.</span><span class="sxs-lookup"><span data-stu-id="4d235-113">JSON is enabled by default.</span></span> <span data-ttu-id="4d235-114">Přidání MessagePack umožňuje podporu pro klienty JSON i MessagePack.</span><span class="sxs-lookup"><span data-stu-id="4d235-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="4d235-115">Pokud chcete přizpůsobit způsob, jakým MessagePack data naformátují, `AddMessagePackProtocol` převezme delegáta konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="4d235-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="4d235-116">V tomto delegátu `SerializerOptions` lze vlastnost použít ke konfiguraci možností serializace MessagePack.</span><span class="sxs-lookup"><span data-stu-id="4d235-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="4d235-117">Další informace o tom, jak překladače fungují, najdete v knihovně MessagePack na adrese [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="4d235-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="4d235-118">Atributy lze použít pro objekty, které chcete serializovat, chcete-li definovat, jak by měly být zpracovány.</span><span class="sxs-lookup"><span data-stu-id="4d235-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(new CustomResolver())
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

> [!WARNING]
> <span data-ttu-id="4d235-119">Důrazně doporučujeme zkontrolovat [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) a použít Doporučené opravy.</span><span class="sxs-lookup"><span data-stu-id="4d235-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="4d235-120">Například volání `.WithSecurity(MessagePackSecurity.UntrustedData)` při nahrazení `SerializerOptions` .</span><span class="sxs-lookup"><span data-stu-id="4d235-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="4d235-121">Konfigurace MessagePack na klientovi</span><span class="sxs-lookup"><span data-stu-id="4d235-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="4d235-122">Formát JSON je ve výchozím nastavení povolen pro podporované klienty.</span><span class="sxs-lookup"><span data-stu-id="4d235-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="4d235-123">Klienti můžou podporovat jenom jeden protokol.</span><span class="sxs-lookup"><span data-stu-id="4d235-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="4d235-124">Přidání podpory MessagePack nahradí všechny dříve nakonfigurované protokoly.</span><span class="sxs-lookup"><span data-stu-id="4d235-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="4d235-125">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="4d235-125">.NET client</span></span>

<span data-ttu-id="4d235-126">Pokud chcete povolit MessagePack v klientovi .NET, nainstalujte `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` balíček a zavolejte `AddMessagePackProtocol` na `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="4d235-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="4d235-127">Toto `AddMessagePackProtocol` volání přebírá delegáta pro konfiguraci možností, jako je server.</span><span class="sxs-lookup"><span data-stu-id="4d235-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="4d235-128">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="4d235-128">JavaScript client</span></span>

<span data-ttu-id="4d235-129">Podpora MessagePack pro klienta JavaScriptu je poskytována [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) balíčkem npm.</span><span class="sxs-lookup"><span data-stu-id="4d235-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="4d235-130">Nainstalujte balíček spuštěním následujícího příkazu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="4d235-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="4d235-131">Po instalaci balíčku npm lze modul použít přímo pomocí zavaděče modulu JavaScript nebo importovat do prohlížeče odkazem na následující soubor:</span><span class="sxs-lookup"><span data-stu-id="4d235-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="4d235-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="4d235-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="4d235-133">V prohlížeči `msgpack5` musí být také odkazováno na knihovnu.</span><span class="sxs-lookup"><span data-stu-id="4d235-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="4d235-134">`<script>`K vytvoření odkazu použijte značku.</span><span class="sxs-lookup"><span data-stu-id="4d235-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="4d235-135">Knihovnu najdete na adrese *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="4d235-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="4d235-136">Při použití `<script>` elementu je pořadí důležité.</span><span class="sxs-lookup"><span data-stu-id="4d235-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="4d235-137">Pokud je před *msgpack5.js*odkazováno na *signalr-protocol-msgpack.js* , dojde k chybě při pokusu o připojení k MessagePack.</span><span class="sxs-lookup"><span data-stu-id="4d235-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="4d235-138">*signalr.js* se také vyžaduje před *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="4d235-138">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="4d235-139">`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`Při přidání do `HubConnectionBuilder` nakonfiguruje klient používání protokolu MessagePack při připojování k serveru.</span><span class="sxs-lookup"><span data-stu-id="4d235-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="4d235-140">V tuto chvíli nejsou k dispozici žádné možnosti konfigurace pro protokol MessagePack pro klienta jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4d235-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="4d235-141">MessagePack adaptivní</span><span class="sxs-lookup"><span data-stu-id="4d235-141">MessagePack quirks</span></span>

<span data-ttu-id="4d235-142">Při používání protokolu centra MessagePack je potřeba vědět o několika problémech.</span><span class="sxs-lookup"><span data-stu-id="4d235-142">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="4d235-143">MessagePack rozlišuje velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="4d235-143">MessagePack is case-sensitive</span></span>

<span data-ttu-id="4d235-144">Protokol MessagePack rozlišuje velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="4d235-144">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="4d235-145">Zvažte například následující třídu jazyka C#:</span><span class="sxs-lookup"><span data-stu-id="4d235-145">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="4d235-146">Při odesílání z klienta jazyka JavaScript je nutné použít `PascalCased` názvy vlastností, protože velikost písmen se musí přesně shodovat s třídou jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="4d235-146">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="4d235-147">Příklad:</span><span class="sxs-lookup"><span data-stu-id="4d235-147">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="4d235-148">Použití `camelCased` názvů nebude správně svázáno se třídou jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="4d235-148">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="4d235-149">Můžete to obejít tak, že pomocí `Key` atributu zadáte jiný název vlastnosti MessagePack.</span><span class="sxs-lookup"><span data-stu-id="4d235-149">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="4d235-150">Další informace najdete [v dokumentaci k MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="4d235-150">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="4d235-151">DateTime. Kind se nezachová při serializaci nebo deserializaci.</span><span class="sxs-lookup"><span data-stu-id="4d235-151">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="4d235-152">Protokol MessagePack neposkytuje způsob, jak zakódovat `Kind` hodnotu `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="4d235-152">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="4d235-153">V důsledku toho se při deserializaci data MessagePack hub Protocol převede do formátu UTC, pokud `DateTime.Kind` je v opačném případě se `DateTimeKind.Local` nedotkne času a předáte tak, jak je.</span><span class="sxs-lookup"><span data-stu-id="4d235-153">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="4d235-154">Pokud pracujete s `DateTime` hodnotami, doporučujeme před odesláním převést na čas UTC.</span><span class="sxs-lookup"><span data-stu-id="4d235-154">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="4d235-155">Převeďte je ze standardu UTC na místní čas, když je přijmete.</span><span class="sxs-lookup"><span data-stu-id="4d235-155">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="4d235-156">DateTime. MinValue není podporován MessagePack v JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="4d235-156">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="4d235-157">Knihovna [msgpack5](https://github.com/mcollina/msgpack5) , kterou používá SignalR klient JavaScriptu, nepodporuje `timestamp96` typ v MessagePack.</span><span class="sxs-lookup"><span data-stu-id="4d235-157">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="4d235-158">Tento typ se používá ke kódování velmi velkých hodnot kalendářních dat (zcela brzy v minulosti nebo velmi daleko v budoucnosti).</span><span class="sxs-lookup"><span data-stu-id="4d235-158">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="4d235-159">Hodnota `DateTime.MinValue` je `January 1, 0001` , která musí být kódována v `timestamp96` hodnotě.</span><span class="sxs-lookup"><span data-stu-id="4d235-159">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="4d235-160">Z tohoto důvodu se odeslání `DateTime.MinValue` do klienta jazyka JavaScript nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="4d235-160">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="4d235-161">Když `DateTime.MinValue` je klient jazyka JavaScript přijat, je vyvolána následující chyba:</span><span class="sxs-lookup"><span data-stu-id="4d235-161">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="4d235-162">Obvykle `DateTime.MinValue` se používá ke kódování "chybějící" nebo " `null` hodnoty".</span><span class="sxs-lookup"><span data-stu-id="4d235-162">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="4d235-163">Pokud potřebujete tuto hodnotu v MessagePack zakódovat, použijte `DateTime` hodnotu Nullable ( `DateTime?` ) nebo zakódovat samostatnou `bool` hodnotu označující, jestli je datum k dispozici.</span><span class="sxs-lookup"><span data-stu-id="4d235-163">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="4d235-164">Další informace o tomto omezení najdete v tématu problém GitHubu [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="4d235-164">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="4d235-165">Podpora MessagePack v prostředí kompilace "před a za běhu"</span><span class="sxs-lookup"><span data-stu-id="4d235-165">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="4d235-166">Knihovna [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) , kterou používá klient a Server .NET, používá generování kódu k optimalizaci serializace.</span><span class="sxs-lookup"><span data-stu-id="4d235-166">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="4d235-167">V důsledku toho není podporován ve výchozím nastavení v prostředích, která používají "předdobu" kompilace (například Xamarin iOS nebo Unity).</span><span class="sxs-lookup"><span data-stu-id="4d235-167">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="4d235-168">V těchto prostředích je možné používat MessagePack pomocí "předběžného generování" kódu serializátoru/deserializace.</span><span class="sxs-lookup"><span data-stu-id="4d235-168">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="4d235-169">Další informace najdete [v dokumentaci k MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span><span class="sxs-lookup"><span data-stu-id="4d235-169">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="4d235-170">Jakmile jste předem vygenerovali serializátory, můžete je zaregistrovat pomocí delegáta konfigurace předaného `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="4d235-170">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        StaticCompositeResolver.Instance.Register(
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        );
        options.SerializerOptions = MessagePackSerializerOptions.Standard
            .WithResolver(StaticCompositeResolver.Instance)
            .WithSecurity(MessagePackSecurity.UntrustedData);
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="4d235-171">Kontroly typů jsou v MessagePack přísnější.</span><span class="sxs-lookup"><span data-stu-id="4d235-171">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="4d235-172">Protokol centra JSON provede převody typů během deserializace.</span><span class="sxs-lookup"><span data-stu-id="4d235-172">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="4d235-173">Například pokud má příchozí objekt hodnotu vlastnosti, která je číslo ( `{ foo: 42 }` ), ale vlastnost třídy .NET je typu `string` , hodnota bude převedena.</span><span class="sxs-lookup"><span data-stu-id="4d235-173">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="4d235-174">MessagePack však tento převod neprovede a vyvolá výjimku, kterou lze zobrazit v protokolech na straně serveru (a v konzole nástroje):</span><span class="sxs-lookup"><span data-stu-id="4d235-174">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="4d235-175">Další informace o tomto omezení najdete v tématu problém GitHubu [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="4d235-175">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="4d235-176">Související prostředky</span><span class="sxs-lookup"><span data-stu-id="4d235-176">Related resources</span></span>

* [<span data-ttu-id="4d235-177">Začínáme</span><span class="sxs-lookup"><span data-stu-id="4d235-177">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="4d235-178">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="4d235-178">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="4d235-179">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="4d235-179">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="4d235-180">Tento článek předpokládá, že čtenář je obeznámen s tématy popsanými v tématu [Začínáme](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="4d235-180">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="4d235-181">Co je MessagePack?</span><span class="sxs-lookup"><span data-stu-id="4d235-181">What is MessagePack?</span></span>

<span data-ttu-id="4d235-182">[MessagePack](https://msgpack.org/index.html) je rychlý a kompaktní binární formát serializace.</span><span class="sxs-lookup"><span data-stu-id="4d235-182">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="4d235-183">To je užitečné v případě, že je důležité mít vliv na výkon a šířku pásma, protože vytváří menší zprávy ve srovnání se [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="4d235-183">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="4d235-184">Binární zprávy jsou nečitelný při prohlížení síťových trasování a protokolů, pokud nejsou bajty předávány pomocí MessagePack analyzátoru.</span><span class="sxs-lookup"><span data-stu-id="4d235-184">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="4d235-185">SignalR má vestavěnou podporu pro formát MessagePack a poskytuje rozhraní API pro použití klientem a serverem.</span><span class="sxs-lookup"><span data-stu-id="4d235-185">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="4d235-186">Konfigurace MessagePack na serveru</span><span class="sxs-lookup"><span data-stu-id="4d235-186">Configure MessagePack on the server</span></span>

<span data-ttu-id="4d235-187">Pokud chcete na serveru povolit protokol MessagePack hub, nainstalujte `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` balíček do své aplikace.</span><span class="sxs-lookup"><span data-stu-id="4d235-187">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="4d235-188">V `Startup.ConfigureServices` metodě přidejte `AddMessagePackProtocol` do `AddSignalR` volání, aby bylo možné povolit podporu MessagePack na serveru.</span><span class="sxs-lookup"><span data-stu-id="4d235-188">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="4d235-189">Ve výchozím nastavení je povolený formát JSON.</span><span class="sxs-lookup"><span data-stu-id="4d235-189">JSON is enabled by default.</span></span> <span data-ttu-id="4d235-190">Přidání MessagePack umožňuje podporu pro klienty JSON i MessagePack.</span><span class="sxs-lookup"><span data-stu-id="4d235-190">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="4d235-191">Pokud chcete přizpůsobit způsob, jakým MessagePack data naformátují, `AddMessagePackProtocol` převezme delegáta konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="4d235-191">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="4d235-192">V tomto delegátu `FormatterResolvers` lze vlastnost použít ke konfiguraci možností serializace MessagePack.</span><span class="sxs-lookup"><span data-stu-id="4d235-192">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="4d235-193">Další informace o tom, jak překladače fungují, najdete v knihovně MessagePack na adrese [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="4d235-193">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="4d235-194">Atributy lze použít pro objekty, které chcete serializovat, chcete-li definovat, jak by měly být zpracovány.</span><span class="sxs-lookup"><span data-stu-id="4d235-194">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> <span data-ttu-id="4d235-195">Důrazně doporučujeme zkontrolovat [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) a použít Doporučené opravy.</span><span class="sxs-lookup"><span data-stu-id="4d235-195">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="4d235-196">Například nastavení `MessagePackSecurity.Active` statické vlastnosti na `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="4d235-196">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="4d235-197">Nastavení `MessagePackSecurity.Active` vyžaduje ruční instalaci [verze 1.9. x MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="4d235-197">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="4d235-198">Instalace `MessagePack` aktualizací 1.9. x používá verzi SignalR .</span><span class="sxs-lookup"><span data-stu-id="4d235-198">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="4d235-199">Když není `MessagePackSecurity.Active` nastavená na `MessagePackSecurity.UntrustedData` , škodlivý klient může způsobit odepření služby.</span><span class="sxs-lookup"><span data-stu-id="4d235-199">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="4d235-200">Nastavte `MessagePackSecurity.Active` v `Program.Main` , jak je znázorněno v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="4d235-200">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="4d235-201">Konfigurace MessagePack na klientovi</span><span class="sxs-lookup"><span data-stu-id="4d235-201">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="4d235-202">Formát JSON je ve výchozím nastavení povolen pro podporované klienty.</span><span class="sxs-lookup"><span data-stu-id="4d235-202">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="4d235-203">Klienti můžou podporovat jenom jeden protokol.</span><span class="sxs-lookup"><span data-stu-id="4d235-203">Clients can only support a single protocol.</span></span> <span data-ttu-id="4d235-204">Přidání podpory MessagePack nahradí všechny dříve nakonfigurované protokoly.</span><span class="sxs-lookup"><span data-stu-id="4d235-204">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="4d235-205">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="4d235-205">.NET client</span></span>

<span data-ttu-id="4d235-206">Pokud chcete povolit MessagePack v klientovi .NET, nainstalujte `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` balíček a zavolejte `AddMessagePackProtocol` na `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="4d235-206">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="4d235-207">Toto `AddMessagePackProtocol` volání přebírá delegáta pro konfiguraci možností, jako je server.</span><span class="sxs-lookup"><span data-stu-id="4d235-207">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="4d235-208">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="4d235-208">JavaScript client</span></span>

<span data-ttu-id="4d235-209">Podpora MessagePack pro klienta JavaScriptu je poskytována [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) balíčkem npm.</span><span class="sxs-lookup"><span data-stu-id="4d235-209">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="4d235-210">Nainstalujte balíček spuštěním následujícího příkazu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="4d235-210">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="4d235-211">Po instalaci balíčku npm lze modul použít přímo pomocí zavaděče modulu JavaScript nebo importovat do prohlížeče odkazem na následující soubor:</span><span class="sxs-lookup"><span data-stu-id="4d235-211">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="4d235-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="4d235-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="4d235-213">V prohlížeči `msgpack5` musí být také odkazováno na knihovnu.</span><span class="sxs-lookup"><span data-stu-id="4d235-213">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="4d235-214">`<script>`K vytvoření odkazu použijte značku.</span><span class="sxs-lookup"><span data-stu-id="4d235-214">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="4d235-215">Knihovnu najdete na adrese *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="4d235-215">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="4d235-216">Při použití `<script>` elementu je pořadí důležité.</span><span class="sxs-lookup"><span data-stu-id="4d235-216">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="4d235-217">Pokud je před *msgpack5.js*odkazováno na *signalr-protocol-msgpack.js* , dojde k chybě při pokusu o připojení k MessagePack.</span><span class="sxs-lookup"><span data-stu-id="4d235-217">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="4d235-218">*signalr.js* se také vyžaduje před *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="4d235-218">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="4d235-219">`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`Při přidání do `HubConnectionBuilder` nakonfiguruje klient používání protokolu MessagePack při připojování k serveru.</span><span class="sxs-lookup"><span data-stu-id="4d235-219">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="4d235-220">V tuto chvíli nejsou k dispozici žádné možnosti konfigurace pro protokol MessagePack pro klienta jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4d235-220">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="4d235-221">MessagePack adaptivní</span><span class="sxs-lookup"><span data-stu-id="4d235-221">MessagePack quirks</span></span>

<span data-ttu-id="4d235-222">Při používání protokolu centra MessagePack je potřeba vědět o několika problémech.</span><span class="sxs-lookup"><span data-stu-id="4d235-222">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="4d235-223">MessagePack rozlišuje velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="4d235-223">MessagePack is case-sensitive</span></span>

<span data-ttu-id="4d235-224">Protokol MessagePack rozlišuje velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="4d235-224">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="4d235-225">Zvažte například následující třídu jazyka C#:</span><span class="sxs-lookup"><span data-stu-id="4d235-225">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="4d235-226">Při odesílání z klienta jazyka JavaScript je nutné použít `PascalCased` názvy vlastností, protože velikost písmen se musí přesně shodovat s třídou jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="4d235-226">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="4d235-227">Příklad:</span><span class="sxs-lookup"><span data-stu-id="4d235-227">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="4d235-228">Použití `camelCased` názvů nebude správně svázáno se třídou jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="4d235-228">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="4d235-229">Můžete to obejít tak, že pomocí `Key` atributu zadáte jiný název vlastnosti MessagePack.</span><span class="sxs-lookup"><span data-stu-id="4d235-229">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="4d235-230">Další informace najdete [v dokumentaci k MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="4d235-230">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="4d235-231">DateTime. Kind se nezachová při serializaci nebo deserializaci.</span><span class="sxs-lookup"><span data-stu-id="4d235-231">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="4d235-232">Protokol MessagePack neposkytuje způsob, jak zakódovat `Kind` hodnotu `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="4d235-232">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="4d235-233">Výsledkem je, že při deserializaci data MessagePack hub Protocol předpokládá, že příchozí datum je ve formátu UTC.</span><span class="sxs-lookup"><span data-stu-id="4d235-233">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="4d235-234">Pokud pracujete s `DateTime` hodnotami v místním čase, doporučujeme před odesláním převést na čas UTC.</span><span class="sxs-lookup"><span data-stu-id="4d235-234">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="4d235-235">Převeďte je ze standardu UTC na místní čas, když je přijmete.</span><span class="sxs-lookup"><span data-stu-id="4d235-235">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="4d235-236">Další informace o tomto omezení najdete v tématu problém GitHubu [ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="4d235-236">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="4d235-237">DateTime. MinValue není podporován MessagePack v JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="4d235-237">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="4d235-238">Knihovna [msgpack5](https://github.com/mcollina/msgpack5) , kterou používá SignalR klient JavaScriptu, nepodporuje `timestamp96` typ v MessagePack.</span><span class="sxs-lookup"><span data-stu-id="4d235-238">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="4d235-239">Tento typ se používá ke kódování velmi velkých hodnot kalendářních dat (zcela brzy v minulosti nebo velmi daleko v budoucnosti).</span><span class="sxs-lookup"><span data-stu-id="4d235-239">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="4d235-240">Hodnota `DateTime.MinValue` je `January 1, 0001` , která musí být kódována v `timestamp96` hodnotě.</span><span class="sxs-lookup"><span data-stu-id="4d235-240">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="4d235-241">Z tohoto důvodu se odeslání `DateTime.MinValue` do klienta jazyka JavaScript nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="4d235-241">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="4d235-242">Když `DateTime.MinValue` je klient jazyka JavaScript přijat, je vyvolána následující chyba:</span><span class="sxs-lookup"><span data-stu-id="4d235-242">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="4d235-243">Obvykle `DateTime.MinValue` se používá ke kódování "chybějící" nebo " `null` hodnoty".</span><span class="sxs-lookup"><span data-stu-id="4d235-243">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="4d235-244">Pokud potřebujete tuto hodnotu v MessagePack zakódovat, použijte `DateTime` hodnotu Nullable ( `DateTime?` ) nebo zakódovat samostatnou `bool` hodnotu označující, jestli je datum k dispozici.</span><span class="sxs-lookup"><span data-stu-id="4d235-244">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="4d235-245">Další informace o tomto omezení najdete v tématu problém GitHubu [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="4d235-245">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="4d235-246">Podpora MessagePack v prostředí kompilace "před a za běhu"</span><span class="sxs-lookup"><span data-stu-id="4d235-246">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="4d235-247">Knihovna [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) , kterou používá klient a Server .NET, používá generování kódu k optimalizaci serializace.</span><span class="sxs-lookup"><span data-stu-id="4d235-247">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="4d235-248">V důsledku toho není podporován ve výchozím nastavení v prostředích, která používají "předdobu" kompilace (například Xamarin iOS nebo Unity).</span><span class="sxs-lookup"><span data-stu-id="4d235-248">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="4d235-249">V těchto prostředích je možné používat MessagePack pomocí "předběžného generování" kódu serializátoru/deserializace.</span><span class="sxs-lookup"><span data-stu-id="4d235-249">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="4d235-250">Další informace najdete [v dokumentaci k MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="4d235-250">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="4d235-251">Jakmile jste předem vygenerovali serializátory, můžete je zaregistrovat pomocí delegáta konfigurace předaného `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="4d235-251">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="4d235-252">Kontroly typů jsou v MessagePack přísnější.</span><span class="sxs-lookup"><span data-stu-id="4d235-252">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="4d235-253">Protokol centra JSON provede převody typů během deserializace.</span><span class="sxs-lookup"><span data-stu-id="4d235-253">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="4d235-254">Například pokud má příchozí objekt hodnotu vlastnosti, která je číslo ( `{ foo: 42 }` ), ale vlastnost třídy .NET je typu `string` , hodnota bude převedena.</span><span class="sxs-lookup"><span data-stu-id="4d235-254">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="4d235-255">MessagePack však tento převod neprovede a vyvolá výjimku, kterou lze zobrazit v protokolech na straně serveru (a v konzole nástroje):</span><span class="sxs-lookup"><span data-stu-id="4d235-255">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="4d235-256">Další informace o tomto omezení najdete v tématu problém GitHubu [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="4d235-256">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="4d235-257">Související prostředky</span><span class="sxs-lookup"><span data-stu-id="4d235-257">Related resources</span></span>

* [<span data-ttu-id="4d235-258">Začínáme</span><span class="sxs-lookup"><span data-stu-id="4d235-258">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="4d235-259">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="4d235-259">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="4d235-260">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="4d235-260">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="4d235-261">Tento článek předpokládá, že čtenář je obeznámen s tématy popsanými v tématu [Začínáme](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="4d235-261">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="4d235-262">Co je MessagePack?</span><span class="sxs-lookup"><span data-stu-id="4d235-262">What is MessagePack?</span></span>

<span data-ttu-id="4d235-263">[MessagePack](https://msgpack.org/index.html) je rychlý a kompaktní binární formát serializace.</span><span class="sxs-lookup"><span data-stu-id="4d235-263">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="4d235-264">To je užitečné v případě, že je důležité mít vliv na výkon a šířku pásma, protože vytváří menší zprávy ve srovnání se [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="4d235-264">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="4d235-265">Binární zprávy jsou nečitelný při prohlížení síťových trasování a protokolů, pokud nejsou bajty předávány pomocí MessagePack analyzátoru.</span><span class="sxs-lookup"><span data-stu-id="4d235-265">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="4d235-266">SignalR má vestavěnou podporu pro formát MessagePack a poskytuje rozhraní API pro použití klientem a serverem.</span><span class="sxs-lookup"><span data-stu-id="4d235-266">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="4d235-267">Konfigurace MessagePack na serveru</span><span class="sxs-lookup"><span data-stu-id="4d235-267">Configure MessagePack on the server</span></span>

<span data-ttu-id="4d235-268">Pokud chcete na serveru povolit protokol MessagePack hub, nainstalujte `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` balíček do své aplikace.</span><span class="sxs-lookup"><span data-stu-id="4d235-268">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="4d235-269">V `Startup.ConfigureServices` metodě přidejte `AddMessagePackProtocol` do `AddSignalR` volání, aby bylo možné povolit podporu MessagePack na serveru.</span><span class="sxs-lookup"><span data-stu-id="4d235-269">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="4d235-270">Ve výchozím nastavení je povolený formát JSON.</span><span class="sxs-lookup"><span data-stu-id="4d235-270">JSON is enabled by default.</span></span> <span data-ttu-id="4d235-271">Přidání MessagePack umožňuje podporu pro klienty JSON i MessagePack.</span><span class="sxs-lookup"><span data-stu-id="4d235-271">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="4d235-272">Pokud chcete přizpůsobit způsob, jakým MessagePack data naformátují, `AddMessagePackProtocol` převezme delegáta konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="4d235-272">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="4d235-273">V tomto delegátu `FormatterResolvers` lze vlastnost použít ke konfiguraci možností serializace MessagePack.</span><span class="sxs-lookup"><span data-stu-id="4d235-273">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="4d235-274">Další informace o tom, jak překladače fungují, najdete v knihovně MessagePack na adrese [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="4d235-274">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="4d235-275">Atributy lze použít pro objekty, které chcete serializovat, chcete-li definovat, jak by měly být zpracovány.</span><span class="sxs-lookup"><span data-stu-id="4d235-275">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

> [!WARNING]
> <span data-ttu-id="4d235-276">Důrazně doporučujeme zkontrolovat [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) a použít Doporučené opravy.</span><span class="sxs-lookup"><span data-stu-id="4d235-276">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="4d235-277">Například nastavení `MessagePackSecurity.Active` statické vlastnosti na `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="4d235-277">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="4d235-278">Nastavení `MessagePackSecurity.Active` vyžaduje ruční instalaci [verze 1.9. x MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="4d235-278">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="4d235-279">Instalace `MessagePack` aktualizací 1.9. x používá verzi SignalR .</span><span class="sxs-lookup"><span data-stu-id="4d235-279">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="4d235-280">Když není `MessagePackSecurity.Active` nastavená na `MessagePackSecurity.UntrustedData` , škodlivý klient může způsobit odepření služby.</span><span class="sxs-lookup"><span data-stu-id="4d235-280">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="4d235-281">Nastavte `MessagePackSecurity.Active` v `Program.Main` , jak je znázorněno v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="4d235-281">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="4d235-282">Konfigurace MessagePack na klientovi</span><span class="sxs-lookup"><span data-stu-id="4d235-282">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="4d235-283">Formát JSON je ve výchozím nastavení povolen pro podporované klienty.</span><span class="sxs-lookup"><span data-stu-id="4d235-283">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="4d235-284">Klienti můžou podporovat jenom jeden protokol.</span><span class="sxs-lookup"><span data-stu-id="4d235-284">Clients can only support a single protocol.</span></span> <span data-ttu-id="4d235-285">Přidání podpory MessagePack nahradí všechny dříve nakonfigurované protokoly.</span><span class="sxs-lookup"><span data-stu-id="4d235-285">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="4d235-286">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="4d235-286">.NET client</span></span>

<span data-ttu-id="4d235-287">Pokud chcete povolit MessagePack v klientovi .NET, nainstalujte `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` balíček a zavolejte `AddMessagePackProtocol` na `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="4d235-287">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="4d235-288">Toto `AddMessagePackProtocol` volání přebírá delegáta pro konfiguraci možností, jako je server.</span><span class="sxs-lookup"><span data-stu-id="4d235-288">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="4d235-289">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="4d235-289">JavaScript client</span></span>

<span data-ttu-id="4d235-290">Podpora MessagePack pro klienta JavaScriptu je poskytována [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) balíčkem npm.</span><span class="sxs-lookup"><span data-stu-id="4d235-290">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="4d235-291">Nainstalujte balíček spuštěním následujícího příkazu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="4d235-291">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="4d235-292">Po instalaci balíčku npm lze modul použít přímo pomocí zavaděče modulu JavaScript nebo importovat do prohlížeče odkazem na následující soubor:</span><span class="sxs-lookup"><span data-stu-id="4d235-292">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="4d235-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="4d235-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="4d235-294">V prohlížeči `msgpack5` musí být také odkazováno na knihovnu.</span><span class="sxs-lookup"><span data-stu-id="4d235-294">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="4d235-295">`<script>`K vytvoření odkazu použijte značku.</span><span class="sxs-lookup"><span data-stu-id="4d235-295">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="4d235-296">Knihovnu najdete na adrese *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="4d235-296">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="4d235-297">Při použití `<script>` elementu je pořadí důležité.</span><span class="sxs-lookup"><span data-stu-id="4d235-297">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="4d235-298">Pokud je před *msgpack5.js*odkazováno na *signalr-protocol-msgpack.js* , dojde k chybě při pokusu o připojení k MessagePack.</span><span class="sxs-lookup"><span data-stu-id="4d235-298">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="4d235-299">*signalr.js* se také vyžaduje před *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="4d235-299">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="4d235-300">`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`Při přidání do `HubConnectionBuilder` nakonfiguruje klient používání protokolu MessagePack při připojování k serveru.</span><span class="sxs-lookup"><span data-stu-id="4d235-300">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="4d235-301">V tuto chvíli nejsou k dispozici žádné možnosti konfigurace pro protokol MessagePack pro klienta jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4d235-301">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="4d235-302">MessagePack adaptivní</span><span class="sxs-lookup"><span data-stu-id="4d235-302">MessagePack quirks</span></span>

<span data-ttu-id="4d235-303">Při používání protokolu centra MessagePack je potřeba vědět o několika problémech.</span><span class="sxs-lookup"><span data-stu-id="4d235-303">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="4d235-304">MessagePack rozlišuje velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="4d235-304">MessagePack is case-sensitive</span></span>

<span data-ttu-id="4d235-305">Protokol MessagePack rozlišuje velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="4d235-305">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="4d235-306">Zvažte například následující třídu jazyka C#:</span><span class="sxs-lookup"><span data-stu-id="4d235-306">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="4d235-307">Při odesílání z klienta jazyka JavaScript je nutné použít `PascalCased` názvy vlastností, protože velikost písmen se musí přesně shodovat s třídou jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="4d235-307">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="4d235-308">Příklad:</span><span class="sxs-lookup"><span data-stu-id="4d235-308">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="4d235-309">Použití `camelCased` názvů nebude správně svázáno se třídou jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="4d235-309">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="4d235-310">Můžete to obejít tak, že pomocí `Key` atributu zadáte jiný název vlastnosti MessagePack.</span><span class="sxs-lookup"><span data-stu-id="4d235-310">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="4d235-311">Další informace najdete [v dokumentaci k MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="4d235-311">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="4d235-312">DateTime. Kind se nezachová při serializaci nebo deserializaci.</span><span class="sxs-lookup"><span data-stu-id="4d235-312">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="4d235-313">Protokol MessagePack neposkytuje způsob, jak zakódovat `Kind` hodnotu `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="4d235-313">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="4d235-314">Výsledkem je, že při deserializaci data MessagePack hub Protocol předpokládá, že příchozí datum je ve formátu UTC.</span><span class="sxs-lookup"><span data-stu-id="4d235-314">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="4d235-315">Pokud pracujete s `DateTime` hodnotami v místním čase, doporučujeme před odesláním převést na čas UTC.</span><span class="sxs-lookup"><span data-stu-id="4d235-315">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="4d235-316">Převeďte je ze standardu UTC na místní čas, když je přijmete.</span><span class="sxs-lookup"><span data-stu-id="4d235-316">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="4d235-317">Další informace o tomto omezení najdete v tématu problém GitHubu [ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="4d235-317">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="4d235-318">DateTime. MinValue není podporován MessagePack v JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="4d235-318">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="4d235-319">Knihovna [msgpack5](https://github.com/mcollina/msgpack5) , kterou používá SignalR klient JavaScriptu, nepodporuje `timestamp96` typ v MessagePack.</span><span class="sxs-lookup"><span data-stu-id="4d235-319">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="4d235-320">Tento typ se používá ke kódování velmi velkých hodnot kalendářních dat (zcela brzy v minulosti nebo velmi daleko v budoucnosti).</span><span class="sxs-lookup"><span data-stu-id="4d235-320">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="4d235-321">Hodnota je, `DateTime.MinValue` `January 1, 0001` která musí být kódována v `timestamp96` hodnotě.</span><span class="sxs-lookup"><span data-stu-id="4d235-321">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="4d235-322">Z tohoto důvodu se odeslání `DateTime.MinValue` do klienta jazyka JavaScript nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="4d235-322">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="4d235-323">Když `DateTime.MinValue` je klient jazyka JavaScript přijat, je vyvolána následující chyba:</span><span class="sxs-lookup"><span data-stu-id="4d235-323">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="4d235-324">Obvykle `DateTime.MinValue` se používá ke kódování "chybějící" nebo " `null` hodnoty".</span><span class="sxs-lookup"><span data-stu-id="4d235-324">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="4d235-325">Pokud potřebujete tuto hodnotu v MessagePack zakódovat, použijte `DateTime` hodnotu Nullable ( `DateTime?` ) nebo zakódovat samostatnou `bool` hodnotu označující, jestli je datum k dispozici.</span><span class="sxs-lookup"><span data-stu-id="4d235-325">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="4d235-326">Další informace o tomto omezení najdete v tématu problém GitHubu [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="4d235-326">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="4d235-327">Podpora MessagePack v prostředí kompilace "před a za běhu"</span><span class="sxs-lookup"><span data-stu-id="4d235-327">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="4d235-328">Knihovna [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) , kterou používá klient a Server .NET, používá generování kódu k optimalizaci serializace.</span><span class="sxs-lookup"><span data-stu-id="4d235-328">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="4d235-329">V důsledku toho není podporován ve výchozím nastavení v prostředích, která používají "předdobu" kompilace (například Xamarin iOS nebo Unity).</span><span class="sxs-lookup"><span data-stu-id="4d235-329">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="4d235-330">V těchto prostředích je možné používat MessagePack pomocí "předběžného generování" kódu serializátoru/deserializace.</span><span class="sxs-lookup"><span data-stu-id="4d235-330">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="4d235-331">Další informace najdete [v dokumentaci k MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="4d235-331">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="4d235-332">Jakmile jste předem vygenerovali serializátory, můžete je zaregistrovat pomocí delegáta konfigurace předaného `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="4d235-332">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol(options =>
    {
        options.FormatterResolvers = new List<MessagePack.IFormatterResolver>()
        {
            MessagePack.Resolvers.GeneratedResolver.Instance,
            MessagePack.Resolvers.StandardResolver.Instance
        };
    });
```

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="4d235-333">Kontroly typů jsou v MessagePack přísnější.</span><span class="sxs-lookup"><span data-stu-id="4d235-333">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="4d235-334">Protokol centra JSON provede převody typů během deserializace.</span><span class="sxs-lookup"><span data-stu-id="4d235-334">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="4d235-335">Například pokud má příchozí objekt hodnotu vlastnosti, která je číslo ( `{ foo: 42 }` ), ale vlastnost třídy .NET je typu `string` , hodnota bude převedena.</span><span class="sxs-lookup"><span data-stu-id="4d235-335">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="4d235-336">MessagePack však tento převod neprovede a vyvolá výjimku, kterou lze zobrazit v protokolech na straně serveru (a v konzole nástroje):</span><span class="sxs-lookup"><span data-stu-id="4d235-336">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="4d235-337">Další informace o tomto omezení najdete v tématu problém GitHubu [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="4d235-337">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="4d235-338">Související prostředky</span><span class="sxs-lookup"><span data-stu-id="4d235-338">Related resources</span></span>

* [<span data-ttu-id="4d235-339">Začínáme</span><span class="sxs-lookup"><span data-stu-id="4d235-339">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="4d235-340">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="4d235-340">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="4d235-341">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="4d235-341">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
