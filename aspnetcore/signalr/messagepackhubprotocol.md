---
title: Protokol MessagePack rozbočovače signalr pro ASP.NET Core
author: bradygaster
description: Přidáte protokol MessagePack rozbočovače SignalR technologie ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/04/2018
uid: signalr/messagepackhubprotocol
ms.openlocfilehash: 2852ca93c62e706e9a5203625822c2fb954fd2b8
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54835606"
---
# <a name="use-messagepack-hub-protocol-in-signalr-for-aspnet-core"></a><span data-ttu-id="35971-103">Protokol MessagePack rozbočovače signalr pro ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="35971-103">Use MessagePack Hub Protocol in SignalR for ASP.NET Core</span></span>

<span data-ttu-id="35971-104">Podle [Brennan Conroy](https://github.com/BrennanConroy)</span><span class="sxs-lookup"><span data-stu-id="35971-104">By [Brennan Conroy](https://github.com/BrennanConroy)</span></span>

<span data-ttu-id="35971-105">Tento článek předpokládá čtecí modul se seznámit s tématy v [Začínáme](xref:tutorials/signalr).</span><span class="sxs-lookup"><span data-stu-id="35971-105">This article assumes the reader is familiar with the topics covered in [Get Started](xref:tutorials/signalr).</span></span>

## <a name="what-is-messagepack"></a><span data-ttu-id="35971-106">Co je MessagePack?</span><span class="sxs-lookup"><span data-stu-id="35971-106">What is MessagePack?</span></span>

<span data-ttu-id="35971-107">[MessagePack](https://msgpack.org/index.html) je binární serializační formát, který je rychlý a compact.</span><span class="sxs-lookup"><span data-stu-id="35971-107">[MessagePack](https://msgpack.org/index.html) is a binary serialization format that is fast and compact.</span></span> <span data-ttu-id="35971-108">To je užitečné, když výkonu a šířky pásma jsou důležité, protože vytváří menší zprávy ve srovnání s [JSON](https://www.json.org/).</span><span class="sxs-lookup"><span data-stu-id="35971-108">It's useful when performance and bandwidth are a concern because it creates smaller messages compared to [JSON](https://www.json.org/).</span></span> <span data-ttu-id="35971-109">Vzhledem k tomu, že je binární formát, jsou zprávy nejde přečíst, při prohlížení protokoly a trasování sítě, pokud počet bajtů se předaly MessagePack analyzátor.</span><span class="sxs-lookup"><span data-stu-id="35971-109">Because it's a binary format, messages are unreadable when looking at network traces and logs unless the bytes are passed through a MessagePack parser.</span></span> <span data-ttu-id="35971-110">Funkce SignalR obsahuje integrovanou podporu pro formát MessagePack a poskytuje rozhraní API pro klienty a server používat.</span><span class="sxs-lookup"><span data-stu-id="35971-110">SignalR has built-in support for the MessagePack format, and provides APIs for the client and server to use.</span></span>

## <a name="configure-messagepack-on-the-server"></a><span data-ttu-id="35971-111">Konfigurace MessagePack na serveru</span><span class="sxs-lookup"><span data-stu-id="35971-111">Configure MessagePack on the server</span></span>

<span data-ttu-id="35971-112">Pokud chcete povolit protokol MessagePack rozbočovače na serveru, nainstalujte `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` balíček ve vaší aplikaci.</span><span class="sxs-lookup"><span data-stu-id="35971-112">To enable the MessagePack Hub Protocol on the server, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package in your app.</span></span> <span data-ttu-id="35971-113">V souboru Startup.cs přidat `AddMessagePackProtocol` k `AddSignalR` volání k povolení podpory MessagePack na serveru.</span><span class="sxs-lookup"><span data-stu-id="35971-113">In the Startup.cs file add `AddMessagePackProtocol` to the `AddSignalR` call to enable MessagePack support on the server.</span></span>

> [!NOTE]
> <span data-ttu-id="35971-114">JSON je standardně povolená.</span><span class="sxs-lookup"><span data-stu-id="35971-114">JSON is enabled by default.</span></span> <span data-ttu-id="35971-115">Přidání MessagePack umožňuje podporu pro JSON a MessagePack klienty.</span><span class="sxs-lookup"><span data-stu-id="35971-115">Adding MessagePack enables support for both JSON and MessagePack clients.</span></span>

```csharp
services.AddSignalR()
    .AddMessagePackProtocol();
```

<span data-ttu-id="35971-116">Přizpůsobit, jak bude MessagePack formátovat data, `AddMessagePackProtocol` přijímá delegát pro konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="35971-116">To customize how MessagePack will format your data, `AddMessagePackProtocol` takes a delegate for configuring options.</span></span> <span data-ttu-id="35971-117">V delegátu `FormatterResolvers` vlastnost lze použít ke konfiguraci možností MessagePack serializace.</span><span class="sxs-lookup"><span data-stu-id="35971-117">In that delegate, the `FormatterResolvers` property can be used to configure MessagePack serialization options.</span></span> <span data-ttu-id="35971-118">Další informace o fungování překladače v knihovně MessagePack na [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span><span class="sxs-lookup"><span data-stu-id="35971-118">For more information on how the resolvers work, visit the MessagePack library at [MessagePack-CSharp](https://github.com/neuecc/MessagePack-CSharp).</span></span> <span data-ttu-id="35971-119">Atributy lze použít na objekty, které chcete serializovat k definování, jak by měl být zpracována.</span><span class="sxs-lookup"><span data-stu-id="35971-119">Attributes can be used on the objects you want to serialize to define how they should be handled.</span></span>

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

## <a name="configure-messagepack-on-the-client"></a><span data-ttu-id="35971-120">MessagePack konfigurace na straně klienta</span><span class="sxs-lookup"><span data-stu-id="35971-120">Configure MessagePack on the client</span></span>

> [!NOTE]
> <span data-ttu-id="35971-121">JSON je ve výchozím nastavení povolena pro Podporovaní klienti.</span><span class="sxs-lookup"><span data-stu-id="35971-121">JSON is enabled by default for the supported clients.</span></span> <span data-ttu-id="35971-122">Klienti podporují pouze jeden protokol.</span><span class="sxs-lookup"><span data-stu-id="35971-122">Clients can only support a single protocol.</span></span> <span data-ttu-id="35971-123">Přidání podpory MessagePack nahradí všechny dřív nakonfigurované protokoly.</span><span class="sxs-lookup"><span data-stu-id="35971-123">Adding MessagePack support will replace any previously configured protocols.</span></span>

### <a name="net-client"></a><span data-ttu-id="35971-124">.NET client</span><span class="sxs-lookup"><span data-stu-id="35971-124">.NET client</span></span>

<span data-ttu-id="35971-125">Pokud chcete povolit MessagePack v klientovi .NET, nainstalujte `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` balíčku a volání `AddMessagePackProtocol` na `HubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="35971-125">To enable MessagePack in the .NET Client, install the `Microsoft.AspNetCore.SignalR.Protocols.MessagePack` package and call `AddMessagePackProtocol` on `HubConnectionBuilder`.</span></span>

```csharp
var hubConnection = new HubConnectionBuilder()
                        .WithUrl("/chatHub")
                        .AddMessagePackProtocol()
                        .Build();
```

> [!NOTE]
> <span data-ttu-id="35971-126">To `AddMessagePackProtocol` trvá volání delegáta pro konfiguraci možností, stejně jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="35971-126">This `AddMessagePackProtocol` call takes a delegate for configuring options just like the server.</span></span>

### <a name="javascript-client"></a><span data-ttu-id="35971-127">Javascriptový klient</span><span class="sxs-lookup"><span data-stu-id="35971-127">JavaScript client</span></span>

<span data-ttu-id="35971-128">Poskytuje MessagePack podporu pro Javascript klienta `@aspnet/signalr-protocol-msgpack` balíčku NPM.</span><span class="sxs-lookup"><span data-stu-id="35971-128">MessagePack support for the Javascript client is provided by the `@aspnet/signalr-protocol-msgpack` NPM package.</span></span>

```console
npm install @aspnet/signalr-protocol-msgpack
```

<span data-ttu-id="35971-129">Po instalaci balíčku npm, lze použít přímo prostřednictvím zavaděč modulu JavaScript modulu nebo importovat do prohlížeče odkazováním *node_modules\\ @aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js*  souboru.</span><span class="sxs-lookup"><span data-stu-id="35971-129">After installing the npm package, the module can be used directly via a JavaScript module loader or imported into the browser by referencing the *node_modules\\@aspnet\signalr-protocol-msgpack\dist\browser\signalr-protocol-msgpack.js* file.</span></span> <span data-ttu-id="35971-130">V prohlížeči `msgpack5` knihovny musí také odkazovat.</span><span class="sxs-lookup"><span data-stu-id="35971-130">In a browser, the `msgpack5` library must also be referenced.</span></span> <span data-ttu-id="35971-131">Použití `<script>` značku k vytvoření odkazu.</span><span class="sxs-lookup"><span data-stu-id="35971-131">Use a `<script>` tag to create a reference.</span></span> <span data-ttu-id="35971-132">Knihovnu lze nalézt v *node_modules\msgpack5\dist\msgpack5.js*.</span><span class="sxs-lookup"><span data-stu-id="35971-132">The library can be found at *node_modules\msgpack5\dist\msgpack5.js*.</span></span>

> [!NOTE]
> <span data-ttu-id="35971-133">Při použití `<script>` prvku, pořadí je důležité.</span><span class="sxs-lookup"><span data-stu-id="35971-133">When using the `<script>` element, the order is important.</span></span> <span data-ttu-id="35971-134">Pokud *signalr-protocol-msgpack.js* odkazuje před *msgpack5.js*, dojde k chybě při pokusu o připojení s MessagePack.</span><span class="sxs-lookup"><span data-stu-id="35971-134">If *signalr-protocol-msgpack.js* is referenced before *msgpack5.js*, an error occurs when trying to connect with MessagePack.</span></span> <span data-ttu-id="35971-135">*signalr.js* je také nutné před *signalr-protocol-msgpack.js*.</span><span class="sxs-lookup"><span data-stu-id="35971-135">*signalr.js* is also required before *signalr-protocol-msgpack.js*.</span></span>

```html
<script src="~/lib/signalr/signalr.js"></script>
<script src="~/lib/msgpack5/msgpack5.js"></script>
<script src="~/lib/signalr/signalr-protocol-msgpack.js"></script>
```

<span data-ttu-id="35971-136">Přidání `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` k `HubConnectionBuilder` nakonfiguruje klienta pro použití protokolu MessagePack při připojování k serveru.</span><span class="sxs-lookup"><span data-stu-id="35971-136">Adding `.withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())` to the `HubConnectionBuilder` will configure the client to use the MessagePack protocol when connecting to a server.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chatHub")
    .withHubProtocol(new signalR.protocols.msgpack.MessagePackHubProtocol())
    .build();
```

> [!NOTE]
> <span data-ttu-id="35971-137">V tuto chvíli nejsou k dispozici žádné možnosti konfigurace pro protokol MessagePack na klientovi JavaScript.</span><span class="sxs-lookup"><span data-stu-id="35971-137">At this time, there are no configuration options for the MessagePack protocol on the JavaScript client.</span></span>

## <a name="related-resources"></a><span data-ttu-id="35971-138">Související prostředky</span><span class="sxs-lookup"><span data-stu-id="35971-138">Related resources</span></span>

* [<span data-ttu-id="35971-139">Začínáme</span><span class="sxs-lookup"><span data-stu-id="35971-139">Get Started</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="35971-140">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="35971-140">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="35971-141">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="35971-141">JavaScript client</span></span>](xref:signalr/javascript-client)
