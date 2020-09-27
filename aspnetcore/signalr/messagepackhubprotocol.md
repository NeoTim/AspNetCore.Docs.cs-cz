---
title: Použití protokolu MessagePack hub v SignalR pro ASP.NET Core
author: bradygaster
description: Přidejte k ASP.NET Core protokol MessagePack hub SignalR .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 09/24/2020
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
ms.openlocfilehash: 15ae8911e2ffba43c7bb885efd153d8b6803bc2a
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393675"
---
# <a name="use-messagepack-hub-protocol-in-no-locsignalr-for-aspnet-core"></a><span data-ttu-id="2f46c-103">Použití protokolu MessagePack hub v SignalR pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2f46c-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="2f46c-104">Tento článek předpokládá, že čtenář je obeznámen s tématy popsanými v tématu [Začínáme](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="2f46c-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="2f46c-105">Co je MessagePack?</span><span class="sxs-lookup"><span data-stu-id="2f46c-105">What is MessagePack?</span></span>

<span data-ttu-id="2f46c-106">[MessagePack](https://msgpack.org/index.html) je rychlý a kompaktní binární formát serializace.</span><span class="sxs-lookup"><span data-stu-id="2f46c-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="2f46c-107">To je užitečné v případě, že je důležité mít vliv na výkon a šířku pásma, protože vytváří menší zprávy ve srovnání se [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="2f46c-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="2f46c-108">Binární zprávy jsou nečitelný při prohlížení síťových trasování a protokolů, pokud nejsou bajty předávány pomocí MessagePack analyzátoru.</span><span class="sxs-lookup"><span data-stu-id="2f46c-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="2f46c-109">SignalR má integrovanou podporu formátu MessagePack a poskytuje rozhraní API pro použití klientem a serverem.</span><span class="sxs-lookup"><span data-stu-id="2f46c-109">SignalR has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="2f46c-110">Konfigurace MessagePack na serveru</span><span class="sxs-lookup"><span data-stu-id="2f46c-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="2f46c-111">Pokud chcete na serveru povolit protokol MessagePack hub, nainstalujte `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` balíček do své aplikace.</span><span class="sxs-lookup"><span data-stu-id="2f46c-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="2f46c-112">V `Startup.ConfigureServices` metodě přidejte `AddMessagePackProtocol` do `AddSignalR` volání, aby bylo možné povolit podporu MessagePack na serveru.</span><span class="sxs-lookup"><span data-stu-id="2f46c-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="2f46c-113">Ve výchozím nastavení je povolený formát JSON.</span><span class="sxs-lookup"><span data-stu-id="2f46c-113">JSON is enabled by default.</span></span> <span data-ttu-id="2f46c-114">Přidání MessagePack umožňuje podporu pro klienty JSON i MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2f46c-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="2f46c-115">Pokud chcete přizpůsobit způsob, jakým MessagePack data naformátují, `AddMessagePackProtocol` převezme delegáta konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="2f46c-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="2f46c-116">V tomto delegátu `SerializerOptions` lze vlastnost použít ke konfiguraci možností serializace MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2f46c-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="2f46c-117">Další informace o tom, jak překladače fungují, najdete v knihovně MessagePack na adrese [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="2f46c-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="2f46c-118">Atributy lze použít pro objekty, které chcete serializovat, chcete-li definovat, jak by měly být zpracovány.</span><span class="sxs-lookup"><span data-stu-id="2f46c-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="2f46c-119">Důrazně doporučujeme zkontrolovat [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) a použít Doporučené opravy.</span><span class="sxs-lookup"><span data-stu-id="2f46c-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="2f46c-120">Například volání `.WithSecurity(MessagePackSecurity.UntrustedData)` při nahrazení `SerializerOptions` .</span><span class="sxs-lookup"><span data-stu-id="2f46c-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="2f46c-121">Konfigurace MessagePack na klientovi</span><span class="sxs-lookup"><span data-stu-id="2f46c-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="2f46c-122">Formát JSON je ve výchozím nastavení povolen pro podporované klienty.</span><span class="sxs-lookup"><span data-stu-id="2f46c-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="2f46c-123">Klienti můžou podporovat jenom jeden protokol.</span><span class="sxs-lookup"><span data-stu-id="2f46c-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="2f46c-124">Přidání podpory MessagePack nahradí všechny dříve nakonfigurované protokoly.</span><span class="sxs-lookup"><span data-stu-id="2f46c-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="2f46c-125">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="2f46c-125">.NET client</span></span>

<span data-ttu-id="2f46c-126">Pokud chcete povolit MessagePack v klientovi .NET, nainstalujte `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` balíček a zavolejte `AddMessagePackProtocol` na `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="2f46c-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="2f46c-127">Toto `AddMessagePackProtocol` volání přebírá delegáta pro konfiguraci možností, jako je server.</span><span class="sxs-lookup"><span data-stu-id="2f46c-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="2f46c-128">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="2f46c-128">JavaScript client</span></span>

<span data-ttu-id="2f46c-129">Podpora MessagePack pro klienta JavaScriptu je poskytována [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) balíčkem npm.</span><span class="sxs-lookup"><span data-stu-id="2f46c-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="2f46c-130">Nainstalujte balíček spuštěním následujícího příkazu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="2f46c-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="2f46c-131">Po instalaci balíčku npm lze modul použít přímo pomocí zavaděče modulu JavaScript nebo importovat do prohlížeče odkazem na následující soubor:</span><span class="sxs-lookup"><span data-stu-id="2f46c-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="2f46c-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="2f46c-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="2f46c-133">V prohlížeči `msgpack5` musí být také odkazováno na knihovnu.</span><span class="sxs-lookup"><span data-stu-id="2f46c-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="2f46c-134">`<script>`K vytvoření odkazu použijte značku.</span><span class="sxs-lookup"><span data-stu-id="2f46c-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="2f46c-135">Knihovnu najdete na adrese *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="2f46c-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="2f46c-136">Při použití `<script>` elementu je pořadí důležité.</span><span class="sxs-lookup"><span data-stu-id="2f46c-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="2f46c-137">Pokud je před *msgpack5.js*odkazováno na *signalr-protocol-msgpack.js* , dojde k chybě při pokusu o připojení k MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2f46c-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="2f46c-138">*signalr.js* se také vyžaduje před *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="2f46c-138">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="2f46c-139">`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`Při přidání do `HubConnectionBuilder` nakonfiguruje klient používání protokolu MessagePack při připojování k serveru.</span><span class="sxs-lookup"><span data-stu-id="2f46c-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="2f46c-140">V tuto chvíli nejsou k dispozici žádné možnosti konfigurace pro protokol MessagePack pro klienta jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2f46c-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="2f46c-141">MessagePack adaptivní</span><span class="sxs-lookup"><span data-stu-id="2f46c-141">MessagePack quirks</span></span>

<span data-ttu-id="2f46c-142">Při používání protokolu centra MessagePack je potřeba vědět o několika problémech.</span><span class="sxs-lookup"><span data-stu-id="2f46c-142">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="2f46c-143">MessagePack rozlišuje velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="2f46c-143">MessagePack is case-sensitive</span></span>

<span data-ttu-id="2f46c-144">Protokol MessagePack rozlišuje velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="2f46c-144">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="2f46c-145">Zvažte například následující třídu jazyka C#:</span><span class="sxs-lookup"><span data-stu-id="2f46c-145">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="2f46c-146">Při odesílání z klienta jazyka JavaScript je nutné použít `PascalCased` názvy vlastností, protože velikost písmen se musí přesně shodovat s třídou jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="2f46c-146">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="2f46c-147">Příklad:</span><span class="sxs-lookup"><span data-stu-id="2f46c-147">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="2f46c-148">Použití `camelCased` názvů nebude správně svázáno se třídou jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="2f46c-148">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="2f46c-149">Můžete to obejít tak, že pomocí `Key` atributu zadáte jiný název vlastnosti MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2f46c-149">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="2f46c-150">Další informace najdete [v dokumentaci k MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="2f46c-150">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="2f46c-151">DateTime. Kind se nezachová při serializaci nebo deserializaci.</span><span class="sxs-lookup"><span data-stu-id="2f46c-151">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="2f46c-152">Protokol MessagePack neposkytuje způsob, jak zakódovat `Kind` hodnotu `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="2f46c-152">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="2f46c-153">V důsledku toho se při deserializaci data MessagePack hub Protocol převede do formátu UTC, pokud `DateTime.Kind` je v opačném případě se `DateTimeKind.Local` nedotkne času a předáte tak, jak je.</span><span class="sxs-lookup"><span data-stu-id="2f46c-153">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="2f46c-154">Pokud pracujete s `DateTime` hodnotami, doporučujeme před odesláním převést na čas UTC.</span><span class="sxs-lookup"><span data-stu-id="2f46c-154">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="2f46c-155">Převeďte je ze standardu UTC na místní čas, když je přijmete.</span><span class="sxs-lookup"><span data-stu-id="2f46c-155">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="2f46c-156">DateTime. MinValue není podporován MessagePack v JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="2f46c-156">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="2f46c-157">Knihovna [msgpack5](https://github.com/mcollina/msgpack5) , kterou používá SignalR klient JavaScriptu, nepodporuje `timestamp96` typ v MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2f46c-157">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="2f46c-158">Tento typ se používá ke kódování velmi velkých hodnot kalendářních dat (zcela brzy v minulosti nebo velmi daleko v budoucnosti).</span><span class="sxs-lookup"><span data-stu-id="2f46c-158">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="2f46c-159">Hodnota `DateTime.MinValue` je `January 1, 0001` , která musí být kódována v `timestamp96` hodnotě.</span><span class="sxs-lookup"><span data-stu-id="2f46c-159">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="2f46c-160">Z tohoto důvodu se odeslání `DateTime.MinValue` do klienta jazyka JavaScript nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="2f46c-160">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="2f46c-161">Když `DateTime.MinValue` je klient jazyka JavaScript přijat, je vyvolána následující chyba:</span><span class="sxs-lookup"><span data-stu-id="2f46c-161">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="2f46c-162">Obvykle `DateTime.MinValue` se používá ke kódování "chybějící" nebo " `null` hodnoty".</span><span class="sxs-lookup"><span data-stu-id="2f46c-162">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="2f46c-163">Pokud potřebujete tuto hodnotu v MessagePack zakódovat, použijte `DateTime` hodnotu Nullable ( `DateTime?` ) nebo zakódovat samostatnou `bool` hodnotu označující, jestli je datum k dispozici.</span><span class="sxs-lookup"><span data-stu-id="2f46c-163">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="2f46c-164">Další informace o tomto omezení najdete v tématu problém GitHubu [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="2f46c-164">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="2f46c-165">Podpora MessagePack v prostředí kompilace "před a za běhu"</span><span class="sxs-lookup"><span data-stu-id="2f46c-165">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="2f46c-166">Knihovna [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) , kterou používá klient a Server .NET, používá generování kódu k optimalizaci serializace.</span><span class="sxs-lookup"><span data-stu-id="2f46c-166">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="2f46c-167">V důsledku toho není podporován ve výchozím nastavení v prostředích, která používají "předdobu" kompilace (například Xamarin iOS nebo Unity).</span><span class="sxs-lookup"><span data-stu-id="2f46c-167">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="2f46c-168">V těchto prostředích je možné používat MessagePack pomocí "předběžného generování" kódu serializátoru/deserializace.</span><span class="sxs-lookup"><span data-stu-id="2f46c-168">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="2f46c-169">Další informace najdete [v dokumentaci k MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span><span class="sxs-lookup"><span data-stu-id="2f46c-169">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="2f46c-170">Jakmile jste předem vygenerovali serializátory, můžete je zaregistrovat pomocí delegáta konfigurace předaného `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="2f46c-170">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="2f46c-171">Kontroly typů jsou v MessagePack přísnější.</span><span class="sxs-lookup"><span data-stu-id="2f46c-171">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="2f46c-172">Protokol centra JSON provede převody typů během deserializace.</span><span class="sxs-lookup"><span data-stu-id="2f46c-172">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="2f46c-173">Například pokud má příchozí objekt hodnotu vlastnosti, která je číslo ( `{ foo: 42 }` ), ale vlastnost třídy .NET je typu `string` , hodnota bude převedena.</span><span class="sxs-lookup"><span data-stu-id="2f46c-173">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="2f46c-174">MessagePack však tento převod neprovede a vyvolá výjimku, kterou lze zobrazit v protokolech na straně serveru (a v konzole nástroje):</span><span class="sxs-lookup"><span data-stu-id="2f46c-174">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="2f46c-175">Další informace o tomto omezení najdete v tématu problém GitHubu [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="2f46c-175">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="2f46c-176">Související prostředky</span><span class="sxs-lookup"><span data-stu-id="2f46c-176">Related resources</span></span>

* [<span data-ttu-id="2f46c-177">Začínáme</span><span class="sxs-lookup"><span data-stu-id="2f46c-177">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="2f46c-178">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="2f46c-178">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="2f46c-179">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="2f46c-179">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="2f46c-180">Tento článek předpokládá, že čtenář je obeznámen s tématy popsanými v tématu [Začínáme](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="2f46c-180">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="2f46c-181">Co je MessagePack?</span><span class="sxs-lookup"><span data-stu-id="2f46c-181">What is MessagePack?</span></span>

<span data-ttu-id="2f46c-182">[MessagePack](https://msgpack.org/index.html) je rychlý a kompaktní binární formát serializace.</span><span class="sxs-lookup"><span data-stu-id="2f46c-182">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="2f46c-183">To je užitečné v případě, že je důležité mít vliv na výkon a šířku pásma, protože vytváří menší zprávy ve srovnání se [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="2f46c-183">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="2f46c-184">Binární zprávy jsou nečitelný při prohlížení síťových trasování a protokolů, pokud nejsou bajty předávány pomocí MessagePack analyzátoru.</span><span class="sxs-lookup"><span data-stu-id="2f46c-184">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="2f46c-185">SignalR má vestavěnou podporu pro formát MessagePack a poskytuje rozhraní API pro použití klientem a serverem.</span><span class="sxs-lookup"><span data-stu-id="2f46c-185">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="2f46c-186">Konfigurace MessagePack na serveru</span><span class="sxs-lookup"><span data-stu-id="2f46c-186">Configure MessagePack on the server</span></span>

<span data-ttu-id="2f46c-187">Pokud chcete na serveru povolit protokol MessagePack hub, nainstalujte `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` balíček do své aplikace.</span><span class="sxs-lookup"><span data-stu-id="2f46c-187">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="2f46c-188">V `Startup.ConfigureServices` metodě přidejte `AddMessagePackProtocol` do `AddSignalR` volání, aby bylo možné povolit podporu MessagePack na serveru.</span><span class="sxs-lookup"><span data-stu-id="2f46c-188">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="2f46c-189">Ve výchozím nastavení je povolený formát JSON.</span><span class="sxs-lookup"><span data-stu-id="2f46c-189">JSON is enabled by default.</span></span> <span data-ttu-id="2f46c-190">Přidání MessagePack umožňuje podporu pro klienty JSON i MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2f46c-190">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="2f46c-191">Pokud chcete přizpůsobit způsob, jakým MessagePack data naformátují, `AddMessagePackProtocol` převezme delegáta konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="2f46c-191">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="2f46c-192">V tomto delegátu `FormatterResolvers` lze vlastnost použít ke konfiguraci možností serializace MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2f46c-192">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="2f46c-193">Další informace o tom, jak překladače fungují, najdete v knihovně MessagePack na adrese [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="2f46c-193">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="2f46c-194">Atributy lze použít pro objekty, které chcete serializovat, chcete-li definovat, jak by měly být zpracovány.</span><span class="sxs-lookup"><span data-stu-id="2f46c-194">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="2f46c-195">Důrazně doporučujeme zkontrolovat [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) a použít Doporučené opravy.</span><span class="sxs-lookup"><span data-stu-id="2f46c-195">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="2f46c-196">Například nastavení `MessagePackSecurity.Active` statické vlastnosti na `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="2f46c-196">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="2f46c-197">Nastavení `MessagePackSecurity.Active` vyžaduje ruční instalaci [verze 1.9. x MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="2f46c-197">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="2f46c-198">Instalace `MessagePack` aktualizací 1.9. x používá verzi SignalR .</span><span class="sxs-lookup"><span data-stu-id="2f46c-198">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="2f46c-199">`MessagePack` verze 2. x představila zásadní změny a není kompatibilní s SignalR verzemi 3,1 a staršími.</span><span class="sxs-lookup"><span data-stu-id="2f46c-199">`MessagePack` version 2.x introduced breaking changes and is incompatible with SignalR versions 3.1 and earlier.</span></span> <span data-ttu-id="2f46c-200">Když `MessagePackSecurity.Active` není nastavená na `MessagePackSecurity.UntrustedData` , škodlivý klient může způsobit odepření služby.</span><span class="sxs-lookup"><span data-stu-id="2f46c-200">When `MessagePackSecurity.Active` isn't set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="2f46c-201">Nastavte `MessagePackSecurity.Active` v `Program.Main` , jak je znázorněno v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="2f46c-201">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="2f46c-202">Konfigurace MessagePack na klientovi</span><span class="sxs-lookup"><span data-stu-id="2f46c-202">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="2f46c-203">Formát JSON je ve výchozím nastavení povolen pro podporované klienty.</span><span class="sxs-lookup"><span data-stu-id="2f46c-203">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="2f46c-204">Klienti můžou podporovat jenom jeden protokol.</span><span class="sxs-lookup"><span data-stu-id="2f46c-204">Clients can only support a single protocol.</span></span> <span data-ttu-id="2f46c-205">Přidání podpory MessagePack nahradí všechny dříve nakonfigurované protokoly.</span><span class="sxs-lookup"><span data-stu-id="2f46c-205">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="2f46c-206">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="2f46c-206">.NET client</span></span>

<span data-ttu-id="2f46c-207">Pokud chcete povolit MessagePack v klientovi .NET, nainstalujte `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` balíček a zavolejte `AddMessagePackProtocol` na `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="2f46c-207">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="2f46c-208">Toto `AddMessagePackProtocol` volání přebírá delegáta pro konfiguraci možností, jako je server.</span><span class="sxs-lookup"><span data-stu-id="2f46c-208">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="2f46c-209">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="2f46c-209">JavaScript client</span></span>

<span data-ttu-id="2f46c-210">Podpora MessagePack pro klienta JavaScriptu je poskytována [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) balíčkem npm.</span><span class="sxs-lookup"><span data-stu-id="2f46c-210">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="2f46c-211">Nainstalujte balíček spuštěním následujícího příkazu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="2f46c-211">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="2f46c-212">Po instalaci balíčku npm lze modul použít přímo pomocí zavaděče modulu JavaScript nebo importovat do prohlížeče odkazem na následující soubor:</span><span class="sxs-lookup"><span data-stu-id="2f46c-212">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="2f46c-213">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="2f46c-213">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="2f46c-214">V prohlížeči `msgpack5` musí být také odkazováno na knihovnu.</span><span class="sxs-lookup"><span data-stu-id="2f46c-214">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="2f46c-215">`<script>`K vytvoření odkazu použijte značku.</span><span class="sxs-lookup"><span data-stu-id="2f46c-215">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="2f46c-216">Knihovnu najdete na adrese *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="2f46c-216">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="2f46c-217">Při použití `<script>` elementu je pořadí důležité.</span><span class="sxs-lookup"><span data-stu-id="2f46c-217">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="2f46c-218">Pokud je před *msgpack5.js*odkazováno na *signalr-protocol-msgpack.js* , dojde k chybě při pokusu o připojení k MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2f46c-218">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="2f46c-219">*signalr.js* se také vyžaduje před *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="2f46c-219">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="2f46c-220">`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`Při přidání do `HubConnectionBuilder` nakonfiguruje klient používání protokolu MessagePack při připojování k serveru.</span><span class="sxs-lookup"><span data-stu-id="2f46c-220">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="2f46c-221">V tuto chvíli nejsou k dispozici žádné možnosti konfigurace pro protokol MessagePack pro klienta jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2f46c-221">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="2f46c-222">MessagePack adaptivní</span><span class="sxs-lookup"><span data-stu-id="2f46c-222">MessagePack quirks</span></span>

<span data-ttu-id="2f46c-223">Při používání protokolu centra MessagePack je potřeba vědět o několika problémech.</span><span class="sxs-lookup"><span data-stu-id="2f46c-223">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="2f46c-224">MessagePack rozlišuje velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="2f46c-224">MessagePack is case-sensitive</span></span>

<span data-ttu-id="2f46c-225">Protokol MessagePack rozlišuje velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="2f46c-225">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="2f46c-226">Zvažte například následující třídu jazyka C#:</span><span class="sxs-lookup"><span data-stu-id="2f46c-226">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="2f46c-227">Při odesílání z klienta jazyka JavaScript je nutné použít `PascalCased` názvy vlastností, protože velikost písmen se musí přesně shodovat s třídou jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="2f46c-227">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="2f46c-228">Příklad:</span><span class="sxs-lookup"><span data-stu-id="2f46c-228">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="2f46c-229">Použití `camelCased` názvů nebude správně svázáno se třídou jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="2f46c-229">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="2f46c-230">Můžete to obejít tak, že pomocí `Key` atributu zadáte jiný název vlastnosti MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2f46c-230">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="2f46c-231">Další informace najdete [v dokumentaci k MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="2f46c-231">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="2f46c-232">DateTime. Kind se nezachová při serializaci nebo deserializaci.</span><span class="sxs-lookup"><span data-stu-id="2f46c-232">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="2f46c-233">Protokol MessagePack neposkytuje způsob, jak zakódovat `Kind` hodnotu `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="2f46c-233">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="2f46c-234">Výsledkem je, že při deserializaci data MessagePack hub Protocol předpokládá, že příchozí datum je ve formátu UTC.</span><span class="sxs-lookup"><span data-stu-id="2f46c-234">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="2f46c-235">Pokud pracujete s `DateTime` hodnotami v místním čase, doporučujeme před odesláním převést na čas UTC.</span><span class="sxs-lookup"><span data-stu-id="2f46c-235">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="2f46c-236">Převeďte je ze standardu UTC na místní čas, když je přijmete.</span><span class="sxs-lookup"><span data-stu-id="2f46c-236">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="2f46c-237">Další informace o tomto omezení najdete v tématu problém GitHubu [ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="2f46c-237">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="2f46c-238">DateTime. MinValue není podporován MessagePack v JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="2f46c-238">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="2f46c-239">Knihovna [msgpack5](https://github.com/mcollina/msgpack5) , kterou používá SignalR klient JavaScriptu, nepodporuje `timestamp96` typ v MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2f46c-239">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="2f46c-240">Tento typ se používá ke kódování velmi velkých hodnot kalendářních dat (zcela brzy v minulosti nebo velmi daleko v budoucnosti).</span><span class="sxs-lookup"><span data-stu-id="2f46c-240">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="2f46c-241">Hodnota `DateTime.MinValue` je `January 1, 0001` , která musí být kódována v `timestamp96` hodnotě.</span><span class="sxs-lookup"><span data-stu-id="2f46c-241">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="2f46c-242">Z tohoto důvodu se odeslání `DateTime.MinValue` do klienta jazyka JavaScript nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="2f46c-242">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="2f46c-243">Když `DateTime.MinValue` je klient jazyka JavaScript přijat, je vyvolána následující chyba:</span><span class="sxs-lookup"><span data-stu-id="2f46c-243">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="2f46c-244">Obvykle `DateTime.MinValue` se používá ke kódování "chybějící" nebo " `null` hodnoty".</span><span class="sxs-lookup"><span data-stu-id="2f46c-244">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="2f46c-245">Pokud potřebujete tuto hodnotu v MessagePack zakódovat, použijte `DateTime` hodnotu Nullable ( `DateTime?` ) nebo zakódovat samostatnou `bool` hodnotu označující, jestli je datum k dispozici.</span><span class="sxs-lookup"><span data-stu-id="2f46c-245">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="2f46c-246">Další informace o tomto omezení najdete v tématu problém GitHubu [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="2f46c-246">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="2f46c-247">Podpora MessagePack v prostředí kompilace "před a za běhu"</span><span class="sxs-lookup"><span data-stu-id="2f46c-247">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="2f46c-248">Knihovna [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) , kterou používá klient a Server .NET, používá generování kódu k optimalizaci serializace.</span><span class="sxs-lookup"><span data-stu-id="2f46c-248">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="2f46c-249">V důsledku toho není podporován ve výchozím nastavení v prostředích, která používají "předdobu" kompilace (například Xamarin iOS nebo Unity).</span><span class="sxs-lookup"><span data-stu-id="2f46c-249">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="2f46c-250">V těchto prostředích je možné používat MessagePack pomocí "předběžného generování" kódu serializátoru/deserializace.</span><span class="sxs-lookup"><span data-stu-id="2f46c-250">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="2f46c-251">Další informace najdete [v dokumentaci k MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="2f46c-251">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="2f46c-252">Jakmile jste předem vygenerovali serializátory, můžete je zaregistrovat pomocí delegáta konfigurace předaného `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="2f46c-252">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="2f46c-253">Kontroly typů jsou v MessagePack přísnější.</span><span class="sxs-lookup"><span data-stu-id="2f46c-253">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="2f46c-254">Protokol centra JSON provede převody typů během deserializace.</span><span class="sxs-lookup"><span data-stu-id="2f46c-254">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="2f46c-255">Například pokud má příchozí objekt hodnotu vlastnosti, která je číslo ( `{ foo: 42 }` ), ale vlastnost třídy .NET je typu `string` , hodnota bude převedena.</span><span class="sxs-lookup"><span data-stu-id="2f46c-255">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="2f46c-256">MessagePack však tento převod neprovede a vyvolá výjimku, kterou lze zobrazit v protokolech na straně serveru (a v konzole nástroje):</span><span class="sxs-lookup"><span data-stu-id="2f46c-256">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="2f46c-257">Další informace o tomto omezení najdete v tématu problém GitHubu [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="2f46c-257">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="2f46c-258">Související prostředky</span><span class="sxs-lookup"><span data-stu-id="2f46c-258">Related resources</span></span>

* [<span data-ttu-id="2f46c-259">Začínáme</span><span class="sxs-lookup"><span data-stu-id="2f46c-259">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="2f46c-260">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="2f46c-260">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="2f46c-261">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="2f46c-261">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2f46c-262">Tento článek předpokládá, že čtenář je obeznámen s tématy popsanými v tématu [Začínáme](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="2f46c-262">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="2f46c-263">Co je MessagePack?</span><span class="sxs-lookup"><span data-stu-id="2f46c-263">What is MessagePack?</span></span>

<span data-ttu-id="2f46c-264">[MessagePack](https://msgpack.org/index.html) je rychlý a kompaktní binární formát serializace.</span><span class="sxs-lookup"><span data-stu-id="2f46c-264">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="2f46c-265">To je užitečné v případě, že je důležité mít vliv na výkon a šířku pásma, protože vytváří menší zprávy ve srovnání se [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="2f46c-265">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="2f46c-266">Binární zprávy jsou nečitelný při prohlížení síťových trasování a protokolů, pokud nejsou bajty předávány pomocí MessagePack analyzátoru.</span><span class="sxs-lookup"><span data-stu-id="2f46c-266">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="2f46c-267">SignalR má vestavěnou podporu pro formát MessagePack a poskytuje rozhraní API pro použití klientem a serverem.</span><span class="sxs-lookup"><span data-stu-id="2f46c-267">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="2f46c-268">Konfigurace MessagePack na serveru</span><span class="sxs-lookup"><span data-stu-id="2f46c-268">Configure MessagePack on the server</span></span>

<span data-ttu-id="2f46c-269">Pokud chcete na serveru povolit protokol MessagePack hub, nainstalujte `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` balíček do své aplikace.</span><span class="sxs-lookup"><span data-stu-id="2f46c-269">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="2f46c-270">V `Startup.ConfigureServices` metodě přidejte `AddMessagePackProtocol` do `AddSignalR` volání, aby bylo možné povolit podporu MessagePack na serveru.</span><span class="sxs-lookup"><span data-stu-id="2f46c-270">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="2f46c-271">Ve výchozím nastavení je povolený formát JSON.</span><span class="sxs-lookup"><span data-stu-id="2f46c-271">JSON is enabled by default.</span></span> <span data-ttu-id="2f46c-272">Přidání MessagePack umožňuje podporu pro klienty JSON i MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2f46c-272">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="2f46c-273">Pokud chcete přizpůsobit způsob, jakým MessagePack data naformátují, `AddMessagePackProtocol` převezme delegáta konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="2f46c-273">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="2f46c-274">V tomto delegátu `FormatterResolvers` lze vlastnost použít ke konfiguraci možností serializace MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2f46c-274">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="2f46c-275">Další informace o tom, jak překladače fungují, najdete v knihovně MessagePack na adrese [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="2f46c-275">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="2f46c-276">Atributy lze použít pro objekty, které chcete serializovat, chcete-li definovat, jak by měly být zpracovány.</span><span class="sxs-lookup"><span data-stu-id="2f46c-276">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="2f46c-277">Důrazně doporučujeme zkontrolovat [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) a použít Doporučené opravy.</span><span class="sxs-lookup"><span data-stu-id="2f46c-277">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="2f46c-278">Například nastavení `MessagePackSecurity.Active` statické vlastnosti na `MessagePackSecurity.UntrustedData` .</span><span class="sxs-lookup"><span data-stu-id="2f46c-278">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="2f46c-279">Nastavení `MessagePackSecurity.Active` vyžaduje ruční instalaci [verze 1.9. x MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="2f46c-279">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="2f46c-280">Instalace `MessagePack` aktualizací 1.9. x používá verzi SignalR .</span><span class="sxs-lookup"><span data-stu-id="2f46c-280">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="2f46c-281">Když není `MessagePackSecurity.Active` nastavená na `MessagePackSecurity.UntrustedData` , škodlivý klient může způsobit odepření služby.</span><span class="sxs-lookup"><span data-stu-id="2f46c-281">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="2f46c-282">Nastavte `MessagePackSecurity.Active` v `Program.Main` , jak je znázorněno v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="2f46c-282">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
using MessagePack;

public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="2f46c-283">Konfigurace MessagePack na klientovi</span><span class="sxs-lookup"><span data-stu-id="2f46c-283">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="2f46c-284">Formát JSON je ve výchozím nastavení povolen pro podporované klienty.</span><span class="sxs-lookup"><span data-stu-id="2f46c-284">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="2f46c-285">Klienti můžou podporovat jenom jeden protokol.</span><span class="sxs-lookup"><span data-stu-id="2f46c-285">Clients can only support a single protocol.</span></span> <span data-ttu-id="2f46c-286">Přidání podpory MessagePack nahradí všechny dříve nakonfigurované protokoly.</span><span class="sxs-lookup"><span data-stu-id="2f46c-286">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="2f46c-287">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="2f46c-287">.NET client</span></span>

<span data-ttu-id="2f46c-288">Pokud chcete povolit MessagePack v klientovi .NET, nainstalujte `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` balíček a zavolejte `AddMessagePackProtocol` na `HubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="2f46c-288">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
using Microsoft.AspNetCore.SignalR.Client;
using Microsoft.Extensions.DependencyInjection;

var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chathub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="2f46c-289">Toto `AddMessagePackProtocol` volání přebírá delegáta pro konfiguraci možností, jako je server.</span><span class="sxs-lookup"><span data-stu-id="2f46c-289">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="2f46c-290">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="2f46c-290">JavaScript client</span></span>

<span data-ttu-id="2f46c-291">Podpora MessagePack pro klienta JavaScriptu je poskytována [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) balíčkem npm.</span><span class="sxs-lookup"><span data-stu-id="2f46c-291">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="2f46c-292">Nainstalujte balíček spuštěním následujícího příkazu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="2f46c-292">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="2f46c-293">Po instalaci balíčku npm lze modul použít přímo pomocí zavaděče modulu JavaScript nebo importovat do prohlížeče odkazem na následující soubor:</span><span class="sxs-lookup"><span data-stu-id="2f46c-293">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="2f46c-294">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="2f46c-294">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="2f46c-295">V prohlížeči `msgpack5` musí být také odkazováno na knihovnu.</span><span class="sxs-lookup"><span data-stu-id="2f46c-295">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="2f46c-296">`<script>`K vytvoření odkazu použijte značku.</span><span class="sxs-lookup"><span data-stu-id="2f46c-296">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="2f46c-297">Knihovnu najdete na adrese *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="2f46c-297">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="2f46c-298">Při použití `<script>` elementu je pořadí důležité.</span><span class="sxs-lookup"><span data-stu-id="2f46c-298">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="2f46c-299">Pokud je před *msgpack5.js*odkazováno na *signalr-protocol-msgpack.js* , dojde k chybě při pokusu o připojení k MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2f46c-299">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="2f46c-300">*signalr.js* se také vyžaduje před *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="2f46c-300">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="2f46c-301">`.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())`Při přidání do `HubConnectionBuilder` nakonfiguruje klient používání protokolu MessagePack při připojování k serveru.</span><span class="sxs-lookup"><span data-stu-id="2f46c-301">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="2f46c-302">V tuto chvíli nejsou k dispozici žádné možnosti konfigurace pro protokol MessagePack pro klienta jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2f46c-302">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="2f46c-303">MessagePack adaptivní</span><span class="sxs-lookup"><span data-stu-id="2f46c-303">MessagePack quirks</span></span>

<span data-ttu-id="2f46c-304">Při používání protokolu centra MessagePack je potřeba vědět o několika problémech.</span><span class="sxs-lookup"><span data-stu-id="2f46c-304">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="2f46c-305">MessagePack rozlišuje velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="2f46c-305">MessagePack is case-sensitive</span></span>

<span data-ttu-id="2f46c-306">Protokol MessagePack rozlišuje velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="2f46c-306">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="2f46c-307">Zvažte například následující třídu jazyka C#:</span><span class="sxs-lookup"><span data-stu-id="2f46c-307">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="2f46c-308">Při odesílání z klienta jazyka JavaScript je nutné použít `PascalCased` názvy vlastností, protože velikost písmen se musí přesně shodovat s třídou jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="2f46c-308">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="2f46c-309">Příklad:</span><span class="sxs-lookup"><span data-stu-id="2f46c-309">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="2f46c-310">Použití `camelCased` názvů nebude správně svázáno se třídou jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="2f46c-310">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="2f46c-311">Můžete to obejít tak, že pomocí `Key` atributu zadáte jiný název vlastnosti MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2f46c-311">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="2f46c-312">Další informace najdete [v dokumentaci k MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="2f46c-312">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="2f46c-313">DateTime. Kind se nezachová při serializaci nebo deserializaci.</span><span class="sxs-lookup"><span data-stu-id="2f46c-313">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="2f46c-314">Protokol MessagePack neposkytuje způsob, jak zakódovat `Kind` hodnotu `DateTime` .</span><span class="sxs-lookup"><span data-stu-id="2f46c-314">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="2f46c-315">Výsledkem je, že při deserializaci data MessagePack hub Protocol předpokládá, že příchozí datum je ve formátu UTC.</span><span class="sxs-lookup"><span data-stu-id="2f46c-315">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="2f46c-316">Pokud pracujete s `DateTime` hodnotami v místním čase, doporučujeme před odesláním převést na čas UTC.</span><span class="sxs-lookup"><span data-stu-id="2f46c-316">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="2f46c-317">Převeďte je ze standardu UTC na místní čas, když je přijmete.</span><span class="sxs-lookup"><span data-stu-id="2f46c-317">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="2f46c-318">Další informace o tomto omezení najdete v tématu problém GitHubu [ASPNET/ SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="2f46c-318">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="2f46c-319">DateTime. MinValue není podporován MessagePack v JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="2f46c-319">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="2f46c-320">Knihovna [msgpack5](https://github.com/mcollina/msgpack5) , kterou používá SignalR klient JavaScriptu, nepodporuje `timestamp96` typ v MessagePack.</span><span class="sxs-lookup"><span data-stu-id="2f46c-320">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="2f46c-321">Tento typ se používá ke kódování velmi velkých hodnot kalendářních dat (zcela brzy v minulosti nebo velmi daleko v budoucnosti).</span><span class="sxs-lookup"><span data-stu-id="2f46c-321">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="2f46c-322">Hodnota je, `DateTime.MinValue` `January 1, 0001` která musí být kódována v `timestamp96` hodnotě.</span><span class="sxs-lookup"><span data-stu-id="2f46c-322">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="2f46c-323">Z tohoto důvodu se odeslání `DateTime.MinValue` do klienta jazyka JavaScript nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="2f46c-323">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="2f46c-324">Když `DateTime.MinValue` je klient jazyka JavaScript přijat, je vyvolána následující chyba:</span><span class="sxs-lookup"><span data-stu-id="2f46c-324">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="2f46c-325">Obvykle `DateTime.MinValue` se používá ke kódování "chybějící" nebo " `null` hodnoty".</span><span class="sxs-lookup"><span data-stu-id="2f46c-325">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="2f46c-326">Pokud potřebujete tuto hodnotu v MessagePack zakódovat, použijte `DateTime` hodnotu Nullable ( `DateTime?` ) nebo zakódovat samostatnou `bool` hodnotu označující, jestli je datum k dispozici.</span><span class="sxs-lookup"><span data-stu-id="2f46c-326">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="2f46c-327">Další informace o tomto omezení najdete v tématu problém GitHubu [ASPNET/ SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="2f46c-327">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="2f46c-328">Podpora MessagePack v prostředí kompilace "před a za běhu"</span><span class="sxs-lookup"><span data-stu-id="2f46c-328">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="2f46c-329">Knihovna [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) , kterou používá klient a Server .NET, používá generování kódu k optimalizaci serializace.</span><span class="sxs-lookup"><span data-stu-id="2f46c-329">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="2f46c-330">V důsledku toho není podporován ve výchozím nastavení v prostředích, která používají "předdobu" kompilace (například Xamarin iOS nebo Unity).</span><span class="sxs-lookup"><span data-stu-id="2f46c-330">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="2f46c-331">V těchto prostředích je možné používat MessagePack pomocí "předběžného generování" kódu serializátoru/deserializace.</span><span class="sxs-lookup"><span data-stu-id="2f46c-331">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="2f46c-332">Další informace najdete [v dokumentaci k MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="2f46c-332">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="2f46c-333">Jakmile jste předem vygenerovali serializátory, můžete je zaregistrovat pomocí delegáta konfigurace předaného `AddMessagePackProtocol` :</span><span class="sxs-lookup"><span data-stu-id="2f46c-333">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="2f46c-334">Kontroly typů jsou v MessagePack přísnější.</span><span class="sxs-lookup"><span data-stu-id="2f46c-334">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="2f46c-335">Protokol centra JSON provede převody typů během deserializace.</span><span class="sxs-lookup"><span data-stu-id="2f46c-335">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="2f46c-336">Například pokud má příchozí objekt hodnotu vlastnosti, která je číslo ( `{ foo: 42 }` ), ale vlastnost třídy .NET je typu `string` , hodnota bude převedena.</span><span class="sxs-lookup"><span data-stu-id="2f46c-336">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="2f46c-337">MessagePack však tento převod neprovede a vyvolá výjimku, kterou lze zobrazit v protokolech na straně serveru (a v konzole nástroje):</span><span class="sxs-lookup"><span data-stu-id="2f46c-337">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="2f46c-338">Další informace o tomto omezení najdete v tématu problém GitHubu [ASPNET/ SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="2f46c-338">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="2f46c-339">Související prostředky</span><span class="sxs-lookup"><span data-stu-id="2f46c-339">Related resources</span></span>

* [<span data-ttu-id="2f46c-340">Začínáme</span><span class="sxs-lookup"><span data-stu-id="2f46c-340">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="2f46c-341">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="2f46c-341">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="2f46c-342">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="2f46c-342">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
