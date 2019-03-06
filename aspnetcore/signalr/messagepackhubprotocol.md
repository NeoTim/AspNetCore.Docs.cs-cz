---
title: Protokol MessagePack rozbočovače signalr pro ASP.NET Core
author: bradygaster
description: Přidáte protokol MessagePack rozbočovače SignalR technologie ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 02/27/2019
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: 7742f6f8bb53fb3c299ff98ae52a0da519ff396c
ms.sourcegitcommit: 6ddd8a7675c1c1d997c8ab2d4498538e44954cac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57400668"
---
# <a name="use-messagepack-hub-protocol-in-signalr-for-aspnet-core"></a><span data-ttu-id="b0403-103">Protokol MessagePack rozbočovače signalr pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b0403-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

<span data-ttu-id="b0403-104">Podle [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="b0403-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

<span data-ttu-id="b0403-105">Tento článek předpokládá čtecí modul se seznámit s tématy v [Začínáme](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="b0403-105">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="b0403-106">Co je MessagePack?</span><span class="sxs-lookup"><span data-stu-id="b0403-106">What is MessagePack?</span></span>

<span data-ttu-id="b0403-107">[MessagePack](https://msgpack.org/index.html) je binární serializační formát, který je rychlý a compact.</span><span class="sxs-lookup"><span data-stu-id="b0403-107">[MessagePack](https://msgpack.org/index.html) is a binary serialization format that is fast and compact.</span></span> <span data-ttu-id="b0403-108">To je užitečné, když výkonu a šířky pásma jsou důležité, protože vytváří menší zprávy ve srovnání s [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="b0403-108">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="b0403-109">Vzhledem k tomu, že je binární formát, jsou zprávy nejde přečíst, při prohlížení protokoly a trasování sítě, pokud počet bajtů se předaly MessagePack analyzátor.</span><span class="sxs-lookup"><span data-stu-id="b0403-109">Because it's a binary format, messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="b0403-110">Funkce SignalR obsahuje integrovanou podporu pro formát MessagePack a poskytuje rozhraní API pro klienty a server používat.</span><span class="sxs-lookup"><span data-stu-id="b0403-110">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="b0403-111">Konfigurace MessagePack na serveru</span><span class="sxs-lookup"><span data-stu-id="b0403-111">Configure MessagePack on the server</span></span>

<span data-ttu-id="b0403-112">Pokud chcete povolit protokol MessagePack rozbočovače na serveru, nainstalujte `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` balíček ve vaší aplikaci.</span><span class="sxs-lookup"><span data-stu-id="b0403-112">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="b0403-113">V souboru Startup.cs přidat `AddMessagePackProtocol` k `AddSignalR` volání k povolení podpory MessagePack na serveru.</span><span class="sxs-lookup"><span data-stu-id="b0403-113">In the Startup.cs file add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="b0403-114">JSON je standardně povolená.</span><span class="sxs-lookup"><span data-stu-id="b0403-114">JSON is enabled by default.</span></span> <span data-ttu-id="b0403-115">Přidání MessagePack umožňuje podporu pro JSON a MessagePack klienty.</span><span class="sxs-lookup"><span data-stu-id="b0403-115">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="b0403-116">Přizpůsobit, jak bude MessagePack formátovat data, `AddMessagePackProtocol` přijímá delegát pro konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="b0403-116">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="b0403-117">V delegátu `FormatterResolvers` vlastnost lze použít ke konfiguraci možností MessagePack serializace.</span><span class="sxs-lookup"><span data-stu-id="b0403-117">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="b0403-118">Další informace o fungování překladače v knihovně MessagePack na [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="b0403-118">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="b0403-119">Atributy lze použít na objekty, které chcete serializovat k definování, jak by měl být zpracována.</span><span class="sxs-lookup"><span data-stu-id="b0403-119">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="b0403-120">MessagePack konfigurace na straně klienta</span><span class="sxs-lookup"><span data-stu-id="b0403-120">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="b0403-121">JSON je ve výchozím nastavení povolena pro Podporovaní klienti.</span><span class="sxs-lookup"><span data-stu-id="b0403-121">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="b0403-122">Klienti podporují pouze jeden protokol.</span><span class="sxs-lookup"><span data-stu-id="b0403-122">Clients can only support a single protocol.</span></span> <span data-ttu-id="b0403-123">Přidání podpory MessagePack nahradí všechny dřív nakonfigurované protokoly.</span><span class="sxs-lookup"><span data-stu-id="b0403-123">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="b0403-124">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="b0403-124">.NET client</span></span>

<span data-ttu-id="b0403-125">Pokud chcete povolit MessagePack v klientovi .NET, nainstalujte `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` balíčku a volání `AddMessagePackProtocol` na `HubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="b0403-125">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="b0403-126">To `AddMessagePackProtocol` trvá volání delegáta pro konfiguraci možností, stejně jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="b0403-126">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="b0403-127">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="b0403-127">JavaScript client</span></span>

<span data-ttu-id="b0403-128">Poskytuje MessagePack podporu pro JavaScript klienta `@aspnet/signalr-protocol-msgpack` balíčku npm.</span><span class="sxs-lookup"><span data-stu-id="b0403-128">MessagePack support for the JavaScript client is provided by the `@aspnet/signalr-protocol-msgpack` npm package.</span></span>

```console
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="b0403-129">Po instalaci balíčku npm, lze použít přímo prostřednictvím zavaděč modulu JavaScript modulu nebo importovat do prohlížeče odkazováním *node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* souboru.</span><span class="sxs-lookup"><span data-stu-id="b0403-129">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the *node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* file.</span></span> <span data-ttu-id="b0403-130">V prohlížeči `msgpack5` knihovny musí také odkazovat.</span><span class="sxs-lookup"><span data-stu-id="b0403-130">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="b0403-131">Použití `<script>` značku k vytvoření odkazu.</span><span class="sxs-lookup"><span data-stu-id="b0403-131">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="b0403-132">Knihovnu lze nalézt v *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="b0403-132">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="b0403-133">Při použití `<script>` prvku, pořadí je důležité.</span><span class="sxs-lookup"><span data-stu-id="b0403-133">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="b0403-134">Pokud *signalr-protocol-msgpack.js* odkazuje před *msgpack5.js*, dojde k chybě při pokusu o připojení s MessagePack.</span><span class="sxs-lookup"><span data-stu-id="b0403-134">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="b0403-135">*signalr.js* je také nutné před *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="b0403-135">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="b0403-136">Přidání `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` k `HubConnectionBuilder` nakonfiguruje klienta pro použití protokolu MessagePack při připojování k serveru.</span><span class="sxs-lookup"><span data-stu-id="b0403-136">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="b0403-137">V tuto chvíli nejsou k dispozici žádné možnosti konfigurace pro protokol MessagePack na klientovi JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b0403-137">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="messagepack-quirks"></a><span data-ttu-id="b0403-138">Adaptivní MessagePack</span><span class="sxs-lookup"><span data-stu-id="b0403-138">MessagePack quirks</span></span>

<span data-ttu-id="b0403-139">Existuje několik problémů, je potřeba vědět při použití protokolu MessagePack rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="b0403-139">There are a few issues to be aware of when using the MessagePack Hub Protocol.</span></span>

### <a name="messagepack-is-case-sensitive"></a><span data-ttu-id="b0403-140">MessagePack je malá a velká písmena</span><span class="sxs-lookup"><span data-stu-id="b0403-140">MessagePack is case-sensitive</span></span>

<span data-ttu-id="b0403-141">Protokol MessagePack rozlišuje velká a malá písmena.</span><span class="sxs-lookup"><span data-stu-id="b0403-141">The MessagePack protocol is case-sensitive.</span></span> <span data-ttu-id="b0403-142">Představte si třeba následující C# třídy:</span><span class="sxs-lookup"><span data-stu-id="b0403-142">For example, consider the following C# class:</span></span>

```csharp
public class ChatMessage
{
    public string Sender { get; }
    public string Message { get; }
}
```

<span data-ttu-id="b0403-143">Při odesílání z Javascriptového klienta, je nutné použít `PascalCased` názvy vlastností, protože se musí shodovat velká a malá písmena C# přesně třídy.</span><span class="sxs-lookup"><span data-stu-id="b0403-143">When sending from the JavaScript client, you must use `PascalCased` property names, since the casing must match the C# class exactly.</span></span> <span data-ttu-id="b0403-144">Příklad:</span><span class="sxs-lookup"><span data-stu-id="b0403-144">For example:</span></span>

```javascript
connection.invoke("SomeMethod", { Sender: "Sally", Message: "Hello!" });
```

<span data-ttu-id="b0403-145">Pomocí `camelCased` názvy nebudou správně vázat na C# třídy.</span><span class="sxs-lookup"><span data-stu-id="b0403-145">Using `camelCased` names won't properly bind to the C# class.</span></span> <span data-ttu-id="b0403-146">Můžete alternativně vyřešit to s využitím `Key` atribut zadejte jiný název pro vlastnost MessagePack.</span><span class="sxs-lookup"><span data-stu-id="b0403-146">You can work around this by using the `Key` attribute to specify a different name for the MessagePack property.</span></span> <span data-ttu-id="b0403-147">Další informace najdete v tématu [dokumentaci MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span><span class="sxs-lookup"><span data-stu-id="b0403-147">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#object-serialization).</span></span>

### <a name="datetimekind-is-not-preserved-when-serializingdeserializing"></a><span data-ttu-id="b0403-148">DateTime.Kind se nezachová při serializaci/deserializaci</span><span class="sxs-lookup"><span data-stu-id="b0403-148">DateTime.Kind is not preserved when serializing/deserializing</span></span>

<span data-ttu-id="b0403-149">MessagePack protokol neposkytuje způsob, jak kódovat `Kind` hodnotu `DateTime`.</span><span class="sxs-lookup"><span data-stu-id="b0403-149">The MessagePack protocol doesn't provide a way to encode the `Kind` value of a `DateTime`.</span></span> <span data-ttu-id="b0403-150">V důsledku toho při deserializaci datum, MessagePack centra protokol se předpokládá, že příchozí datum je ve formátu UTC.</span><span class="sxs-lookup"><span data-stu-id="b0403-150">As a result, when deserializing a date, the MessagePack Hub Protocol assumes the incoming date is in UTC format.</span></span> <span data-ttu-id="b0403-151">Pokud pracujete s `DateTime` hodnoty v místním čase, doporučujeme převod na standard UTC před jejich odesláním.</span><span class="sxs-lookup"><span data-stu-id="b0403-151">If you're working with `DateTime` values in local time, we recommend converting to UTC before sending them.</span></span> <span data-ttu-id="b0403-152">Převod je od času UTC na místní čas při jejich obdržení.</span><span class="sxs-lookup"><span data-stu-id="b0403-152">Convert them from UTC to local time when you receive them.</span></span>

<span data-ttu-id="b0403-153">Další informace o tomto omezení, najdete na Githubu problém [aspnet/SignalR #2632](https://github.com/aspnet/SignalR/issues/2632).</span><span class="sxs-lookup"><span data-stu-id="b0403-153">For more information on this limitation, see GitHub issue [aspnet/SignalR#2632](https://github.com/aspnet/SignalR/issues/2632).</span></span>

### <a name="datetimeminvalue-is-not-supported-by-messagepack-in-javascript"></a><span data-ttu-id="b0403-154">DateTime.MinValue nepodporuje MessagePack v jazyce JavaScript</span><span class="sxs-lookup"><span data-stu-id="b0403-154">DateTime.MinValue is not supported by MessagePack in JavaScript</span></span>

<span data-ttu-id="b0403-155">[Msgpack5](https://github.com/mcollina/msgpack5) používaných klientem SignalR JavaScript library nepodporuje `timestamp96` zadejte MessagePack.</span><span class="sxs-lookup"><span data-stu-id="b0403-155">The [msgpack5](https://github.com/mcollina/msgpack5) library used by the SignalR JavaScript client doesn't support the `timestamp96` type in MessagePack.</span></span> <span data-ttu-id="b0403-156">Tento typ se používá ke kódování velmi velké datové hodnoty (buď velmi brzy v minulosti nebo velmi úplně v budoucnosti).</span><span class="sxs-lookup"><span data-stu-id="b0403-156">This type is used to encode very large date values (either very early in the past or very far in the future).</span></span> <span data-ttu-id="b0403-157">Hodnota `DateTime.MinValue` je `January 1, 0001` musí být kódovány ve `timestamp96` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="b0403-157">The value of `DateTime.MinValue` is `January 1, 0001` which must be encoded in a `timestamp96` value.</span></span> <span data-ttu-id="b0403-158">Z důvodu této, odesílání `DateTime.MinValue` pro JavaScript klienta nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="b0403-158">Because of this, sending `DateTime.MinValue` to a JavaScript client isn't supported.</span></span> <span data-ttu-id="b0403-159">Když `DateTime.MinValue` přijetí klientem jazyka JavaScript, je vyvolána následující chybu:</span><span class="sxs-lookup"><span data-stu-id="b0403-159">When `DateTime.MinValue` is received by the JavaScript client, the following error is thrown:</span></span>

```
Uncaught Error: unable to find ext type 255 at decoder.js:427
```

<span data-ttu-id="b0403-160">Obvykle `DateTime.MinValue` slouží ke kódování "nebyl nalezen" nebo `null` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="b0403-160">Usually, `DateTime.MinValue` is used to encode a "missing" or `null` value.</span></span> <span data-ttu-id="b0403-161">Pokud je potřeba tuto hodnotu v MessagePack kódování, použijte s povolenou hodnotou Null `DateTime` hodnotu (`DateTime?`) nebo kódování samostatné `bool` hodnotu, která pokud je k dispozici data.</span><span class="sxs-lookup"><span data-stu-id="b0403-161">If you need to encode that value in MessagePack, use a nullable `DateTime` value (`DateTime?`) or encode a separate `bool` value indicating if the date is present.</span></span>

<span data-ttu-id="b0403-162">Další informace o tomto omezení, najdete na Githubu problém [aspnet/SignalR #2228](https://github.com/aspnet/SignalR/issues/2228).</span><span class="sxs-lookup"><span data-stu-id="b0403-162">For more information on this limitation, see GitHub issue [aspnet/SignalR#2228](https://github.com/aspnet/SignalR/issues/2228).</span></span>

### <a name="messagepack-support-in-ahead-of-time-compilation-environment"></a><span data-ttu-id="b0403-163">Podpora MessagePack v prostředí "ahead-of-time" kompilace</span><span class="sxs-lookup"><span data-stu-id="b0403-163">MessagePack support in "ahead-of-time" compilation environment</span></span>

<span data-ttu-id="b0403-164">[MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp) knihovny používá klient .NET a server používá k optimalizaci serializace generování kódu.</span><span class="sxs-lookup"><span data-stu-id="b0403-164">The [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp) library used by the .NET client and server uses code generation to optimize serialization.</span></span> <span data-ttu-id="b0403-165">V důsledku toho se nepodporuje ve výchozím nastavení v prostředí, které používá "ahead-of-time" kompilace (jako je Unity nebo Xamarin pro iOS).</span><span class="sxs-lookup"><span data-stu-id="b0403-165">As a result, it isn't supported by default on environments that use "ahead-of-time" compilation (such as Xamarin iOS or Unity).</span></span> <span data-ttu-id="b0403-166">Je možné použít MessagePack vygenerováním"před" serializátor/deserializátor kód v těchto prostředích.</span><span class="sxs-lookup"><span data-stu-id="b0403-166">It's possible to use MessagePack in these environments by "pre-generating" the serializer/deserializer code.</span></span> <span data-ttu-id="b0403-167">Další informace najdete v tématu [dokumentaci MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp#pre-code-generationunityxamarin-supports).</span><span class="sxs-lookup"><span data-stu-id="b0403-167">For more information, see [the MessagePack-CSharp documentation](https://github.com/neuecc/MessagePack-CSharp#pre-code-generationunityxamarin-supports).</span></span> <span data-ttu-id="b0403-168">Po vygenerování serializátory předem, můžete zaregistrovat pomocí konfigurace delegát předaný `AddMessagePackProtocol`:</span><span class="sxs-lookup"><span data-stu-id="b0403-168">Once you have pre-generated the serializers, you can register them using the configuration delegate passed to `AddMessagePackProtocol`:</span></span>

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

### <a name="type-checks-are-more-strict-in-messagepack"></a><span data-ttu-id="b0403-169">Typ kontroly jsou přísnější v MessagePack</span><span class="sxs-lookup"><span data-stu-id="b0403-169">Type checks are more strict in MessagePack</span></span>

<span data-ttu-id="b0403-170">JSON centra protokol provede převody typů během deserializace.</span><span class="sxs-lookup"><span data-stu-id="b0403-170">The JSON Hub Protocol will perform type conversions during deserialization.</span></span> <span data-ttu-id="b0403-171">Například pokud má hodnotu vlastnosti příchozí objekt, který je číslo (`{ foo: 42 }`), ale vlastnost ve třídě .NET je typu `string`, převede se hodnota.</span><span class="sxs-lookup"><span data-stu-id="b0403-171">For example, if the incoming object has a property value that is a number (`{ foo: 42 }`) but the property on the .NET class is of type `string`, the value will be converted.</span></span> <span data-ttu-id="b0403-172">MessagePack však nebude vykoná tento převod a vyvolá výjimku, která můžete zobrazit v protokolech na straně serveru (a v konzole):</span><span class="sxs-lookup"><span data-stu-id="b0403-172">However, MessagePack doesn't perform this conversion and will throw an exception that can be seen in server-side logs (and in the console):</span></span>

```
InvalidDataException: Error binding arguments. Make sure that the types of the provided values match the types of the hub method being invoked.
```

<span data-ttu-id="b0403-173">Další informace o tomto omezení, najdete na Githubu problém [aspnet/SignalR #2937](https://github.com/aspnet/SignalR/issues/2937).</span><span class="sxs-lookup"><span data-stu-id="b0403-173">For more information on this limitation, see GitHub issue [aspnet/SignalR#2937](https://github.com/aspnet/SignalR/issues/2937).</span></span>

## <a name="related-resources"></a><span data-ttu-id="b0403-174">Související prostředky</span><span class="sxs-lookup"><span data-stu-id="b0403-174">Related resources</span></span>

* [<span data-ttu-id="b0403-175">Začínáme</span><span class="sxs-lookup"><span data-stu-id="b0403-175">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="b0403-176">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="b0403-176">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="b0403-177">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="b0403-177">JavaScript client</span></span>](xref:signalr/javascript-client)
