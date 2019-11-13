---
title: Konfigurace ASP.NET Core SignalR
author: bradygaster
description: Naučte se konfigurovat aplikace ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 682cc36216a4dc9b38c87b4f67100ab565a70e5c
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963986"
---
# <a name="aspnet-core-opno-locsignalr-configuration"></a><span data-ttu-id="fbb78-103">Konfigurace ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="fbb78-103">ASP.NET Core SignalR configuration</span></span>

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="fbb78-104">Možnosti serializace JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="fbb78-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="fbb78-105">ASP.NET Core SignalR podporuje pro kódování zprávy dva protokoly: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="fbb78-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="fbb78-106">Každý protokol má možnosti konfigurace serializace.</span><span class="sxs-lookup"><span data-stu-id="fbb78-106">Each protocol has serialization configuration options.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fbb78-107">Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="fbb78-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="fbb78-108">`AddJsonProtocol` lze přidat po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="fbb78-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="fbb78-109">Metoda `AddJsonProtocol` přebírá delegáta, který přijímá objekt `options`.</span><span class="sxs-lookup"><span data-stu-id="fbb78-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="fbb78-110">Vlastnost [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) tohoto objektu je objekt <xref:System.Text.Json.JsonSerializerOptions> `System.Text.Json`, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="fbb78-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="fbb78-111">Další informace naleznete v [dokumentaci System. text. JSON](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="fbb78-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="fbb78-112">Chcete-li například nakonfigurovat serializátor, aby neměnil velká a malá písmena názvů vlastností namísto výchozích názvů "camelCase", použijte následující kód v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="fbb78-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="fbb78-113">V klientovi .NET existuje stejná `AddJsonProtocol` metoda rozšíření v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="fbb78-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="fbb78-114">Aby bylo možné přeložit metodu rozšíření, je třeba importovat obor názvů `Microsoft.Extensions.DependencyInjection`:</span><span class="sxs-lookup"><span data-stu-id="fbb78-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;

// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="fbb78-115">V tuto chvíli není možné konfigurovat serializaci JSON v klientovi jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="fbb78-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="fbb78-116">Přepnout na Newtonsoft. JSON</span><span class="sxs-lookup"><span data-stu-id="fbb78-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="fbb78-117">Pokud potřebujete funkce `Newtonsoft.Json`, které nejsou v `System.Text.Json`podporované, přečtěte si téma [Přepnutí na Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="fbb78-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="fbb78-118">Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , kterou lze přidat po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) do metody `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="fbb78-118">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="fbb78-119">Metoda `AddJsonProtocol` přebírá delegáta, který přijímá objekt `options`.</span><span class="sxs-lookup"><span data-stu-id="fbb78-119">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="fbb78-120">Vlastnost [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) tohoto objektu je objekt JSON.NET `JsonSerializerSettings`, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="fbb78-120">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="fbb78-121">Další informace najdete v [dokumentaci k JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="fbb78-121">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="fbb78-122">Chcete-li například nakonfigurovat serializátor pro použití názvů vlastností "PascalCase" namísto výchozích názvů "camelCase", použijte následující kód v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="fbb78-122">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="fbb78-123">V klientovi .NET existuje stejná `AddJsonProtocol` metoda rozšíření v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="fbb78-123">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="fbb78-124">Aby bylo možné přeložit metodu rozšíření, je třeba importovat obor názvů `Microsoft.Extensions.DependencyInjection`:</span><span class="sxs-lookup"><span data-stu-id="fbb78-124">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

```csharp
// At the top of the file:
using Microsoft.Extensions.DependencyInjection;
 
// When constructing your connection:
var connection = new HubConnectionBuilder()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    })
    .Build();
```

> [!NOTE]
> <span data-ttu-id="fbb78-125">V tuto chvíli není možné konfigurovat serializaci JSON v klientovi jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="fbb78-125">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

::: moniker-end

### <a name="messagepack-serialization-options"></a><span data-ttu-id="fbb78-126">Možnosti serializace MessagePack</span><span class="sxs-lookup"><span data-stu-id="fbb78-126">MessagePack serialization options</span></span>

