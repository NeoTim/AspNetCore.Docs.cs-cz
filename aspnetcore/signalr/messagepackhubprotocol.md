---
title: Použití protokolu Centra SignalR MessagePack pro ASP.NET core
author: bradygaster
description: Přidejte protokol MessagePack SignalRHub do ASP.NET core .
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/13/2020
no-loc:
- SignalR
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: bbc34d790387a96bb3b6f75e841b45685eb137ce
ms.sourcegitcommit: 5af16166977da598953f82da3ed3b7712d38f6cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81277233"
---
# <a name="use-messagepack-hub-protocol-in-opno-locsignalr-for-aspnet-core"></a><span data-ttu-id="d16f6-103">Použití protokolu Centra SignalR MessagePack pro ASP.NET core</span><span class="sxs-lookup"><span data-stu-id="d16f6-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="d16f6-104">Tento článek předpokládá, že čtenář je obeznámen s tématy, na které se vztahuje [v Začínáme](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="d16f6-104">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="d16f6-105">Co je MessagePack?</span><span class="sxs-lookup"><span data-stu-id="d16f6-105">What is MessagePack?</span></span>

<span data-ttu-id="d16f6-106">[MessagePack](https://msgpack.org/index.html) je rychlý a kompaktní binární serializace formátu.</span><span class="sxs-lookup"><span data-stu-id="d16f6-106">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="d16f6-107">Je to užitečné, když výkon a šířka pásma jsou problém, protože vytváří menší zprávy ve srovnání s [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="d16f6-107">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="d16f6-108">Binární zprávy jsou nečitelné při pohledu na trasování sítě a protokoly, pokud bajty jsou předány prostřednictvím analyzátormessagepack.</span><span class="sxs-lookup"><span data-stu-id="d16f6-108">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="d16f6-109">Má integrovanou podporu formátu MessagePack a poskytuje api pro klienta a server k použití.</span><span class="sxs-lookup"><span data-stu-id="d16f6-109"> has built-in support for the MessagePack format and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="d16f6-110">Konfigurace programu MessagePack na serveru</span><span class="sxs-lookup"><span data-stu-id="d16f6-110">Configure MessagePack on the server</span></span>

<span data-ttu-id="d16f6-111">Chcete-li povolit protokol MessagePack Hub `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` na serveru, nainstalujte balíček do aplikace.</span><span class="sxs-lookup"><span data-stu-id="d16f6-111">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="d16f6-112">V `Startup.ConfigureServices` metodě `AddMessagePackProtocol` přidejte `AddSignalR` do volání povolit messagepack podporu na serveru.</span><span class="sxs-lookup"><span data-stu-id="d16f6-112">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="d16f6-113">JSON je ve výchozím nastavení povolen.</span><span class="sxs-lookup"><span data-stu-id="d16f6-113">JSON is enabled by default.</span></span> <span data-ttu-id="d16f6-114">Přidání MessagePack umožňuje podporu pro klienty JSON i MessagePack.</span><span class="sxs-lookup"><span data-stu-id="d16f6-114">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="d16f6-115">Chcete-li přizpůsobit, jak MessagePack bude formátovat data, `AddMessagePackProtocol` trvá delegát pro konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="d16f6-115">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="d16f6-116">V tomto delegáta `SerializerOptions` vlastnost lze použít ke konfiguraci MessagePack možnosti serializace.</span><span class="sxs-lookup"><span data-stu-id="d16f6-116">In that delegate, the `SerializerOptions` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="d16f6-117">Další informace o tom, jak překladače fungují, naleznete v knihovně MessagePack na [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="d16f6-117">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="d16f6-118">Atributy lze použít na objekty, které chcete serializovat definovat, jak by měly být zpracovány.</span><span class="sxs-lookup"><span data-stu-id="d16f6-118">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="d16f6-119">Důrazně doporučujeme zkontrolovat [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) a použít doporučené opravy.</span><span class="sxs-lookup"><span data-stu-id="d16f6-119">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="d16f6-120">Například volání `.WithSecurity(MessagePackSecurity.UntrustedData)` při výměně `SerializerOptions`.</span><span class="sxs-lookup"><span data-stu-id="d16f6-120">For example, calling `.WithSecurity(MessagePackSecurity.UntrustedData)` when replacing the `SerializerOptions`.</span></span>

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="d16f6-121">Konfigurace balíčku MessagePack v klientovi</span><span class="sxs-lookup"><span data-stu-id="d16f6-121">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="d16f6-122">JSON je ve výchozím nastavení povolen pro podporované klienty.</span><span class="sxs-lookup"><span data-stu-id="d16f6-122">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="d16f6-123">Klienti mohou podporovat pouze jeden protokol.</span><span class="sxs-lookup"><span data-stu-id="d16f6-123">Clients can only support a single protocol.</span></span> <span data-ttu-id="d16f6-124">Přidání podpory messagepack nahradí všechny dříve nakonfigurované protokoly.</span><span class="sxs-lookup"><span data-stu-id="d16f6-124">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="d16f6-125">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="d16f6-125">.NET client</span></span>

<span data-ttu-id="d16f6-126">Chcete-li povolit messagepack v `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` klientovi `AddMessagePackProtocol` .NET, nainstalujte balíček a volejte na `HubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="d16f6-126">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="d16f6-127">Toto `AddMessagePackProtocol` volání trvá delegátpro konfiguraci možností, stejně jako server.</span><span class="sxs-lookup"><span data-stu-id="d16f6-127">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="d16f6-128">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="d16f6-128">JavaScript client</span></span>

<span data-ttu-id="d16f6-129">Podpora messagepacku pro klienta JavaScriptu je poskytována balíčkem [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm.</span><span class="sxs-lookup"><span data-stu-id="d16f6-129">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="d16f6-130">Nainstalujte balíček spuštěním následujícího příkazu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="d16f6-130">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="d16f6-131">Po instalaci balíčku npm lze modul použít přímo přes javascriptový zavaděč modulu nebo importovat do prohlížeče odkazem na následující soubor:</span><span class="sxs-lookup"><span data-stu-id="d16f6-131">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="d16f6-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="d16f6-132">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="d16f6-133">V prohlížeči `msgpack5` musí být také odkazováno na knihovnu.</span><span class="sxs-lookup"><span data-stu-id="d16f6-133">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="d16f6-134">K `<script>` vytvoření odkazu použijte značku.</span><span class="sxs-lookup"><span data-stu-id="d16f6-134">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="d16f6-135">Knihovnu naleznete na *adrese node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="d16f6-135">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="d16f6-136">Při použití `<script>` prvku je důležité pořadí.</span><span class="sxs-lookup"><span data-stu-id="d16f6-136">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="d16f6-137">Pokud *signalr-protocol-msgpack.js* odkazuje před *msgpack5.js*, dojde k chybě při pokusu o připojení s MessagePack.</span><span class="sxs-lookup"><span data-stu-id="d16f6-137">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="d16f6-138">*signalr.js* je také vyžadován před *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="d16f6-138">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="d16f6-139">Přidání `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` do `HubConnectionBuilder` programu nakonfiguruje klienta tak, aby při připojování k serveru používal protokol MessagePack.</span><span class="sxs-lookup"><span data-stu-id="d16f6-139">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="d16f6-140">V současné době neexistují žádné možnosti konfigurace pro messagepack protokol na klienta JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d16f6-140">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="d16f6-141">MessagePack vtípky</span><span class="sxs-lookup"><span data-stu-id="d16f6-141">MessagePack quirks</span></span>

<span data-ttu-id="d16f6-142">Existuje několik problémů, které je třeba vědět při použití messagepack hub protokolu.</span><span class="sxs-lookup"><span data-stu-id="d16f6-142">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="d16f6-143">MessagePack rozlišuje malá a velká písmena</span><span class="sxs-lookup"><span data-stu-id="d16f6-143">MessagePack is case-sensitive</span></span>

<span data-ttu-id="d16f6-144">MessagePack protokol je malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="d16f6-144">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="d16f6-145">Zvažte například následující třídu C#:</span><span class="sxs-lookup"><span data-stu-id="d16f6-145">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="d16f6-146">Při odesílání z klienta JavaScript, musíte použít `PascalCased` názvy vlastností, protože kryt musí přesně odpovídat c# třídy.</span><span class="sxs-lookup"><span data-stu-id="d16f6-146">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="d16f6-147">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d16f6-147">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="d16f6-148">Pomocí `camelCased` názvů nebude správně svázat na C# třídy.</span><span class="sxs-lookup"><span data-stu-id="d16f6-148">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="d16f6-149">Můžete tento problém obejít pomocí atributu `Key` k určení jiného názvu vlastnosti MessagePack.</span><span class="sxs-lookup"><span data-stu-id="d16f6-149">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="d16f6-150">Další informace naleznete [v dokumentaci MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="d16f6-150">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="d16f6-151">DateTime.Kind není zachována při serializaci/deserializaci</span><span class="sxs-lookup"><span data-stu-id="d16f6-151">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="d16f6-152">MessagePack protokol neposkytuje způsob, jak kódovat `Kind` hodnotu . `DateTime`</span><span class="sxs-lookup"><span data-stu-id="d16f6-152">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="d16f6-153">V důsledku toho při rekonstrukci data messagepack hub protokol převede do formátu `DateTime.Kind` `DateTimeKind.Local` UTC, pokud je jinak nebude dotýkat času a předat tak, jak je.</span><span class="sxs-lookup"><span data-stu-id="d16f6-153">As a result, when deserializing a date, the MessagePack Hub Protocol will convert to the UTC format if the `DateTime.Kind` is `DateTimeKind.Local` otherwise it will not touch the time and pass it as is.</span></span> <span data-ttu-id="d16f6-154">Pokud pracujete s `DateTime` hodnotami, doporučujeme před jejich odesláním převést na čas UTC.</span><span class="sxs-lookup"><span data-stu-id="d16f6-154">If you're working with `DateTime` values, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="d16f6-155">Převeďte je z Času UTC na místní čas, když je obdržíte.</span><span class="sxs-lookup"><span data-stu-id="d16f6-155">Convert them from UTC to local time when you receive them.</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="d16f6-156">DateTime.MinValue není podporovánmessagepack v Jazyce JavaScript</span><span class="sxs-lookup"><span data-stu-id="d16f6-156">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="d16f6-157">Knihovna [msgpack5](https://github.com/mcollina/msgpack5) používaná klientem SignalR Jazyka JavaScript nepodporuje typ v messagepacku. `timestamp96`</span><span class="sxs-lookup"><span data-stu-id="d16f6-157">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="d16f6-158">Tento typ se používá ke kódování velmi velké hodnoty kalendářních dat (buď velmi brzy v minulosti nebo velmi daleko v budoucnosti).</span><span class="sxs-lookup"><span data-stu-id="d16f6-158">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="d16f6-159">Hodnota `DateTime.MinValue` je `January 1, 0001`, která musí být zakódována v hodnotě. `timestamp96`</span><span class="sxs-lookup"><span data-stu-id="d16f6-159">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="d16f6-160">Z tohoto důvodu `DateTime.MinValue` není podporováno odesílání do klienta JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="d16f6-160">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="d16f6-161">Když `DateTime.MinValue` je přijat klientem JavaScript, je vyvolána následující chyba:</span><span class="sxs-lookup"><span data-stu-id="d16f6-161">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="d16f6-162">Obvykle `DateTime.MinValue` se používá ke kódování "chybějící" nebo `null` hodnota.</span><span class="sxs-lookup"><span data-stu-id="d16f6-162">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="d16f6-163">Pokud potřebujete kódovat tuto hodnotu v MessagePack,`DateTime?`použijte hodnotu s `bool` hodnotou `DateTime` null ( ) nebo zakódujte samostatnou hodnotu označující, zda je datum přítomno.</span><span class="sxs-lookup"><span data-stu-id="d16f6-163">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="d16f6-164">Další informace o tomto omezení naleznete v tématu GitHub problém [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="d16f6-164">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="d16f6-165">Podpora messagepacku v prostředí kompilace "ahead-of-time"</span><span class="sxs-lookup"><span data-stu-id="d16f6-165">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="d16f6-166">Knihovna [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) používaná klientem a serverem .NET používá generování kódu k optimalizaci serializace.</span><span class="sxs-lookup"><span data-stu-id="d16f6-166">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="d16f6-167">V důsledku toho není ve výchozím nastavení podporována v prostředích, která používají kompilaci "ahead-of-time" (například Xamarin iOS nebo Unity).</span><span class="sxs-lookup"><span data-stu-id="d16f6-167">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="d16f6-168">Je možné použít MessagePack v těchto prostředích "pre-generating" serializátor/deserializer kód.</span><span class="sxs-lookup"><span data-stu-id="d16f6-168">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="d16f6-169">Další informace naleznete [v dokumentaci MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span><span class="sxs-lookup"><span data-stu-id="d16f6-169">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v2.1.90#aot-code-generation-to-support-unityxamarin).</span></span> <span data-ttu-id="d16f6-170">Jakmile jste předem vygenerovali serializátory, můžete je `AddMessagePackProtocol`zaregistrovat pomocí delegáta konfigurace předaný :</span><span class="sxs-lookup"><span data-stu-id="d16f6-170">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="d16f6-171">Typ kontroly jsou přísnější v MessagePack</span><span class="sxs-lookup"><span data-stu-id="d16f6-171">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="d16f6-172">Protokol JSON Hub bude během deserializace provádět převody typů.</span><span class="sxs-lookup"><span data-stu-id="d16f6-172">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="d16f6-173">Například pokud příchozí objekt má hodnotu vlastnosti,`{ foo: 42 }`která je číslo ( ), ale `string`vlastnost na .NET třída je typu , hodnota bude převedena.</span><span class="sxs-lookup"><span data-stu-id="d16f6-173">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="d16f6-174">MessagePack však neprovede tento převod a vyvolá výjimku, která může být vidět v protokolech na straně serveru (a v konzoli):</span><span class="sxs-lookup"><span data-stu-id="d16f6-174">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="d16f6-175">Další informace o tomto omezení naleznete v tématu GitHub problém [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="d16f6-175">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="d16f6-176">Související prostředky</span><span class="sxs-lookup"><span data-stu-id="d16f6-176">Related resources</span></span>

* [<span data-ttu-id="d16f6-177">Začínáme</span><span class="sxs-lookup"><span data-stu-id="d16f6-177">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="d16f6-178">Klient rozhraní .NET</span><span class="sxs-lookup"><span data-stu-id="d16f6-178">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="d16f6-179">Javascriptový klient</span><span class="sxs-lookup"><span data-stu-id="d16f6-179">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

<span data-ttu-id="d16f6-180">Tento článek předpokládá, že čtenář je obeznámen s tématy, na které se vztahuje [v Začínáme](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="d16f6-180">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="d16f6-181">Co je MessagePack?</span><span class="sxs-lookup"><span data-stu-id="d16f6-181">What is MessagePack?</span></span>

<span data-ttu-id="d16f6-182">[MessagePack](https://msgpack.org/index.html) je rychlý a kompaktní binární serializace formátu.</span><span class="sxs-lookup"><span data-stu-id="d16f6-182">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="d16f6-183">Je to užitečné, když výkon a šířka pásma jsou problém, protože vytváří menší zprávy ve srovnání s [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="d16f6-183">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="d16f6-184">Binární zprávy jsou nečitelné při pohledu na trasování sítě a protokoly, pokud bajty jsou předány prostřednictvím analyzátormessagepack.</span><span class="sxs-lookup"><span data-stu-id="d16f6-184">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="d16f6-185">Má integrovanou podporu formátu MessagePack a poskytuje api pro klienta a server k použití.</span><span class="sxs-lookup"><span data-stu-id="d16f6-185"> has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="d16f6-186">Konfigurace programu MessagePack na serveru</span><span class="sxs-lookup"><span data-stu-id="d16f6-186">Configure MessagePack on the server</span></span>

<span data-ttu-id="d16f6-187">Chcete-li povolit protokol MessagePack Hub `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` na serveru, nainstalujte balíček do aplikace.</span><span class="sxs-lookup"><span data-stu-id="d16f6-187">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="d16f6-188">V `Startup.ConfigureServices` metodě `AddMessagePackProtocol` přidejte `AddSignalR` do volání povolit messagepack podporu na serveru.</span><span class="sxs-lookup"><span data-stu-id="d16f6-188">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="d16f6-189">JSON je ve výchozím nastavení povolen.</span><span class="sxs-lookup"><span data-stu-id="d16f6-189">JSON is enabled by default.</span></span> <span data-ttu-id="d16f6-190">Přidání MessagePack umožňuje podporu pro klienty JSON i MessagePack.</span><span class="sxs-lookup"><span data-stu-id="d16f6-190">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="d16f6-191">Chcete-li přizpůsobit, jak MessagePack bude formátovat data, `AddMessagePackProtocol` trvá delegát pro konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="d16f6-191">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="d16f6-192">V tomto delegáta `FormatterResolvers` vlastnost lze použít ke konfiguraci MessagePack možnosti serializace.</span><span class="sxs-lookup"><span data-stu-id="d16f6-192">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="d16f6-193">Další informace o tom, jak překladače fungují, naleznete v knihovně MessagePack na [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="d16f6-193">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="d16f6-194">Atributy lze použít na objekty, které chcete serializovat definovat, jak by měly být zpracovány.</span><span class="sxs-lookup"><span data-stu-id="d16f6-194">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="d16f6-195">Důrazně doporučujeme zkontrolovat [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) a použít doporučené opravy.</span><span class="sxs-lookup"><span data-stu-id="d16f6-195">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="d16f6-196">Například nastavení `MessagePackSecurity.Active` statické vlastnosti na `MessagePackSecurity.UntrustedData`.</span><span class="sxs-lookup"><span data-stu-id="d16f6-196">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="d16f6-197">Nastavení `MessagePackSecurity.Active` vyžaduje ruční instalaci [1.9.x verze MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="d16f6-197">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="d16f6-198">Instalace `MessagePack` verze 1.9.x SignalR upgraduje verzi.</span><span class="sxs-lookup"><span data-stu-id="d16f6-198">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="d16f6-199">Není-li `MessagePackSecurity.Active` nastavena možnost `MessagePackSecurity.UntrustedData`, může klient se zlými úmysly způsobit odmítnutí služby.</span><span class="sxs-lookup"><span data-stu-id="d16f6-199">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="d16f6-200">Set `MessagePackSecurity.Active` `Program.Main`in , jak je znázorněno v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="d16f6-200">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="d16f6-201">Konfigurace balíčku MessagePack v klientovi</span><span class="sxs-lookup"><span data-stu-id="d16f6-201">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="d16f6-202">JSON je ve výchozím nastavení povolen pro podporované klienty.</span><span class="sxs-lookup"><span data-stu-id="d16f6-202">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="d16f6-203">Klienti mohou podporovat pouze jeden protokol.</span><span class="sxs-lookup"><span data-stu-id="d16f6-203">Clients can only support a single protocol.</span></span> <span data-ttu-id="d16f6-204">Přidání podpory messagepack nahradí všechny dříve nakonfigurované protokoly.</span><span class="sxs-lookup"><span data-stu-id="d16f6-204">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="d16f6-205">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="d16f6-205">.NET client</span></span>

<span data-ttu-id="d16f6-206">Chcete-li povolit messagepack v `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` klientovi `AddMessagePackProtocol` .NET, nainstalujte balíček a volejte na `HubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="d16f6-206">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="d16f6-207">Toto `AddMessagePackProtocol` volání trvá delegátpro konfiguraci možností, stejně jako server.</span><span class="sxs-lookup"><span data-stu-id="d16f6-207">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="d16f6-208">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="d16f6-208">JavaScript client</span></span>

<span data-ttu-id="d16f6-209">Podpora messagepacku pro klienta JavaScriptu je poskytována balíčkem [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm.</span><span class="sxs-lookup"><span data-stu-id="d16f6-209">MessagePack support for the JavaScript client is provided by the [@microsoft/signalr-protocol-msgpack](https://www.npmjs.com/package/@microsoft/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="d16f6-210">Nainstalujte balíček spuštěním následujícího příkazu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="d16f6-210">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @microsoft/signalr-protocol-msgpack
```

<span data-ttu-id="d16f6-211">Po instalaci balíčku npm lze modul použít přímo přes javascriptový zavaděč modulu nebo importovat do prohlížeče odkazem na následující soubor:</span><span class="sxs-lookup"><span data-stu-id="d16f6-211">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="d16f6-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="d16f6-212">*node_modules\\@microsoft\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span> 

<span data-ttu-id="d16f6-213">V prohlížeči `msgpack5` musí být také odkazováno na knihovnu.</span><span class="sxs-lookup"><span data-stu-id="d16f6-213">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="d16f6-214">K `<script>` vytvoření odkazu použijte značku.</span><span class="sxs-lookup"><span data-stu-id="d16f6-214">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="d16f6-215">Knihovnu naleznete na *adrese node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="d16f6-215">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="d16f6-216">Při použití `<script>` prvku je důležité pořadí.</span><span class="sxs-lookup"><span data-stu-id="d16f6-216">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="d16f6-217">Pokud *signalr-protocol-msgpack.js* odkazuje před *msgpack5.js*, dojde k chybě při pokusu o připojení s MessagePack.</span><span class="sxs-lookup"><span data-stu-id="d16f6-217">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="d16f6-218">*signalr.js* je také vyžadován před *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="d16f6-218">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="d16f6-219">Přidání `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` do `HubConnectionBuilder` programu nakonfiguruje klienta tak, aby při připojování k serveru používal protokol MessagePack.</span><span class="sxs-lookup"><span data-stu-id="d16f6-219">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="d16f6-220">V současné době neexistují žádné možnosti konfigurace pro messagepack protokol na klienta JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d16f6-220">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="d16f6-221">MessagePack vtípky</span><span class="sxs-lookup"><span data-stu-id="d16f6-221">MessagePack quirks</span></span>

<span data-ttu-id="d16f6-222">Existuje několik problémů, které je třeba vědět při použití messagepack hub protokolu.</span><span class="sxs-lookup"><span data-stu-id="d16f6-222">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="d16f6-223">MessagePack rozlišuje malá a velká písmena</span><span class="sxs-lookup"><span data-stu-id="d16f6-223">MessagePack is case-sensitive</span></span>

<span data-ttu-id="d16f6-224">MessagePack protokol je malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="d16f6-224">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="d16f6-225">Zvažte například následující třídu C#:</span><span class="sxs-lookup"><span data-stu-id="d16f6-225">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="d16f6-226">Při odesílání z klienta JavaScript, musíte použít `PascalCased` názvy vlastností, protože kryt musí přesně odpovídat c# třídy.</span><span class="sxs-lookup"><span data-stu-id="d16f6-226">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="d16f6-227">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d16f6-227">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="d16f6-228">Pomocí `camelCased` názvů nebude správně svázat na C# třídy.</span><span class="sxs-lookup"><span data-stu-id="d16f6-228">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="d16f6-229">Můžete tento problém obejít pomocí atributu `Key` k určení jiného názvu vlastnosti MessagePack.</span><span class="sxs-lookup"><span data-stu-id="d16f6-229">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="d16f6-230">Další informace naleznete [v dokumentaci MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="d16f6-230">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="d16f6-231">DateTime.Kind není zachována při serializaci/deserializaci</span><span class="sxs-lookup"><span data-stu-id="d16f6-231">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="d16f6-232">MessagePack protokol neposkytuje způsob, jak kódovat `Kind` hodnotu . `DateTime`</span><span class="sxs-lookup"><span data-stu-id="d16f6-232">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="d16f6-233">V důsledku toho při rekonstrukci data protokol MessagePack Hub protokol předpokládá, že příchozí datum je ve formátu UTC.</span><span class="sxs-lookup"><span data-stu-id="d16f6-233">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="d16f6-234">Pokud pracujete s `DateTime` hodnotami v místním čase, doporučujeme před jejich odesláním převést na čas UTC.</span><span class="sxs-lookup"><span data-stu-id="d16f6-234">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="d16f6-235">Převeďte je z Času UTC na místní čas, když je obdržíte.</span><span class="sxs-lookup"><span data-stu-id="d16f6-235">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="d16f6-236">Další informace o tomto omezení naleznete v tématu GitHub problém [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="d16f6-236">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="d16f6-237">DateTime.MinValue není podporovánmessagepack v Jazyce JavaScript</span><span class="sxs-lookup"><span data-stu-id="d16f6-237">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="d16f6-238">Knihovna [msgpack5](https://github.com/mcollina/msgpack5) používaná klientem SignalR Jazyka JavaScript nepodporuje typ v messagepacku. `timestamp96`</span><span class="sxs-lookup"><span data-stu-id="d16f6-238">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="d16f6-239">Tento typ se používá ke kódování velmi velké hodnoty kalendářních dat (buď velmi brzy v minulosti nebo velmi daleko v budoucnosti).</span><span class="sxs-lookup"><span data-stu-id="d16f6-239">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="d16f6-240">Hodnota `DateTime.MinValue` je `January 1, 0001`, která musí být zakódována v hodnotě. `timestamp96`</span><span class="sxs-lookup"><span data-stu-id="d16f6-240">The value of `DateTime.MinValue` is `January 1, 0001`, which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="d16f6-241">Z tohoto důvodu `DateTime.MinValue` není podporováno odesílání do klienta JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="d16f6-241">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="d16f6-242">Když `DateTime.MinValue` je přijat klientem JavaScript, je vyvolána následující chyba:</span><span class="sxs-lookup"><span data-stu-id="d16f6-242">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="d16f6-243">Obvykle `DateTime.MinValue` se používá ke kódování "chybějící" nebo `null` hodnota.</span><span class="sxs-lookup"><span data-stu-id="d16f6-243">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="d16f6-244">Pokud potřebujete kódovat tuto hodnotu v MessagePack,`DateTime?`použijte hodnotu s `bool` hodnotou `DateTime` null ( ) nebo zakódujte samostatnou hodnotu označující, zda je datum přítomno.</span><span class="sxs-lookup"><span data-stu-id="d16f6-244">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="d16f6-245">Další informace o tomto omezení naleznete v tématu GitHub problém [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="d16f6-245">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="d16f6-246">Podpora messagepacku v prostředí kompilace "ahead-of-time"</span><span class="sxs-lookup"><span data-stu-id="d16f6-246">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="d16f6-247">Knihovna [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) používaná klientem a serverem .NET používá generování kódu k optimalizaci serializace.</span><span class="sxs-lookup"><span data-stu-id="d16f6-247">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="d16f6-248">V důsledku toho není ve výchozím nastavení podporována v prostředích, která používají kompilaci "ahead-of-time" (například Xamarin iOS nebo Unity).</span><span class="sxs-lookup"><span data-stu-id="d16f6-248">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="d16f6-249">Je možné použít MessagePack v těchto prostředích "pre-generating" serializátor/deserializer kód.</span><span class="sxs-lookup"><span data-stu-id="d16f6-249">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="d16f6-250">Další informace naleznete [v dokumentaci MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="d16f6-250">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="d16f6-251">Jakmile jste předem vygenerovali serializátory, můžete je `AddMessagePackProtocol`zaregistrovat pomocí delegáta konfigurace předaný :</span><span class="sxs-lookup"><span data-stu-id="d16f6-251">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="d16f6-252">Typ kontroly jsou přísnější v MessagePack</span><span class="sxs-lookup"><span data-stu-id="d16f6-252">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="d16f6-253">Protokol JSON Hub bude během deserializace provádět převody typů.</span><span class="sxs-lookup"><span data-stu-id="d16f6-253">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="d16f6-254">Například pokud příchozí objekt má hodnotu vlastnosti,`{ foo: 42 }`která je číslo ( ), ale `string`vlastnost na .NET třída je typu , hodnota bude převedena.</span><span class="sxs-lookup"><span data-stu-id="d16f6-254">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="d16f6-255">MessagePack však neprovede tento převod a vyvolá výjimku, která může být vidět v protokolech na straně serveru (a v konzoli):</span><span class="sxs-lookup"><span data-stu-id="d16f6-255">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="d16f6-256">Další informace o tomto omezení naleznete v tématu GitHub problém [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="d16f6-256">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="d16f6-257">Související prostředky</span><span class="sxs-lookup"><span data-stu-id="d16f6-257">Related resources</span></span>

* [<span data-ttu-id="d16f6-258">Začínáme</span><span class="sxs-lookup"><span data-stu-id="d16f6-258">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="d16f6-259">Klient rozhraní .NET</span><span class="sxs-lookup"><span data-stu-id="d16f6-259">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="d16f6-260">Javascriptový klient</span><span class="sxs-lookup"><span data-stu-id="d16f6-260">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d16f6-261">Tento článek předpokládá, že čtenář je obeznámen s tématy, na které se vztahuje [v Začínáme](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="d16f6-261">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="d16f6-262">Co je MessagePack?</span><span class="sxs-lookup"><span data-stu-id="d16f6-262">What is MessagePack?</span></span>

<span data-ttu-id="d16f6-263">[MessagePack](https://msgpack.org/index.html) je rychlý a kompaktní binární serializace formátu.</span><span class="sxs-lookup"><span data-stu-id="d16f6-263">[MessagePack](https://msgpack.org/index.html) is a fast and compact binary serialization format.</span></span> <span data-ttu-id="d16f6-264">Je to užitečné, když výkon a šířka pásma jsou problém, protože vytváří menší zprávy ve srovnání s [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="d16f6-264">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="d16f6-265">Binární zprávy jsou nečitelné při pohledu na trasování sítě a protokoly, pokud bajty jsou předány prostřednictvím analyzátormessagepack.</span><span class="sxs-lookup"><span data-stu-id="d16f6-265">The binary messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> SignalR<span data-ttu-id="d16f6-266">Má integrovanou podporu formátu MessagePack a poskytuje api pro klienta a server k použití.</span><span class="sxs-lookup"><span data-stu-id="d16f6-266"> has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="d16f6-267">Konfigurace programu MessagePack na serveru</span><span class="sxs-lookup"><span data-stu-id="d16f6-267">Configure MessagePack on the server</span></span>

<span data-ttu-id="d16f6-268">Chcete-li povolit protokol MessagePack Hub `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` na serveru, nainstalujte balíček do aplikace.</span><span class="sxs-lookup"><span data-stu-id="d16f6-268">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="d16f6-269">V `Startup.ConfigureServices` metodě `AddMessagePackProtocol` přidejte `AddSignalR` do volání povolit messagepack podporu na serveru.</span><span class="sxs-lookup"><span data-stu-id="d16f6-269">In the `Startup.ConfigureServices` method, add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="d16f6-270">JSON je ve výchozím nastavení povolen.</span><span class="sxs-lookup"><span data-stu-id="d16f6-270">JSON is enabled by default.</span></span> <span data-ttu-id="d16f6-271">Přidání MessagePack umožňuje podporu pro klienty JSON i MessagePack.</span><span class="sxs-lookup"><span data-stu-id="d16f6-271">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="d16f6-272">Chcete-li přizpůsobit, jak MessagePack bude formátovat data, `AddMessagePackProtocol` trvá delegát pro konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="d16f6-272">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="d16f6-273">V tomto delegáta `FormatterResolvers` vlastnost lze použít ke konfiguraci MessagePack možnosti serializace.</span><span class="sxs-lookup"><span data-stu-id="d16f6-273">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="d16f6-274">Další informace o tom, jak překladače fungují, naleznete v knihovně MessagePack na [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="d16f6-274">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="d16f6-275">Atributy lze použít na objekty, které chcete serializovat definovat, jak by měly být zpracovány.</span><span class="sxs-lookup"><span data-stu-id="d16f6-275">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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
> <span data-ttu-id="d16f6-276">Důrazně doporučujeme zkontrolovat [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) a použít doporučené opravy.</span><span class="sxs-lookup"><span data-stu-id="d16f6-276">We strongly recommend reviewing [CVE-2020-5234](https://github.com/neuecc/MessagePack-CSharp/security/advisories/GHSA-7q36-4xx7-xcxf) and applying the recommended patches.</span></span> <span data-ttu-id="d16f6-277">Například nastavení `MessagePackSecurity.Active` statické vlastnosti na `MessagePackSecurity.UntrustedData`.</span><span class="sxs-lookup"><span data-stu-id="d16f6-277">For example, setting the `MessagePackSecurity.Active` static property to `MessagePackSecurity.UntrustedData`.</span></span> <span data-ttu-id="d16f6-278">Nastavení `MessagePackSecurity.Active` vyžaduje ruční instalaci [1.9.x verze MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span><span class="sxs-lookup"><span data-stu-id="d16f6-278">Setting the `MessagePackSecurity.Active` requires manually installing a [1.9.x version of MessagePack](https://www.nuget.org/packages/MessagePack/1.9.3).</span></span> <span data-ttu-id="d16f6-279">Instalace `MessagePack` verze 1.9.x SignalR upgraduje verzi.</span><span class="sxs-lookup"><span data-stu-id="d16f6-279">Installing `MessagePack` 1.9.x upgrades the version SignalR uses.</span></span> <span data-ttu-id="d16f6-280">Není-li `MessagePackSecurity.Active` nastavena možnost `MessagePackSecurity.UntrustedData`, může klient se zlými úmysly způsobit odmítnutí služby.</span><span class="sxs-lookup"><span data-stu-id="d16f6-280">When `MessagePackSecurity.Active` is not set to `MessagePackSecurity.UntrustedData`, a malicious client could cause a denial of service.</span></span> <span data-ttu-id="d16f6-281">Set `MessagePackSecurity.Active` `Program.Main`in , jak je znázorněno v následujícím kódu:</span><span class="sxs-lookup"><span data-stu-id="d16f6-281">Set `MessagePackSecurity.Active` in `Program.Main`, as shown in the following code:</span></span>

```csharp
public static void Main(string[] args)
{
  MessagePackSecurity.Active = MessagePackSecurity.UntrustedData;

  CreateHostBuilder(args).Build().Run();
}
```

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="d16f6-282">Konfigurace balíčku MessagePack v klientovi</span><span class="sxs-lookup"><span data-stu-id="d16f6-282">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="d16f6-283">JSON je ve výchozím nastavení povolen pro podporované klienty.</span><span class="sxs-lookup"><span data-stu-id="d16f6-283">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="d16f6-284">Klienti mohou podporovat pouze jeden protokol.</span><span class="sxs-lookup"><span data-stu-id="d16f6-284">Clients can only support a single protocol.</span></span> <span data-ttu-id="d16f6-285">Přidání podpory messagepack nahradí všechny dříve nakonfigurované protokoly.</span><span class="sxs-lookup"><span data-stu-id="d16f6-285">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="d16f6-286">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="d16f6-286">.NET client</span></span>

<span data-ttu-id="d16f6-287">Chcete-li povolit messagepack v `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` klientovi `AddMessagePackProtocol` .NET, nainstalujte balíček a volejte na `HubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="d16f6-287">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="d16f6-288">Toto `AddMessagePackProtocol` volání trvá delegátpro konfiguraci možností, stejně jako server.</span><span class="sxs-lookup"><span data-stu-id="d16f6-288">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="d16f6-289">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="d16f6-289">JavaScript client</span></span>

<span data-ttu-id="d16f6-290">Podpora messagepacku pro klienta JavaScriptu je poskytována balíčkem [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm.</span><span class="sxs-lookup"><span data-stu-id="d16f6-290">MessagePack support for the JavaScript client is provided by the [@aspnet/signalr-protocol-msgpack](https://www.npmjs.com/package/@aspnet/signalr-protocol-msgpack) npm package.</span></span> <span data-ttu-id="d16f6-291">Nainstalujte balíček spuštěním následujícího příkazu v příkazovém prostředí:</span><span class="sxs-lookup"><span data-stu-id="d16f6-291">Install the package by executing the following command in a command shell:</span></span>

```bash
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="d16f6-292">Po instalaci balíčku npm lze modul použít přímo přes javascriptový zavaděč modulu nebo importovat do prohlížeče odkazem na následující soubor:</span><span class="sxs-lookup"><span data-stu-id="d16f6-292">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the following file:</span></span>

<span data-ttu-id="d16f6-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span><span class="sxs-lookup"><span data-stu-id="d16f6-293">*node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*</span></span>

<span data-ttu-id="d16f6-294">V prohlížeči `msgpack5` musí být také odkazováno na knihovnu.</span><span class="sxs-lookup"><span data-stu-id="d16f6-294">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="d16f6-295">K `<script>` vytvoření odkazu použijte značku.</span><span class="sxs-lookup"><span data-stu-id="d16f6-295">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="d16f6-296">Knihovnu naleznete na *adrese node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="d16f6-296">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="d16f6-297">Při použití `<script>` prvku je důležité pořadí.</span><span class="sxs-lookup"><span data-stu-id="d16f6-297">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="d16f6-298">Pokud *signalr-protocol-msgpack.js* odkazuje před *msgpack5.js*, dojde k chybě při pokusu o připojení s MessagePack.</span><span class="sxs-lookup"><span data-stu-id="d16f6-298">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="d16f6-299">*signalr.js* je také vyžadován před *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="d16f6-299">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="d16f6-300">Přidání `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` do `HubConnectionBuilder` programu nakonfiguruje klienta tak, aby při připojování k serveru používal protokol MessagePack.</span><span class="sxs-lookup"><span data-stu-id="d16f6-300">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="d16f6-301">V současné době neexistují žádné možnosti konfigurace pro messagepack protokol na klienta JavaScript.</span><span class="sxs-lookup"><span data-stu-id="d16f6-301">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="d16f6-302">MessagePack vtípky</span><span class="sxs-lookup"><span data-stu-id="d16f6-302">MessagePack quirks</span></span>

<span data-ttu-id="d16f6-303">Existuje několik problémů, které je třeba vědět při použití messagepack hub protokolu.</span><span class="sxs-lookup"><span data-stu-id="d16f6-303">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="d16f6-304">MessagePack rozlišuje malá a velká písmena</span><span class="sxs-lookup"><span data-stu-id="d16f6-304">MessagePack is case-sensitive</span></span>

<span data-ttu-id="d16f6-305">MessagePack protokol je malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="d16f6-305">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="d16f6-306">Zvažte například následující třídu C#:</span><span class="sxs-lookup"><span data-stu-id="d16f6-306">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="d16f6-307">Při odesílání z klienta JavaScript, musíte použít `PascalCased` názvy vlastností, protože kryt musí přesně odpovídat c# třídy.</span><span class="sxs-lookup"><span data-stu-id="d16f6-307">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="d16f6-308">Příklad:</span><span class="sxs-lookup"><span data-stu-id="d16f6-308">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="d16f6-309">Pomocí `camelCased` názvů nebude správně svázat na C# třídy.</span><span class="sxs-lookup"><span data-stu-id="d16f6-309">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="d16f6-310">Můžete tento problém obejít pomocí atributu `Key` k určení jiného názvu vlastnosti MessagePack.</span><span class="sxs-lookup"><span data-stu-id="d16f6-310">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="d16f6-311">Další informace naleznete [v dokumentaci MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="d16f6-311">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="d16f6-312">DateTime.Kind není zachována při serializaci/deserializaci</span><span class="sxs-lookup"><span data-stu-id="d16f6-312">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="d16f6-313">MessagePack protokol neposkytuje způsob, jak kódovat `Kind` hodnotu . `DateTime`</span><span class="sxs-lookup"><span data-stu-id="d16f6-313">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="d16f6-314">V důsledku toho při rekonstrukci data protokol MessagePack Hub protokol předpokládá, že příchozí datum je ve formátu UTC.</span><span class="sxs-lookup"><span data-stu-id="d16f6-314">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="d16f6-315">Pokud pracujete s `DateTime` hodnotami v místním čase, doporučujeme před jejich odesláním převést na čas UTC.</span><span class="sxs-lookup"><span data-stu-id="d16f6-315">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="d16f6-316">Převeďte je z Času UTC na místní čas, když je obdržíte.</span><span class="sxs-lookup"><span data-stu-id="d16f6-316">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="d16f6-317">Další informace o tomto omezení naleznete v tématu GitHub problém [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="d16f6-317">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="d16f6-318">DateTime.MinValue není podporovánmessagepack v Jazyce JavaScript</span><span class="sxs-lookup"><span data-stu-id="d16f6-318">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="d16f6-319">Knihovna [msgpack5](https://github.com/mcollina/msgpack5) používaná klientem SignalR Jazyka JavaScript nepodporuje typ v messagepacku. `timestamp96`</span><span class="sxs-lookup"><span data-stu-id="d16f6-319">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="d16f6-320">Tento typ se používá ke kódování velmi velké hodnoty kalendářních dat (buď velmi brzy v minulosti nebo velmi daleko v budoucnosti).</span><span class="sxs-lookup"><span data-stu-id="d16f6-320">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="d16f6-321">Hodnota `DateTime.MinValue` is, `January 1, 0001` která musí být zakódována v hodnotě. `timestamp96`</span><span class="sxs-lookup"><span data-stu-id="d16f6-321">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="d16f6-322">Z tohoto důvodu `DateTime.MinValue` není podporováno odesílání do klienta JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="d16f6-322">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="d16f6-323">Když `DateTime.MinValue` je přijat klientem JavaScript, je vyvolána následující chyba:</span><span class="sxs-lookup"><span data-stu-id="d16f6-323">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="d16f6-324">Obvykle `DateTime.MinValue` se používá ke kódování "chybějící" nebo `null` hodnota.</span><span class="sxs-lookup"><span data-stu-id="d16f6-324">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="d16f6-325">Pokud potřebujete kódovat tuto hodnotu v MessagePack,`DateTime?`použijte hodnotu s `bool` hodnotou `DateTime` null ( ) nebo zakódujte samostatnou hodnotu označující, zda je datum přítomno.</span><span class="sxs-lookup"><span data-stu-id="d16f6-325">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="d16f6-326">Další informace o tomto omezení naleznete v tématu GitHub problém [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="d16f6-326">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="d16f6-327">Podpora messagepacku v prostředí kompilace "ahead-of-time"</span><span class="sxs-lookup"><span data-stu-id="d16f6-327">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="d16f6-328">Knihovna [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) používaná klientem a serverem .NET používá generování kódu k optimalizaci serializace.</span><span class="sxs-lookup"><span data-stu-id="d16f6-328">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="d16f6-329">V důsledku toho není ve výchozím nastavení podporována v prostředích, která používají kompilaci "ahead-of-time" (například Xamarin iOS nebo Unity).</span><span class="sxs-lookup"><span data-stu-id="d16f6-329">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="d16f6-330">Je možné použít MessagePack v těchto prostředích "pre-generating" serializátor/deserializer kód.</span><span class="sxs-lookup"><span data-stu-id="d16f6-330">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="d16f6-331">Další informace naleznete [v dokumentaci MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="d16f6-331">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp/tree/v1.8.80#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="d16f6-332">Jakmile jste předem vygenerovali serializátory, můžete je `AddMessagePackProtocol`zaregistrovat pomocí delegáta konfigurace předaný :</span><span class="sxs-lookup"><span data-stu-id="d16f6-332">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="d16f6-333">Typ kontroly jsou přísnější v MessagePack</span><span class="sxs-lookup"><span data-stu-id="d16f6-333">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="d16f6-334">Protokol JSON Hub bude během deserializace provádět převody typů.</span><span class="sxs-lookup"><span data-stu-id="d16f6-334">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="d16f6-335">Například pokud příchozí objekt má hodnotu vlastnosti,`{ foo: 42 }`která je číslo ( ), ale `string`vlastnost na .NET třída je typu , hodnota bude převedena.</span><span class="sxs-lookup"><span data-stu-id="d16f6-335">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="d16f6-336">MessagePack však neprovede tento převod a vyvolá výjimku, která může být vidět v protokolech na straně serveru (a v konzoli):</span><span class="sxs-lookup"><span data-stu-id="d16f6-336">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="d16f6-337">Další informace o tomto omezení naleznete v tématu GitHub problém [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="d16f6-337">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="d16f6-338">Související prostředky</span><span class="sxs-lookup"><span data-stu-id="d16f6-338">Related resources</span></span>

* [<span data-ttu-id="d16f6-339">Začínáme</span><span class="sxs-lookup"><span data-stu-id="d16f6-339">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="d16f6-340">Klient rozhraní .NET</span><span class="sxs-lookup"><span data-stu-id="d16f6-340">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="d16f6-341">Javascriptový klient</span><span class="sxs-lookup"><span data-stu-id="d16f6-341">JavaScript client</span></span>](xref:signalr/javascript-client)

::: moniker-end
