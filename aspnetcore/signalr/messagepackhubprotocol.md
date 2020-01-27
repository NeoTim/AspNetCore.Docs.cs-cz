---
title: Použijte protokol MessagePack hub v SignalR pro ASP.NET Core
author: bradygaster
description: Přidejte protokol MessagePack hub pro ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: 1b01357233a9b95a5da052d92e30232c94e78a78
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76727220"
---
# <a name="use-messagepack-hub-protocol-in-opno-locsignalr-for-aspnet-core"></a><span data-ttu-id="297f1-103">Použijte protokol MessagePack hub v SignalR pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="297f1-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

<span data-ttu-id="297f1-104">Od [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="297f1-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

<span data-ttu-id="297f1-105">Tento článek předpokládá, že čtenář je obeznámen s tématy popsanými v tématu [Začínáme](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="297f1-105">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="297f1-106">Co je MessagePack?</span><span class="sxs-lookup"><span data-stu-id="297f1-106">What is MessagePack?</span></span>

<span data-ttu-id="297f1-107">[MessagePack](https://msgpack.org/index.html) je binární Serializační formát, který je rychlý a kompaktní.</span><span class="sxs-lookup"><span data-stu-id="297f1-107">[MessagePack](https://msgpack.org/index.html) is a binary serialization format that is fast and compact.</span></span> <span data-ttu-id="297f1-108">To je užitečné v případě, že je důležité mít vliv na výkon a šířku pásma, protože vytváří menší zprávy ve srovnání se [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="297f1-108">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="297f1-109">Vzhledem k tomu, že se jedná o binární formát, zprávy jsou nečitelný při prohlížení síťových trasování a protokolů, pokud nejsou bajty předávány pomocí MessagePack analyzátoru.</span><span class="sxs-lookup"><span data-stu-id="297f1-109">Because it's a binary format, messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="297f1-110"> má integrovanou podporu pro formát MessagePack a poskytuje rozhraní API pro použití klientem a serverem.</span><span class="sxs-lookup"><span data-stu-id="297f1-110"> has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="297f1-111">Konfigurace MessagePack na serveru</span><span class="sxs-lookup"><span data-stu-id="297f1-111">Configure MessagePack on the server</span></span>

<span data-ttu-id="297f1-112">Pokud chcete na serveru povolit protokol MessagePack hub, nainstalujte do své aplikace balíček `Microsoft.AspNetCore.SignalR.Protocols.MessagePack`.</span><span class="sxs-lookup"><span data-stu-id="297f1-112">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="297f1-113">Do souboru Startup.cs přidejte `AddMessagePackProtocol` volání `AddSignalR` a povolte podporu MessagePack na serveru.</span><span class="sxs-lookup"><span data-stu-id="297f1-113">In the Startup.cs file add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="297f1-114">Ve výchozím nastavení je povolený formát JSON.</span><span class="sxs-lookup"><span data-stu-id="297f1-114">JSON is enabled by default.</span></span> <span data-ttu-id="297f1-115">Přidání MessagePack umožňuje podporu pro klienty JSON i MessagePack.</span><span class="sxs-lookup"><span data-stu-id="297f1-115">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="297f1-116">Pokud chcete přizpůsobit způsob, jakým aplikace MessagePack naformátuje data, `AddMessagePackProtocol` převezme delegáta pro konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="297f1-116">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="297f1-117">V tomto delegátu lze vlastnost `FormatterResolvers` použít ke konfiguraci možností serializace MessagePack.</span><span class="sxs-lookup"><span data-stu-id="297f1-117">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="297f1-118">Další informace o tom, jak překladače fungují, najdete v knihovně MessagePack na adrese [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="297f1-118">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="297f1-119">Atributy lze použít pro objekty, které chcete serializovat, chcete-li definovat, jak by měly být zpracovány.</span><span class="sxs-lookup"><span data-stu-id="297f1-119">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="297f1-120">Konfigurace MessagePack na klientovi</span><span class="sxs-lookup"><span data-stu-id="297f1-120">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="297f1-121">Formát JSON je ve výchozím nastavení povolen pro podporované klienty.</span><span class="sxs-lookup"><span data-stu-id="297f1-121">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="297f1-122">Klienti můžou podporovat jenom jeden protokol.</span><span class="sxs-lookup"><span data-stu-id="297f1-122">Clients can only support a single protocol.</span></span> <span data-ttu-id="297f1-123">Přidání podpory MessagePack nahradí všechny dříve nakonfigurované protokoly.</span><span class="sxs-lookup"><span data-stu-id="297f1-123">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="297f1-124">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="297f1-124">.NET client</span></span>

<span data-ttu-id="297f1-125">Pokud chcete povolit MessagePack v klientovi .NET, nainstalujte balíček `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` a zavolejte `AddMessagePackProtocol` na `HubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="297f1-125">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="297f1-126">Toto `AddMessagePackProtocol` volání přebírá delegáta pro konfiguraci možností, jako je server.</span><span class="sxs-lookup"><span data-stu-id="297f1-126">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="297f1-127">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="297f1-127">JavaScript client</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="297f1-128">Podpora MessagePack pro klienta JavaScriptu je poskytována balíčkem `@microsoft/signalr-protocol-msgpack` npm.</span><span class="sxs-lookup"><span data-stu-id="297f1-128">MessagePack support for the JavaScript client is provided by the `@microsoft/signalr-protocol-msgpack` npm package.</span></span> <span data-ttu-id="297f1-129">Nainstalujte balíček spuštěním následujícího příkazu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="297f1-129">Install the package by executing the following command in a command shell:</span></span>

```console
npm install @microsoft/signalr-protocol-msgpack
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="297f1-130">Podpora MessagePack pro klienta JavaScriptu je poskytována balíčkem `@aspnet/signalr-protocol-msgpack` npm.</span><span class="sxs-lookup"><span data-stu-id="297f1-130">MessagePack support for the JavaScript client is provided by the `@aspnet/signalr-protocol-msgpack` npm package.</span></span> <span data-ttu-id="297f1-131">Nainstalujte balíček spuštěním následujícího příkazu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="297f1-131">Install the package by executing the following command in a command shell:</span></span>

```console
npm install @aspnet/signalr-protocol-msgpack
```

::: moniker-end

<span data-ttu-id="297f1-132">Po instalaci balíčku npm lze modul použít přímo pomocí zavaděče modulu JavaScript nebo importovat do prohlížeče odkazem na následující soubor:</span><span class="sxs-lookup"><span data-stu-id="297f1-132">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="297f1-133">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="297f1-133">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="297f1-134">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="297f1-134">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

::: moniker-end

<span data-ttu-id="297f1-135">V prohlížeči musí být také odkazována na knihovnu `msgpack5`.</span><span class="sxs-lookup"><span data-stu-id="297f1-135">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="297f1-136">Použijte značku `<script>` k vytvoření odkazu.</span><span class="sxs-lookup"><span data-stu-id="297f1-136">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="297f1-137">Knihovnu najdete na adrese *node_modules \msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="297f1-137">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="297f1-138">Při použití prvku `<script>` je pořadí důležité.</span><span class="sxs-lookup"><span data-stu-id="297f1-138">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="297f1-139">Pokud se na *SignalR-Protocol-msgpack. js* odkazuje před *msgpack5. js*, při pokusu o připojení pomocí MessagePack dojde k chybě.</span><span class="sxs-lookup"><span data-stu-id="297f1-139">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="297f1-140">*návěstí. js* je také vyžadováno před *SignalR-Protocol-msgpack. js*.</span><span class="sxs-lookup"><span data-stu-id="297f1-140">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="297f1-141">Přidání `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` do `HubConnectionBuilder` nakonfiguruje klienta tak, aby používal protokol MessagePack při připojování k serveru.</span><span class="sxs-lookup"><span data-stu-id="297f1-141">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="297f1-142">V tuto chvíli nejsou k dispozici žádné možnosti konfigurace pro protokol MessagePack pro klienta jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="297f1-142">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="297f1-143">MessagePack adaptivní</span><span class="sxs-lookup"><span data-stu-id="297f1-143">MessagePack quirks</span></span>

<span data-ttu-id="297f1-144">Při používání protokolu centra MessagePack je potřeba vědět o několika problémech.</span><span class="sxs-lookup"><span data-stu-id="297f1-144">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="297f1-145">MessagePack rozlišuje velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="297f1-145">MessagePack is case-sensitive</span></span>

<span data-ttu-id="297f1-146">Protokol MessagePack rozlišuje velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="297f1-146">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="297f1-147">Zvažte například následující C# třídu:</span><span class="sxs-lookup"><span data-stu-id="297f1-147">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="297f1-148">Při odesílání z klienta jazyka JavaScript je nutné použít `PascalCased` názvů vlastností, protože velikost písmen se musí přesně shodovat C# s třídou.</span><span class="sxs-lookup"><span data-stu-id="297f1-148">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="297f1-149">Příklad:</span><span class="sxs-lookup"><span data-stu-id="297f1-149">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="297f1-150">Použití názvů `camelCased` se nebude správně navazovat C# na třídu.</span><span class="sxs-lookup"><span data-stu-id="297f1-150">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="297f1-151">Můžete to obejít pomocí atributu `Key` a zadat jiný název pro vlastnost MessagePack.</span><span class="sxs-lookup"><span data-stu-id="297f1-151">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="297f1-152">Další informace najdete [v dokumentaci k MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="297f1-152">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="297f1-153">DateTime. Kind se nezachová při serializaci nebo deserializaci.</span><span class="sxs-lookup"><span data-stu-id="297f1-153">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="297f1-154">Protokol MessagePack neposkytuje způsob, jak zakódovat `Kind` hodnotu `DateTime`.</span><span class="sxs-lookup"><span data-stu-id="297f1-154">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="297f1-155">Výsledkem je, že při deserializaci data MessagePack hub Protocol předpokládá, že příchozí datum je ve formátu UTC.</span><span class="sxs-lookup"><span data-stu-id="297f1-155">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="297f1-156">Pokud pracujete s `DateTime` hodnotami v místním čase, doporučujeme před odesláním převést na čas UTC.</span><span class="sxs-lookup"><span data-stu-id="297f1-156">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="297f1-157">Převeďte je ze standardu UTC na místní čas, když je přijmete.</span><span class="sxs-lookup"><span data-stu-id="297f1-157">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="297f1-158">Další informace o tomto omezení najdete v tématu věnovaném problému GitHubu [ASPNET/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="297f1-158">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="297f1-159">DateTime. MinValue není podporován MessagePack v JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="297f1-159">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="297f1-160">Knihovna [msgpack5](https://github.com/mcollina/msgpack5) používaná klientem SignalR JavaScript nepodporuje typ `timestamp96` v MessagePack.</span><span class="sxs-lookup"><span data-stu-id="297f1-160">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="297f1-161">Tento typ se používá ke kódování velmi velkých hodnot kalendářních dat (zcela brzy v minulosti nebo velmi daleko v budoucnosti).</span><span class="sxs-lookup"><span data-stu-id="297f1-161">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="297f1-162">Hodnota `DateTime.MinValue` je `January 1, 0001`, která musí být kódována v `timestamp96` hodnotě.</span><span class="sxs-lookup"><span data-stu-id="297f1-162">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="297f1-163">Z tohoto důvodu se odeslání `DateTime.MinValue` do klienta jazyka JavaScript nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="297f1-163">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="297f1-164">Když klient jazyka JavaScript obdrží `DateTime.MinValue`, je vyvolána následující chyba:</span><span class="sxs-lookup"><span data-stu-id="297f1-164">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="297f1-165">K zakódování chybějící nebo `null`é hodnoty se obvykle používá `DateTime.MinValue`.</span><span class="sxs-lookup"><span data-stu-id="297f1-165">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="297f1-166">Pokud potřebujete tuto hodnotu v MessagePack kódovat, použijte `DateTime` hodnotu s možnou hodnotou null (`DateTime?`) nebo zakódováním samostatné `bool` hodnoty, která označuje, jestli je k dispozici datum.</span><span class="sxs-lookup"><span data-stu-id="297f1-166">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="297f1-167">Další informace o tomto omezení najdete v tématu věnovaném problému GitHubu [ASPNET/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="297f1-167">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="297f1-168">Podpora MessagePack v prostředí kompilace "před a za běhu"</span><span class="sxs-lookup"><span data-stu-id="297f1-168">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="297f1-169">Knihovna [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8) , kterou používá klient a Server .NET, používá generování kódu k optimalizaci serializace.</span><span class="sxs-lookup"><span data-stu-id="297f1-169">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="297f1-170">V důsledku toho není podporován ve výchozím nastavení v prostředích, která používají "předdobu" kompilace (například Xamarin iOS nebo Unity).</span><span class="sxs-lookup"><span data-stu-id="297f1-170">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="297f1-171">V těchto prostředích je možné používat MessagePack pomocí "předběžného generování" kódu serializátoru/deserializace.</span><span class="sxs-lookup"><span data-stu-id="297f1-171">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="297f1-172">Další informace najdete [v dokumentaci k MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="297f1-172">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="297f1-173">Jakmile jste předem vygenerovali serializátory, můžete je zaregistrovat pomocí delegáta konfigurace předaného `AddMessagePackProtocol`:</span><span class="sxs-lookup"><span data-stu-id="297f1-173">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="297f1-174">Kontroly typů jsou v MessagePack přísnější.</span><span class="sxs-lookup"><span data-stu-id="297f1-174">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="297f1-175">Protokol centra JSON provede převody typů během deserializace.</span><span class="sxs-lookup"><span data-stu-id="297f1-175">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="297f1-176">Například pokud má příchozí objekt hodnotu vlastnosti, která je číslo (`{ foo: 42 }`), ale vlastnost třídy .NET je typu `string`, hodnota bude převedena.</span><span class="sxs-lookup"><span data-stu-id="297f1-176">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="297f1-177">MessagePack však tento převod neprovede a vyvolá výjimku, kterou lze zobrazit v protokolech na straně serveru (a v konzole nástroje):</span><span class="sxs-lookup"><span data-stu-id="297f1-177">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="297f1-178">Další informace o tomto omezení najdete v tématu věnovaném problému GitHubu [ASPNET/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="297f1-178">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="297f1-179">Související prostředky</span><span class="sxs-lookup"><span data-stu-id="297f1-179">Related resources</span></span>

* [<span data-ttu-id="297f1-180">Začínáme</span><span class="sxs-lookup"><span data-stu-id="297f1-180">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="297f1-181">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="297f1-181">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="297f1-182">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="297f1-182">JavaScript client</span></span>](xref:signalr/javascript-client)