<span data-ttu-id="fbb78-127">Serializaci MessagePack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="fbb78-127">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="fbb78-128">Další podrobnosti najdete [v tématu MessagePack v SignalR](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="fbb78-128">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="fbb78-129">V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi v jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="fbb78-129">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="fbb78-130">Konfigurovat možnosti serveru</span><span class="sxs-lookup"><span data-stu-id="fbb78-130">Configure server options</span></span>

<span data-ttu-id="fbb78-131">Následující tabulka popisuje možnosti konfigurace SignalRch Center:</span><span class="sxs-lookup"><span data-stu-id="fbb78-131">The following table describes options for configuring SignalR hubs:</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="fbb78-132">Možnost</span><span class="sxs-lookup"><span data-stu-id="fbb78-132">Option</span></span> | <span data-ttu-id="fbb78-133">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="fbb78-133">Default Value</span></span> | <span data-ttu-id="fbb78-134">Popis</span><span class="sxs-lookup"><span data-stu-id="fbb78-134">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="fbb78-135">30 sekund</span><span class="sxs-lookup"><span data-stu-id="fbb78-135">30 seconds</span></span> | <span data-ttu-id="fbb78-136">Server bude předpokládat, že je klient odpojený, pokud v tomto intervalu neobdržel zprávu (včetně Keep-Alive).</span><span class="sxs-lookup"><span data-stu-id="fbb78-136">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="fbb78-137">Může trvat delší dobu, než je tento časový limit, aby bylo možné klienta ve skutečnosti označit jako odpojený, protože to je implementováno.</span><span class="sxs-lookup"><span data-stu-id="fbb78-137">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="fbb78-138">Doporučená hodnota je dvojnásobná hodnota `KeepAliveInterval`.</span><span class="sxs-lookup"><span data-stu-id="fbb78-138">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="fbb78-139">15 sekund</span><span class="sxs-lookup"><span data-stu-id="fbb78-139">15 seconds</span></span> | <span data-ttu-id="fbb78-140">Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře.</span><span class="sxs-lookup"><span data-stu-id="fbb78-140">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="fbb78-141">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="fbb78-141">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="fbb78-142">Další informace o procesu handshake najdete v tématu [specifikace protokoluSignalR hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="fbb78-142">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="fbb78-143">15 sekund</span><span class="sxs-lookup"><span data-stu-id="fbb78-143">15 seconds</span></span> | <span data-ttu-id="fbb78-144">Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="fbb78-144">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="fbb78-145">Při změně `KeepAliveInterval`změňte nastavení `ServerTimeout`/`serverTimeoutInMilliseconds` na klientovi.</span><span class="sxs-lookup"><span data-stu-id="fbb78-145">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="fbb78-146">Doporučená `ServerTimeout`/`serverTimeoutInMilliseconds` hodnota je dvojnásobek hodnoty `KeepAliveInterval`.</span><span class="sxs-lookup"><span data-stu-id="fbb78-146">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="fbb78-147">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="fbb78-147">All installed protocols</span></span> | <span data-ttu-id="fbb78-148">Protokoly podporované tímto rozbočovačem</span><span class="sxs-lookup"><span data-stu-id="fbb78-148">Protocols supported by this hub.</span></span> <span data-ttu-id="fbb78-149">Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly.</span><span class="sxs-lookup"><span data-stu-id="fbb78-149">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="fbb78-150">Pokud `true`, jsou klientovi vraceny podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="fbb78-150">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="fbb78-151">Výchozí hodnota je `false`, protože tyto zprávy o výjimce mohou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="fbb78-151">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="fbb78-152">Maximální počet položek, které lze uložit do vyrovnávací paměti pro datové proudy pro odeslání klienta.</span><span class="sxs-lookup"><span data-stu-id="fbb78-152">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="fbb78-153">Pokud je dosaženo tohoto limitu, zpracování volání je blokováno, dokud server nezpracovává položky datového proudu.</span><span class="sxs-lookup"><span data-stu-id="fbb78-153">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="fbb78-154">32 KB</span><span class="sxs-lookup"><span data-stu-id="fbb78-154">32 KB</span></span> | <span data-ttu-id="fbb78-155">Maximální velikost jedné příchozí zprávy centra</span><span class="sxs-lookup"><span data-stu-id="fbb78-155">Maximum size of a single incoming hub message.</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.2"

| <span data-ttu-id="fbb78-156">Možnost</span><span class="sxs-lookup"><span data-stu-id="fbb78-156">Option</span></span> | <span data-ttu-id="fbb78-157">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="fbb78-157">Default Value</span></span> | <span data-ttu-id="fbb78-158">Popis</span><span class="sxs-lookup"><span data-stu-id="fbb78-158">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="fbb78-159">30 sekund</span><span class="sxs-lookup"><span data-stu-id="fbb78-159">30 seconds</span></span> | <span data-ttu-id="fbb78-160">Server bude předpokládat, že je klient odpojený, pokud v tomto intervalu neobdržel zprávu (včetně Keep-Alive).</span><span class="sxs-lookup"><span data-stu-id="fbb78-160">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="fbb78-161">Může trvat delší dobu, než je tento časový limit, aby bylo možné klienta ve skutečnosti označit jako odpojený, protože to je implementováno.</span><span class="sxs-lookup"><span data-stu-id="fbb78-161">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="fbb78-162">Doporučená hodnota je dvojnásobná hodnota `KeepAliveInterval`.</span><span class="sxs-lookup"><span data-stu-id="fbb78-162">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="fbb78-163">15 sekund</span><span class="sxs-lookup"><span data-stu-id="fbb78-163">15 seconds</span></span> | <span data-ttu-id="fbb78-164">Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře.</span><span class="sxs-lookup"><span data-stu-id="fbb78-164">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="fbb78-165">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="fbb78-165">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="fbb78-166">Další informace o procesu handshake najdete v tématu [specifikace protokoluSignalR hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="fbb78-166">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="fbb78-167">15 sekund</span><span class="sxs-lookup"><span data-stu-id="fbb78-167">15 seconds</span></span> | <span data-ttu-id="fbb78-168">Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="fbb78-168">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="fbb78-169">Při změně `KeepAliveInterval`změňte nastavení `ServerTimeout`/`serverTimeoutInMilliseconds` na klientovi.</span><span class="sxs-lookup"><span data-stu-id="fbb78-169">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="fbb78-170">Doporučená `ServerTimeout`/`serverTimeoutInMilliseconds` hodnota je dvojnásobek hodnoty `KeepAliveInterval`.</span><span class="sxs-lookup"><span data-stu-id="fbb78-170">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="fbb78-171">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="fbb78-171">All installed protocols</span></span> | <span data-ttu-id="fbb78-172">Protokoly podporované tímto rozbočovačem</span><span class="sxs-lookup"><span data-stu-id="fbb78-172">Protocols supported by this hub.</span></span> <span data-ttu-id="fbb78-173">Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly.</span><span class="sxs-lookup"><span data-stu-id="fbb78-173">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="fbb78-174">Pokud `true`, jsou klientovi vraceny podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="fbb78-174">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="fbb78-175">Výchozí hodnota je `false`, protože tyto zprávy o výjimce mohou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="fbb78-175">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| <span data-ttu-id="fbb78-176">Možnost</span><span class="sxs-lookup"><span data-stu-id="fbb78-176">Option</span></span> | <span data-ttu-id="fbb78-177">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="fbb78-177">Default Value</span></span> | <span data-ttu-id="fbb78-178">Popis</span><span class="sxs-lookup"><span data-stu-id="fbb78-178">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="fbb78-179">15 sekund</span><span class="sxs-lookup"><span data-stu-id="fbb78-179">15 seconds</span></span> | <span data-ttu-id="fbb78-180">Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře.</span><span class="sxs-lookup"><span data-stu-id="fbb78-180">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="fbb78-181">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="fbb78-181">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="fbb78-182">Další informace o procesu handshake najdete v tématu [specifikace protokoluSignalR hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="fbb78-182">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="fbb78-183">15 sekund</span><span class="sxs-lookup"><span data-stu-id="fbb78-183">15 seconds</span></span> | <span data-ttu-id="fbb78-184">Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="fbb78-184">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="fbb78-185">Při změně `KeepAliveInterval`změňte nastavení `ServerTimeout`/`serverTimeoutInMilliseconds` na klientovi.</span><span class="sxs-lookup"><span data-stu-id="fbb78-185">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="fbb78-186">Doporučená `ServerTimeout`/`serverTimeoutInMilliseconds` hodnota je dvojnásobek hodnoty `KeepAliveInterval`.</span><span class="sxs-lookup"><span data-stu-id="fbb78-186">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="fbb78-187">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="fbb78-187">All installed protocols</span></span> | <span data-ttu-id="fbb78-188">Protokoly podporované tímto rozbočovačem</span><span class="sxs-lookup"><span data-stu-id="fbb78-188">Protocols supported by this hub.</span></span> <span data-ttu-id="fbb78-189">Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly.</span><span class="sxs-lookup"><span data-stu-id="fbb78-189">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="fbb78-190">Pokud `true`, jsou klientovi vraceny podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="fbb78-190">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="fbb78-191">Výchozí hodnota je `false`, protože tyto zprávy o výjimce mohou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="fbb78-191">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

<span data-ttu-id="fbb78-192">Možnosti lze nakonfigurovat pro všechna centra tím, že poskytnete delegáty možností `AddSignalR` volání `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="fbb78-192">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddSignalR(hubOptions =>
    {
        hubOptions.EnableDetailedErrors = true;
        hubOptions.KeepAliveInterval = TimeSpan.FromMinutes(1);
    });
}
```

<span data-ttu-id="fbb78-193">Možnosti pro jeden rozbočovač přepíší globální možnosti poskytované v `AddSignalR` a dají se nakonfigurovat pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span><span class="sxs-lookup"><span data-stu-id="fbb78-193">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="fbb78-194">Rozšířené možnosti konfigurace protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="fbb78-194">Advanced HTTP configuration options</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fbb78-195">Pomocí `HttpConnectionDispatcherOptions` můžete nakonfigurovat upřesňující nastavení týkající se přenosů a správy vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="fbb78-195">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="fbb78-196">Tyto možnosti jsou nakonfigurovány předáním delegáta [MapHub\<t >](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="fbb78-196">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<MyHub>("/myhub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```
::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="fbb78-197">Pomocí `HttpConnectionDispatcherOptions` můžete nakonfigurovat upřesňující nastavení týkající se přenosů a správy vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="fbb78-197">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="fbb78-198">Tyto možnosti jsou nakonfigurovány předáním delegáta [MapHub\<t >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="fbb78-198">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<MyHub>("/myhub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

::: moniker-end

<span data-ttu-id="fbb78-199">Následující tabulka obsahuje popis možností konfigurace ASP.NET Core SignalRpokročilé možnosti protokolu HTTP:</span><span class="sxs-lookup"><span data-stu-id="fbb78-199">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="fbb78-200">Možnost</span><span class="sxs-lookup"><span data-stu-id="fbb78-200">Option</span></span> | <span data-ttu-id="fbb78-201">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="fbb78-201">Default Value</span></span> | <span data-ttu-id="fbb78-202">Popis</span><span class="sxs-lookup"><span data-stu-id="fbb78-202">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="fbb78-203">32 KB</span><span class="sxs-lookup"><span data-stu-id="fbb78-203">32 KB</span></span> | <span data-ttu-id="fbb78-204">Maximální počet bajtů přijatých od klienta, které jsou ukládány do vyrovnávací paměti serveru před použitím protitlaku.</span><span class="sxs-lookup"><span data-stu-id="fbb78-204">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="fbb78-205">Zvýšením této hodnoty umožníte, aby server přijímal větší zprávy rychleji bez použití zatížení, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="fbb78-205">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="fbb78-206">Data, která se automaticky shromažďují z atributů `Authorize` použitých pro třídu centra</span><span class="sxs-lookup"><span data-stu-id="fbb78-206">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="fbb78-207">Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru</span><span class="sxs-lookup"><span data-stu-id="fbb78-207">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="fbb78-208">32 KB</span><span class="sxs-lookup"><span data-stu-id="fbb78-208">32 KB</span></span> | <span data-ttu-id="fbb78-209">Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru před pozorováním protitlaku.</span><span class="sxs-lookup"><span data-stu-id="fbb78-209">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="fbb78-210">Zvýšením této hodnoty umožníte serveru rychleji ukládat větší zprávy bez nutnosti očekávat zatížení, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="fbb78-210">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="fbb78-211">Všechny přenosy jsou povolené.</span><span class="sxs-lookup"><span data-stu-id="fbb78-211">All Transports are enabled.</span></span> | <span data-ttu-id="fbb78-212">Bitové příznaky vyčíslují `HttpTransportType` hodnoty, které mohou omezit přenos, který může klient použít pro připojení.</span><span class="sxs-lookup"><span data-stu-id="fbb78-212">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="fbb78-213">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="fbb78-213">See below.</span></span> | <span data-ttu-id="fbb78-214">Další možnosti specifické pro přenos dlouhého cyklického dotazování.</span><span class="sxs-lookup"><span data-stu-id="fbb78-214">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="fbb78-215">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="fbb78-215">See below.</span></span> | <span data-ttu-id="fbb78-216">Další možnosti specifické pro přenos pomocí protokolu WebSockets</span><span class="sxs-lookup"><span data-stu-id="fbb78-216">Additional options specific to the WebSockets transport.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| <span data-ttu-id="fbb78-217">Možnost</span><span class="sxs-lookup"><span data-stu-id="fbb78-217">Option</span></span> | <span data-ttu-id="fbb78-218">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="fbb78-218">Default Value</span></span> | <span data-ttu-id="fbb78-219">Popis</span><span class="sxs-lookup"><span data-stu-id="fbb78-219">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="fbb78-220">32 KB</span><span class="sxs-lookup"><span data-stu-id="fbb78-220">32 KB</span></span> | <span data-ttu-id="fbb78-221">Maximální počet bajtů přijatých od klienta, které jsou vyrovnávací paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="fbb78-221">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="fbb78-222">Zvýšením této hodnoty umožníte serveru přijímat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="fbb78-222">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="fbb78-223">Data, která se automaticky shromažďují z atributů `Authorize` použitých pro třídu centra</span><span class="sxs-lookup"><span data-stu-id="fbb78-223">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="fbb78-224">Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru</span><span class="sxs-lookup"><span data-stu-id="fbb78-224">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="fbb78-225">32 KB</span><span class="sxs-lookup"><span data-stu-id="fbb78-225">32 KB</span></span> | <span data-ttu-id="fbb78-226">Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="fbb78-226">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="fbb78-227">Zvýšením této hodnoty umožníte serveru odesílat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="fbb78-227">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="fbb78-228">Všechny přenosy jsou povolené.</span><span class="sxs-lookup"><span data-stu-id="fbb78-228">All Transports are enabled.</span></span> | <span data-ttu-id="fbb78-229">Bitové příznaky vyčíslují `HttpTransportType` hodnoty, které mohou omezit přenos, který může klient použít pro připojení.</span><span class="sxs-lookup"><span data-stu-id="fbb78-229">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="fbb78-230">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="fbb78-230">See below.</span></span> | <span data-ttu-id="fbb78-231">Další možnosti specifické pro přenos dlouhého cyklického dotazování.</span><span class="sxs-lookup"><span data-stu-id="fbb78-231">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="fbb78-232">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="fbb78-232">See below.</span></span> | <span data-ttu-id="fbb78-233">Další možnosti specifické pro přenos pomocí protokolu WebSockets</span><span class="sxs-lookup"><span data-stu-id="fbb78-233">Additional options specific to the WebSockets transport.</span></span> |

::: moniker-end

<span data-ttu-id="fbb78-234">Přenos dlouhého cyklického dotazování má další možnosti, které je možné konfigurovat pomocí vlastnosti `LongPolling`:</span><span class="sxs-lookup"><span data-stu-id="fbb78-234">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="fbb78-235">Možnost</span><span class="sxs-lookup"><span data-stu-id="fbb78-235">Option</span></span> | <span data-ttu-id="fbb78-236">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="fbb78-236">Default Value</span></span> | <span data-ttu-id="fbb78-237">Popis</span><span class="sxs-lookup"><span data-stu-id="fbb78-237">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="fbb78-238">90 sekund</span><span class="sxs-lookup"><span data-stu-id="fbb78-238">90 seconds</span></span> | <span data-ttu-id="fbb78-239">Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jedné žádosti o cyklické dotazování.</span><span class="sxs-lookup"><span data-stu-id="fbb78-239">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="fbb78-240">Snížení této hodnoty způsobí, že klient bude vydávat nové požadavky na dotaz častěji.</span><span class="sxs-lookup"><span data-stu-id="fbb78-240">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="fbb78-241">Přenos přes protokol WebSocket má další možnosti, které je možné konfigurovat pomocí vlastnosti `WebSockets`:</span><span class="sxs-lookup"><span data-stu-id="fbb78-241">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="fbb78-242">Možnost</span><span class="sxs-lookup"><span data-stu-id="fbb78-242">Option</span></span> | <span data-ttu-id="fbb78-243">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="fbb78-243">Default Value</span></span> | <span data-ttu-id="fbb78-244">Popis</span><span class="sxs-lookup"><span data-stu-id="fbb78-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="fbb78-245">5 sekund</span><span class="sxs-lookup"><span data-stu-id="fbb78-245">5 seconds</span></span> | <span data-ttu-id="fbb78-246">Pokud se po ukončení serveru aplikace v tomto časovém intervalu nepovede zavřít, připojení se ukončí.</span><span class="sxs-lookup"><span data-stu-id="fbb78-246">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="fbb78-247">Delegát, který lze použít k nastavení záhlaví `Sec-WebSocket-Protocol` na vlastní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="fbb78-247">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="fbb78-248">Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že se vrátí požadovaná hodnota.</span><span class="sxs-lookup"><span data-stu-id="fbb78-248">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="fbb78-249">Konfigurace možností klienta</span><span class="sxs-lookup"><span data-stu-id="fbb78-249">Configure client options</span></span>

<span data-ttu-id="fbb78-250">Možnosti klienta lze nakonfigurovat u `HubConnectionBuilder`ho typu (k dispozici v klientech rozhraní .NET a v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="fbb78-250">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="fbb78-251">Je také k dispozici v klientovi Java, ale `HttpHubConnectionBuilder` podtřídou je to, co obsahuje možnosti konfigurace tvůrce a také na `HubConnection` sám.</span><span class="sxs-lookup"><span data-stu-id="fbb78-251">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="fbb78-252">Konfigurovat protokolování</span><span class="sxs-lookup"><span data-stu-id="fbb78-252">Configure logging</span></span>

<span data-ttu-id="fbb78-253">Protokolování je konfigurováno v klientovi .NET pomocí metody `ConfigureLogging`.</span><span class="sxs-lookup"><span data-stu-id="fbb78-253">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="fbb78-254">Zprostředkovatelé protokolování a filtry je možné zaregistrovat stejným způsobem jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="fbb78-254">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="fbb78-255">Další informace najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="fbb78-255">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="fbb78-256">Aby bylo možné registrovat poskytovatele protokolování, je nutné nainstalovat potřebné balíčky.</span><span class="sxs-lookup"><span data-stu-id="fbb78-256">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="fbb78-257">Úplný seznam najdete v části [předdefinovaná zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="fbb78-257">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="fbb78-258">Chcete-li například povolit protokolování konzoly, nainstalujte balíček `Microsoft.Extensions.Logging.Console` NuGet.</span><span class="sxs-lookup"><span data-stu-id="fbb78-258">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="fbb78-259">Zavolejte metodu rozšíření `AddConsole`:</span><span class="sxs-lookup"><span data-stu-id="fbb78-259">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="fbb78-260">V klientovi jazyka JavaScript existuje podobná `configureLogging` metoda.</span><span class="sxs-lookup"><span data-stu-id="fbb78-260">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="fbb78-261">Zadejte `LogLevel`ovou hodnotu označující minimální úroveň zpráv protokolu, které se mají vytvořit.</span><span class="sxs-lookup"><span data-stu-id="fbb78-261">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="fbb78-262">Protokoly se zapisují do okna konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="fbb78-262">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fbb78-263">Místo `LogLevel` hodnoty můžete zadat také hodnotu `string` reprezentující název úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="fbb78-263">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="fbb78-264">To je užitečné při konfiguraci SignalR protokolování v prostředích, kde nemáte přístup k konstantám `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="fbb78-264">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="fbb78-265">V následující tabulce jsou uvedeny dostupné úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="fbb78-265">The following table lists the available log levels.</span></span> <span data-ttu-id="fbb78-266">Hodnota, kterou zadáte, `configureLogging` nastaví **minimální** úroveň protokolování, která se bude protokolovat.</span><span class="sxs-lookup"><span data-stu-id="fbb78-266">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="fbb78-267">Zprávy zaznamenané na této úrovni **nebo úrovně uvedené za ní v tabulce**budou protokolovány.</span><span class="sxs-lookup"><span data-stu-id="fbb78-267">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="fbb78-268">String</span><span class="sxs-lookup"><span data-stu-id="fbb78-268">String</span></span>                      | <span data-ttu-id="fbb78-269">LogLevel</span><span class="sxs-lookup"><span data-stu-id="fbb78-269">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="fbb78-270">`info` **nebo** `information`</span><span class="sxs-lookup"><span data-stu-id="fbb78-270">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="fbb78-271">`warn` **nebo** `warning`</span><span class="sxs-lookup"><span data-stu-id="fbb78-271">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

::: moniker-end

> [!NOTE]
> <span data-ttu-id="fbb78-272">Pokud chcete protokolování zcela zakázat, zadejte `signalR.LogLevel.None` v metodě `configureLogging`.</span><span class="sxs-lookup"><span data-stu-id="fbb78-272">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="fbb78-273">Další informace o protokolování najdete v dokumentaci k [diagnosticeSignalR](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="fbb78-273">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="fbb78-274">SignalR klient Java používá k protokolování knihovnu [SLF4J](https://www.slf4j.org/) .</span><span class="sxs-lookup"><span data-stu-id="fbb78-274">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="fbb78-275">Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí.</span><span class="sxs-lookup"><span data-stu-id="fbb78-275">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="fbb78-276">Následující fragment kódu ukazuje, jak použít `java.util.logging` s klientem SignalR Java.</span><span class="sxs-lookup"><span data-stu-id="fbb78-276">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="fbb78-277">Pokud ve svých závislostech nenakonfigurujete protokolování, SLF4J načte výchozí protokolovací nástroj No-operation s následující zprávou upozornění:</span><span class="sxs-lookup"><span data-stu-id="fbb78-277">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="fbb78-278">Tuto možnost lze bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="fbb78-278">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="fbb78-279">Konfigurace povolených přenosů</span><span class="sxs-lookup"><span data-stu-id="fbb78-279">Configure allowed transports</span></span>

<span data-ttu-id="fbb78-280">Transporty používané SignalR lze konfigurovat v volání `WithUrl` (`withUrl` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="fbb78-280">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="fbb78-281">Bitové nebo hodnoty `HttpTransportType` lze použít k omezení klienta na používání pouze určených přenosů.</span><span class="sxs-lookup"><span data-stu-id="fbb78-281">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="fbb78-282">Ve výchozím nastavení jsou povolené všechny přenosy.</span><span class="sxs-lookup"><span data-stu-id="fbb78-282">All transports are enabled by default.</span></span>

<span data-ttu-id="fbb78-283">Pokud třeba chcete zakázat přenos událostí odeslaných serverem, ale povolit WebSockets a dlouhá připojení s dotazem:</span><span class="sxs-lookup"><span data-stu-id="fbb78-283">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="fbb78-284">V klientu jazyka JavaScript jsou přenosy konfigurovány nastavením pole `transport` v objektu Options, který je k dispozici pro `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="fbb78-284">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="fbb78-285">V této verzi je jediným dostupným přenosem klientský WebSocket v jazyce Java.</span><span class="sxs-lookup"><span data-stu-id="fbb78-285">In this version of the Java client websockets is the only available transport.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="fbb78-286">V klientovi Java se Transport vybere pomocí metody `withTransport` v `HttpHubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="fbb78-286">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="fbb78-287">Klient Java standardně používá přenos pomocí protokolu WebSockets.</span><span class="sxs-lookup"><span data-stu-id="fbb78-287">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="fbb78-288">Klient nástroje SignalR Java nepodporuje ještě záložní přenos.</span><span class="sxs-lookup"><span data-stu-id="fbb78-288">The SignalR Java client doesn't support transport fallback yet.</span></span>

::: moniker-end

### <a name="configure-bearer-authentication"></a><span data-ttu-id="fbb78-289">Konfigurace ověřování nosiče</span><span class="sxs-lookup"><span data-stu-id="fbb78-289">Configure bearer authentication</span></span>

<span data-ttu-id="fbb78-290">K poskytnutí ověřovacích dat spolu s požadavky SignalR použijte možnost `AccessTokenProvider` (`accessTokenFactory` v JavaScriptu) k určení funkce, která vrací požadovaný přístupový token.</span><span class="sxs-lookup"><span data-stu-id="fbb78-290">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="fbb78-291">V klientovi .NET se tento přístupový token předává jako token ověřování HTTP "Bearer" (pomocí `Authorization` záhlaví s typem `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="fbb78-291">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="fbb78-292">V klientu jazyka JavaScript se přístupový token používá jako nosný token, **s výjimkou** případů, kdy rozhraní API prohlížeče omezuje možnost použít hlavičky (konkrétně v požadavcích na události odeslané serverem a objekty WebSockets).</span><span class="sxs-lookup"><span data-stu-id="fbb78-292">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="fbb78-293">V těchto případech je přístupový token k dispozici jako hodnota řetězce dotazu `access_token`.</span><span class="sxs-lookup"><span data-stu-id="fbb78-293">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="fbb78-294">V klientu .NET lze zadat možnost `AccessTokenProvider` pomocí delegáta možností v `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="fbb78-294">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="fbb78-295">V klientovi JavaScriptu je přístupový token nakonfigurovaný nastavením pole `accessTokenFactory` u objektu Options v `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="fbb78-295">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="fbb78-296">V SignalR klienta Java můžete nakonfigurovat nosný token, který se má použít k ověřování tím, že poskytne továrnu přístupového tokenu [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="fbb78-296">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="fbb78-297">Použijte [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) k poskytnutí [> jednoho\<ového řetězce](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava) .</span><span class="sxs-lookup"><span data-stu-id="fbb78-297">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="fbb78-298">Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.</span><span class="sxs-lookup"><span data-stu-id="fbb78-298">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="fbb78-299">Konfigurace možností timeout a Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="fbb78-299">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="fbb78-300">Další možnosti konfigurace časového limitu a chování při udržování připojení jsou k dispozici na samotném objektu `HubConnection`:</span><span class="sxs-lookup"><span data-stu-id="fbb78-300">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>


::: moniker range=">= aspnetcore-2.2"

# <a name="nettabdotnet"></a>[<span data-ttu-id="fbb78-301">.NET</span><span class="sxs-lookup"><span data-stu-id="fbb78-301">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="fbb78-302">Možnost</span><span class="sxs-lookup"><span data-stu-id="fbb78-302">Option</span></span> | <span data-ttu-id="fbb78-303">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="fbb78-303">Default value</span></span> | <span data-ttu-id="fbb78-304">Popis</span><span class="sxs-lookup"><span data-stu-id="fbb78-304">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="fbb78-305">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="fbb78-305">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="fbb78-306">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="fbb78-306">Timeout for server activity.</span></span> <span data-ttu-id="fbb78-307">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí událost `Closed` (`onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="fbb78-307">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="fbb78-308">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="fbb78-308">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="fbb78-309">Doporučená hodnota je číslo minimálně dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy k zadání času.</span><span class="sxs-lookup"><span data-stu-id="fbb78-309">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="fbb78-310">15 sekund</span><span class="sxs-lookup"><span data-stu-id="fbb78-310">15 seconds</span></span> | <span data-ttu-id="fbb78-311">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="fbb78-311">Timeout for initial server handshake.</span></span> <span data-ttu-id="fbb78-312">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje událost `Closed` (`onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="fbb78-312">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="fbb78-313">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="fbb78-313">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="fbb78-314">Další informace o procesu handshake najdete v tématu [specifikace protokoluSignalR hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="fbb78-314">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="fbb78-315">15 sekund</span><span class="sxs-lookup"><span data-stu-id="fbb78-315">15 seconds</span></span> | <span data-ttu-id="fbb78-316">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="fbb78-316">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="fbb78-317">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="fbb78-317">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="fbb78-318">Pokud klient neodeslal zprávu ve `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="fbb78-318">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="fbb78-319">V klientovi .NET jsou hodnoty časového limitu zadány jako `TimeSpan` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="fbb78-319">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="fbb78-320">JavaScript</span><span class="sxs-lookup"><span data-stu-id="fbb78-320">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="fbb78-321">Možnost</span><span class="sxs-lookup"><span data-stu-id="fbb78-321">Option</span></span> | <span data-ttu-id="fbb78-322">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="fbb78-322">Default value</span></span> | <span data-ttu-id="fbb78-323">Popis</span><span class="sxs-lookup"><span data-stu-id="fbb78-323">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="fbb78-324">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="fbb78-324">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="fbb78-325">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="fbb78-325">Timeout for server activity.</span></span> <span data-ttu-id="fbb78-326">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí událost `onclose`.</span><span class="sxs-lookup"><span data-stu-id="fbb78-326">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="fbb78-327">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="fbb78-327">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="fbb78-328">Doporučená hodnota je číslo minimálně dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy k zadání času.</span><span class="sxs-lookup"><span data-stu-id="fbb78-328">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="fbb78-329">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="fbb78-329">15 seconds (15,000 miliseconds)</span></span> | <span data-ttu-id="fbb78-330">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="fbb78-330">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="fbb78-331">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="fbb78-331">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="fbb78-332">Pokud klient neodeslal zprávu ve `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="fbb78-332">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="fbb78-333">Java</span><span class="sxs-lookup"><span data-stu-id="fbb78-333">Java</span></span>](#tab/java)

| <span data-ttu-id="fbb78-334">Možnost</span><span class="sxs-lookup"><span data-stu-id="fbb78-334">Option</span></span> | <span data-ttu-id="fbb78-335">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="fbb78-335">Default value</span></span> | <span data-ttu-id="fbb78-336">Popis</span><span class="sxs-lookup"><span data-stu-id="fbb78-336">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="fbb78-337">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="fbb78-337">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="fbb78-338">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="fbb78-338">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="fbb78-339">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="fbb78-339">Timeout for server activity.</span></span> <span data-ttu-id="fbb78-340">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí událost `onClose`.</span><span class="sxs-lookup"><span data-stu-id="fbb78-340">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="fbb78-341">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="fbb78-341">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="fbb78-342">Doporučená hodnota je číslo minimálně dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy k zadání času.</span><span class="sxs-lookup"><span data-stu-id="fbb78-342">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="fbb78-343">15 sekund</span><span class="sxs-lookup"><span data-stu-id="fbb78-343">15 seconds</span></span> | <span data-ttu-id="fbb78-344">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="fbb78-344">Timeout for initial server handshake.</span></span> <span data-ttu-id="fbb78-345">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje událost `onClose`.</span><span class="sxs-lookup"><span data-stu-id="fbb78-345">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="fbb78-346">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="fbb78-346">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="fbb78-347">Další informace o procesu handshake najdete v tématu [specifikace protokoluSignalR hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="fbb78-347">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="fbb78-348">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="fbb78-348">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="fbb78-349">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="fbb78-349">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="fbb78-350">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="fbb78-350">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="fbb78-351">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="fbb78-351">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="fbb78-352">Pokud klient neodeslal zprávu ve `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="fbb78-352">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="nettabdotnet"></a>[<span data-ttu-id="fbb78-353">.NET</span><span class="sxs-lookup"><span data-stu-id="fbb78-353">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="fbb78-354">Možnost</span><span class="sxs-lookup"><span data-stu-id="fbb78-354">Option</span></span> | <span data-ttu-id="fbb78-355">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="fbb78-355">Default value</span></span> | <span data-ttu-id="fbb78-356">Popis</span><span class="sxs-lookup"><span data-stu-id="fbb78-356">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="fbb78-357">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="fbb78-357">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="fbb78-358">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="fbb78-358">Timeout for server activity.</span></span> <span data-ttu-id="fbb78-359">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí událost `Closed` (`onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="fbb78-359">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="fbb78-360">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="fbb78-360">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="fbb78-361">Doporučená hodnota je číslo minimálně dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy k zadání času.</span><span class="sxs-lookup"><span data-stu-id="fbb78-361">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="fbb78-362">15 sekund</span><span class="sxs-lookup"><span data-stu-id="fbb78-362">15 seconds</span></span> | <span data-ttu-id="fbb78-363">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="fbb78-363">Timeout for initial server handshake.</span></span> <span data-ttu-id="fbb78-364">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje událost `Closed` (`onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="fbb78-364">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="fbb78-365">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="fbb78-365">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="fbb78-366">Další informace o procesu handshake najdete v tématu [specifikace protokoluSignalR hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="fbb78-366">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="fbb78-367">V klientovi .NET jsou hodnoty časového limitu zadány jako `TimeSpan` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="fbb78-367">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="fbb78-368">JavaScript</span><span class="sxs-lookup"><span data-stu-id="fbb78-368">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="fbb78-369">Možnost</span><span class="sxs-lookup"><span data-stu-id="fbb78-369">Option</span></span> | <span data-ttu-id="fbb78-370">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="fbb78-370">Default value</span></span> | <span data-ttu-id="fbb78-371">Popis</span><span class="sxs-lookup"><span data-stu-id="fbb78-371">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="fbb78-372">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="fbb78-372">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="fbb78-373">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="fbb78-373">Timeout for server activity.</span></span> <span data-ttu-id="fbb78-374">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí událost `onclose`.</span><span class="sxs-lookup"><span data-stu-id="fbb78-374">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="fbb78-375">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="fbb78-375">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="fbb78-376">Doporučená hodnota je číslo minimálně dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy k zadání času.</span><span class="sxs-lookup"><span data-stu-id="fbb78-376">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="fbb78-377">Java</span><span class="sxs-lookup"><span data-stu-id="fbb78-377">Java</span></span>](#tab/java)

| <span data-ttu-id="fbb78-378">Možnost</span><span class="sxs-lookup"><span data-stu-id="fbb78-378">Option</span></span> | <span data-ttu-id="fbb78-379">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="fbb78-379">Default value</span></span> | <span data-ttu-id="fbb78-380">Popis</span><span class="sxs-lookup"><span data-stu-id="fbb78-380">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="fbb78-381">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="fbb78-381">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="fbb78-382">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="fbb78-382">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="fbb78-383">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="fbb78-383">Timeout for server activity.</span></span> <span data-ttu-id="fbb78-384">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí událost `onClose`.</span><span class="sxs-lookup"><span data-stu-id="fbb78-384">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="fbb78-385">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="fbb78-385">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="fbb78-386">Doporučená hodnota je číslo minimálně dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy k zadání času.</span><span class="sxs-lookup"><span data-stu-id="fbb78-386">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="fbb78-387">15 sekund</span><span class="sxs-lookup"><span data-stu-id="fbb78-387">15 seconds</span></span> | <span data-ttu-id="fbb78-388">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="fbb78-388">Timeout for initial server handshake.</span></span> <span data-ttu-id="fbb78-389">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje událost `onClose`.</span><span class="sxs-lookup"><span data-stu-id="fbb78-389">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="fbb78-390">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="fbb78-390">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="fbb78-391">Další informace o procesu handshake najdete v tématu [specifikace protokoluSignalR hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="fbb78-391">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

::: moniker-end

---

### <a name="configure-additional-options"></a><span data-ttu-id="fbb78-392">Konfigurovat další možnosti</span><span class="sxs-lookup"><span data-stu-id="fbb78-392">Configure additional options</span></span>

<span data-ttu-id="fbb78-393">Další možnosti lze nakonfigurovat v metodě `WithUrl` (`withUrl` in JavaScript) na `HubConnectionBuilder` nebo na různých konfiguračních rozhraních API `HttpHubConnectionBuilder` v klientovi Java:</span><span class="sxs-lookup"><span data-stu-id="fbb78-393">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="fbb78-394">.NET</span><span class="sxs-lookup"><span data-stu-id="fbb78-394">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="fbb78-395">Možnost .NET</span><span class="sxs-lookup"><span data-stu-id="fbb78-395">.NET Option</span></span> |  <span data-ttu-id="fbb78-396">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="fbb78-396">Default value</span></span> | <span data-ttu-id="fbb78-397">Popis</span><span class="sxs-lookup"><span data-stu-id="fbb78-397">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="fbb78-398">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="fbb78-398">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="fbb78-399">Nastavte tuto hodnotu na `true`, aby se přeskočil krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="fbb78-399">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="fbb78-400">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="fbb78-400">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="fbb78-401">Toto nastavení se nedá povolit při použití služby Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="fbb78-401">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="fbb78-402">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="fbb78-402">Empty</span></span> | <span data-ttu-id="fbb78-403">Kolekce certifikátů TLS pro odeslání na požadavky na ověření.</span><span class="sxs-lookup"><span data-stu-id="fbb78-403">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="fbb78-404">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="fbb78-404">Empty</span></span> | <span data-ttu-id="fbb78-405">Kolekce souborů cookie protokolu HTTP, které se mají odeslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="fbb78-405">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="fbb78-406">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="fbb78-406">Empty</span></span> | <span data-ttu-id="fbb78-407">Přihlašovací údaje, které se mají poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="fbb78-407">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="fbb78-408">5 sekund</span><span class="sxs-lookup"><span data-stu-id="fbb78-408">5 seconds</span></span> | <span data-ttu-id="fbb78-409">Jenom objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="fbb78-409">WebSockets only.</span></span> <span data-ttu-id="fbb78-410">Maximální doba, po jejímž uplynutí bude klient čekat na ukončení žádosti o uzavření.</span><span class="sxs-lookup"><span data-stu-id="fbb78-410">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="fbb78-411">Pokud server nepotvrdí zavření v této době, klient se odpojí.</span><span class="sxs-lookup"><span data-stu-id="fbb78-411">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="fbb78-412">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="fbb78-412">Empty</span></span> | <span data-ttu-id="fbb78-413">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="fbb78-413">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="fbb78-414">Delegát, který lze použít ke konfiguraci nebo nahrazení `HttpMessageHandler` používaných k odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="fbb78-414">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="fbb78-415">Nepoužívá se pro připojení pomocí protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="fbb78-415">Not used for WebSocket connections.</span></span> <span data-ttu-id="fbb78-416">Tento delegát musí vracet hodnotu, která není null, a obdrží výchozí hodnotu jako parametr.</span><span class="sxs-lookup"><span data-stu-id="fbb78-416">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="fbb78-417">Buď změňte nastavení pro tuto výchozí hodnotu a vraťte je, nebo vraťte novou instanci `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="fbb78-417">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="fbb78-418">**Při nahrazování obslužné rutiny Nezapomeňte zkopírovat nastavení, která chcete zachovat, od poskytnuté obslužné rutiny, jinak konfigurované možnosti (například soubory cookie a záhlaví) nebudou použity pro novou obslužnou rutinu.**</span><span class="sxs-lookup"><span data-stu-id="fbb78-418">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="fbb78-419">Proxy server HTTP, který se má použít při odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="fbb78-419">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="fbb78-420">Nastavte tuto logickou hodnotu pro odeslání výchozích přihlašovacích údajů pro požadavky HTTP a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="fbb78-420">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="fbb78-421">To umožňuje použití ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="fbb78-421">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="fbb78-422">Delegát, který lze použít ke konfiguraci dalších možností protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="fbb78-422">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="fbb78-423">Přijímá instanci [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , která se dá použít ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="fbb78-423">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="fbb78-424">JavaScript</span><span class="sxs-lookup"><span data-stu-id="fbb78-424">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="fbb78-425">Možnost JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="fbb78-425">JavaScript Option</span></span> | <span data-ttu-id="fbb78-426">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="fbb78-426">Default Value</span></span> | <span data-ttu-id="fbb78-427">Popis</span><span class="sxs-lookup"><span data-stu-id="fbb78-427">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="fbb78-428">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="fbb78-428">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="fbb78-429">Nastavte tuto hodnotu na `true`, aby se přeskočil krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="fbb78-429">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="fbb78-430">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="fbb78-430">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="fbb78-431">Toto nastavení se nedá povolit při použití služby Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="fbb78-431">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="fbb78-432">Java</span><span class="sxs-lookup"><span data-stu-id="fbb78-432">Java</span></span>](#tab/java)

| <span data-ttu-id="fbb78-433">Možnost jazyka Java</span><span class="sxs-lookup"><span data-stu-id="fbb78-433">Java Option</span></span> | <span data-ttu-id="fbb78-434">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="fbb78-434">Default Value</span></span> | <span data-ttu-id="fbb78-435">Popis</span><span class="sxs-lookup"><span data-stu-id="fbb78-435">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="fbb78-436">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="fbb78-436">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="fbb78-437">Nastavte tuto hodnotu na `true`, aby se přeskočil krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="fbb78-437">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="fbb78-438">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="fbb78-438">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="fbb78-439">Toto nastavení se nedá povolit při použití služby Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="fbb78-439">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="fbb78-440">`withHeader``withHeaders`</span><span class="sxs-lookup"><span data-stu-id="fbb78-440">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="fbb78-441">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="fbb78-441">Empty</span></span> | <span data-ttu-id="fbb78-442">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="fbb78-442">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="fbb78-443">V klientu .NET můžete tyto možnosti upravit pomocí delegáta možností, který je k dispozici pro `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="fbb78-443">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="fbb78-444">V klientovi JavaScriptu lze tyto možnosti poskytnout v objektu jazyka JavaScript, který je k dispozici pro `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="fbb78-444">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="fbb78-445">V klientovi Java je možné nakonfigurovat tyto možnosti pomocí metod `HttpHubConnectionBuilder` vrácených z `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="fbb78-445">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="fbb78-446">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="fbb78-446">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>
