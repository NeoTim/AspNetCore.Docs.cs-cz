---
title: Konfigurace signalizace ASP.NET Core
author: bradygaster
description: Naučte se konfigurovat aplikace pro signalizaci ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 08/05/2019
uid: signalr/configuration
ms.openlocfilehash: 475d9664c588c06bfcd816959be8a425ee01c023
ms.sourcegitcommit: 776367717e990bdd600cb3c9148ffb905d56862d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68915086"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="3fc3d-103">Konfigurace signalizace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="3fc3d-103">ASP.NET Core SignalR configuration</span></span>

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="3fc3d-104">Možnosti serializace JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="3fc3d-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="3fc3d-105">ASP.NET Core Signal podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="3fc3d-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="3fc3d-106">Každý protokol má možnosti konfigurace serializace.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="3fc3d-107">Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , kterou lze přidat po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v `Startup.ConfigureServices` metodě.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="3fc3d-108">Metoda přebírá delegáta, který `options` přijímá objekt. `AddJsonProtocol`</span><span class="sxs-lookup"><span data-stu-id="3fc3d-108">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="3fc3d-109">Vlastnost [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) tohoto objektu je objekt JSON.NET `JsonSerializerSettings` , který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-109">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="3fc3d-110">Další podrobnosti najdete v [dokumentaci k JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm) .</span><span class="sxs-lookup"><span data-stu-id="3fc3d-110">See the [JSON.NET Documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm) for more details.</span></span>

<span data-ttu-id="3fc3d-111">Chcete-li například nakonfigurovat serializátor pro použití názvů vlastností "PascalCase" namísto výchozích názvů "camelCase", použijte následující kód:</span><span class="sxs-lookup"><span data-stu-id="3fc3d-111">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
        new DefaultContractResolver();
    });
```

<span data-ttu-id="3fc3d-112">V klientovi .NET existuje stejná `AddJsonProtocol` rozšiřující metoda v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="3fc3d-112">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="3fc3d-113">`Microsoft.Extensions.DependencyInjection` Obor názvů musí být importován, aby bylo možné přeložit metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="3fc3d-113">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="3fc3d-114">V tuto chvíli není možné konfigurovat serializaci JSON v klientovi jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-114">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="3fc3d-115">Možnosti serializace MessagePack</span><span class="sxs-lookup"><span data-stu-id="3fc3d-115">MessagePack serialization options</span></span>

<span data-ttu-id="3fc3d-116">Serializaci MessagePack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="3fc3d-116">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="3fc3d-117">Další podrobnosti najdete [v tématu MessagePack v nástroji Signal](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="3fc3d-117">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="3fc3d-118">V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi v jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-118">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="3fc3d-119">Konfigurovat možnosti serveru</span><span class="sxs-lookup"><span data-stu-id="3fc3d-119">Configure server options</span></span>

<span data-ttu-id="3fc3d-120">Následující tabulka obsahuje popis možností konfigurace Center pro signály:</span><span class="sxs-lookup"><span data-stu-id="3fc3d-120">The following table describes options for configuring SignalR hubs:</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="3fc3d-121">Možnost</span><span class="sxs-lookup"><span data-stu-id="3fc3d-121">Option</span></span> | <span data-ttu-id="3fc3d-122">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="3fc3d-122">Default Value</span></span> | <span data-ttu-id="3fc3d-123">Popis</span><span class="sxs-lookup"><span data-stu-id="3fc3d-123">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="3fc3d-124">30 sekund</span><span class="sxs-lookup"><span data-stu-id="3fc3d-124">30 seconds</span></span> | <span data-ttu-id="3fc3d-125">Server bude předpokládat, že je klient odpojený, pokud v tomto intervalu neobdržel zprávu (včetně Keep-Alive).</span><span class="sxs-lookup"><span data-stu-id="3fc3d-125">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="3fc3d-126">Může trvat delší dobu, než je tento časový limit, aby bylo možné klienta ve skutečnosti označit jako odpojený, protože to je implementováno.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-126">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="3fc3d-127">Doporučená hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-127">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="3fc3d-128">15 sekund</span><span class="sxs-lookup"><span data-stu-id="3fc3d-128">15 seconds</span></span> | <span data-ttu-id="3fc3d-129">Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-129">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="3fc3d-130">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-130">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="3fc3d-131">Další informace o procesu handshake najdete v tématu [specifikace protokolu centra signalizace](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="3fc3d-131">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="3fc3d-132">15 sekund</span><span class="sxs-lookup"><span data-stu-id="3fc3d-132">15 seconds</span></span> | <span data-ttu-id="3fc3d-133">Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-133">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="3fc3d-134">Při změně `KeepAliveInterval` `ServerTimeout` nastavení/ změňte nastavení v klientovi. `serverTimeoutInMilliseconds`</span><span class="sxs-lookup"><span data-stu-id="3fc3d-134">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="3fc3d-135">Doporučená `ServerTimeout` / hodnota je dvojnásobná`KeepAliveInterval` hodnota. `serverTimeoutInMilliseconds`</span><span class="sxs-lookup"><span data-stu-id="3fc3d-135">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="3fc3d-136">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="3fc3d-136">All installed protocols</span></span> | <span data-ttu-id="3fc3d-137">Protokoly podporované tímto rozbočovačem</span><span class="sxs-lookup"><span data-stu-id="3fc3d-137">Protocols supported by this hub.</span></span> <span data-ttu-id="3fc3d-138">Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-138">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="3fc3d-139">Pokud `true`se do klientů vrátí podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-139">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="3fc3d-140">Výchozí hodnota je `false`, protože tyto zprávy o výjimce můžou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-140">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="3fc3d-141">Maximální počet položek, které lze uložit do vyrovnávací paměti pro datové proudy pro odeslání klienta.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-141">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="3fc3d-142">Pokud je dosaženo tohoto limitu, zpracování volání je blokováno, dokud server nezpracovává položky datového proudu.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-142">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="3fc3d-143">32 KB</span><span class="sxs-lookup"><span data-stu-id="3fc3d-143">32 KB</span></span> | <span data-ttu-id="3fc3d-144">Maximální velikost jedné příchozí zprávy centra</span><span class="sxs-lookup"><span data-stu-id="3fc3d-144">Maximum size of a single incoming hub message.</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.2"

| <span data-ttu-id="3fc3d-145">Možnost</span><span class="sxs-lookup"><span data-stu-id="3fc3d-145">Option</span></span> | <span data-ttu-id="3fc3d-146">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="3fc3d-146">Default Value</span></span> | <span data-ttu-id="3fc3d-147">Popis</span><span class="sxs-lookup"><span data-stu-id="3fc3d-147">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="3fc3d-148">30 sekund</span><span class="sxs-lookup"><span data-stu-id="3fc3d-148">30 seconds</span></span> | <span data-ttu-id="3fc3d-149">Server bude předpokládat, že je klient odpojený, pokud v tomto intervalu neobdržel zprávu (včetně Keep-Alive).</span><span class="sxs-lookup"><span data-stu-id="3fc3d-149">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="3fc3d-150">Může trvat delší dobu, než je tento časový limit, aby bylo možné klienta ve skutečnosti označit jako odpojený, protože to je implementováno.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-150">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="3fc3d-151">Doporučená hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-151">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="3fc3d-152">15 sekund</span><span class="sxs-lookup"><span data-stu-id="3fc3d-152">15 seconds</span></span> | <span data-ttu-id="3fc3d-153">Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-153">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="3fc3d-154">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-154">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="3fc3d-155">Další informace o procesu handshake najdete v tématu [specifikace protokolu centra signalizace](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="3fc3d-155">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="3fc3d-156">15 sekund</span><span class="sxs-lookup"><span data-stu-id="3fc3d-156">15 seconds</span></span> | <span data-ttu-id="3fc3d-157">Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-157">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="3fc3d-158">Při změně `KeepAliveInterval` `ServerTimeout` nastavení/ změňte nastavení v klientovi. `serverTimeoutInMilliseconds`</span><span class="sxs-lookup"><span data-stu-id="3fc3d-158">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="3fc3d-159">Doporučená `ServerTimeout` / hodnota je dvojnásobná`KeepAliveInterval` hodnota. `serverTimeoutInMilliseconds`</span><span class="sxs-lookup"><span data-stu-id="3fc3d-159">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="3fc3d-160">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="3fc3d-160">All installed protocols</span></span> | <span data-ttu-id="3fc3d-161">Protokoly podporované tímto rozbočovačem</span><span class="sxs-lookup"><span data-stu-id="3fc3d-161">Protocols supported by this hub.</span></span> <span data-ttu-id="3fc3d-162">Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-162">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="3fc3d-163">Pokud `true`se do klientů vrátí podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-163">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="3fc3d-164">Výchozí hodnota je `false`, protože tyto zprávy o výjimce můžou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-164">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| <span data-ttu-id="3fc3d-165">Možnost</span><span class="sxs-lookup"><span data-stu-id="3fc3d-165">Option</span></span> | <span data-ttu-id="3fc3d-166">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="3fc3d-166">Default Value</span></span> | <span data-ttu-id="3fc3d-167">Popis</span><span class="sxs-lookup"><span data-stu-id="3fc3d-167">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="3fc3d-168">15 sekund</span><span class="sxs-lookup"><span data-stu-id="3fc3d-168">15 seconds</span></span> | <span data-ttu-id="3fc3d-169">Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-169">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="3fc3d-170">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-170">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="3fc3d-171">Další informace o procesu handshake najdete v tématu [specifikace protokolu centra signalizace](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="3fc3d-171">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="3fc3d-172">15 sekund</span><span class="sxs-lookup"><span data-stu-id="3fc3d-172">15 seconds</span></span> | <span data-ttu-id="3fc3d-173">Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-173">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="3fc3d-174">Při změně `KeepAliveInterval` `ServerTimeout` nastavení/ změňte nastavení v klientovi. `serverTimeoutInMilliseconds`</span><span class="sxs-lookup"><span data-stu-id="3fc3d-174">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="3fc3d-175">Doporučená `ServerTimeout` / hodnota je dvojnásobná`KeepAliveInterval` hodnota. `serverTimeoutInMilliseconds`</span><span class="sxs-lookup"><span data-stu-id="3fc3d-175">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="3fc3d-176">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="3fc3d-176">All installed protocols</span></span> | <span data-ttu-id="3fc3d-177">Protokoly podporované tímto rozbočovačem</span><span class="sxs-lookup"><span data-stu-id="3fc3d-177">Protocols supported by this hub.</span></span> <span data-ttu-id="3fc3d-178">Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-178">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="3fc3d-179">Pokud `true`se do klientů vrátí podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-179">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="3fc3d-180">Výchozí hodnota je `false`, protože tyto zprávy o výjimce můžou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-180">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

<span data-ttu-id="3fc3d-181">Možnosti lze nakonfigurovat pro všechna centra tím, že poskytnete možnosti delegáta `AddSignalR` pro volání `Startup.ConfigureServices`v.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-181">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="3fc3d-182">Možnosti pro jeden rozbočovač přepíší globální možnosti poskytované v `AddSignalR` a můžou být nakonfigurované pomocí: <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*></span><span class="sxs-lookup"><span data-stu-id="3fc3d-182">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="3fc3d-183">Rozšířené možnosti konfigurace protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="3fc3d-183">Advanced HTTP configuration options</span></span>

<span data-ttu-id="3fc3d-184">Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-184">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="3fc3d-185">Tyto možnosti jsou nakonfigurovány předáním delegáta [MapHub\<T >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-185">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="3fc3d-186">Následující tabulka popisuje možnosti konfigurace upřesňujících možností protokolu HTTP pro ASP.NET Core signalizaci:</span><span class="sxs-lookup"><span data-stu-id="3fc3d-186">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="3fc3d-187">Možnost</span><span class="sxs-lookup"><span data-stu-id="3fc3d-187">Option</span></span> | <span data-ttu-id="3fc3d-188">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="3fc3d-188">Default Value</span></span> | <span data-ttu-id="3fc3d-189">Popis</span><span class="sxs-lookup"><span data-stu-id="3fc3d-189">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="3fc3d-190">32 KB</span><span class="sxs-lookup"><span data-stu-id="3fc3d-190">32 KB</span></span> | <span data-ttu-id="3fc3d-191">Maximální počet bajtů přijatých od klienta, které jsou ukládány do vyrovnávací paměti serveru před použitím protitlaku.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-191">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="3fc3d-192">Zvýšením této hodnoty umožníte, aby server přijímal větší zprávy rychleji bez použití zatížení, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-192">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="3fc3d-193">Data se `Authorize` automaticky shromažďují z atributů použitých pro třídu centra.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-193">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="3fc3d-194">Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru</span><span class="sxs-lookup"><span data-stu-id="3fc3d-194">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="3fc3d-195">32 KB</span><span class="sxs-lookup"><span data-stu-id="3fc3d-195">32 KB</span></span> | <span data-ttu-id="3fc3d-196">Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru před pozorováním protitlaku.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-196">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="3fc3d-197">Zvýšením této hodnoty umožníte serveru rychleji ukládat větší zprávy bez nutnosti očekávat zatížení, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-197">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="3fc3d-198">Všechny přenosy jsou povolené.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-198">All Transports are enabled.</span></span> | <span data-ttu-id="3fc3d-199">Bitové příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenos, který může klient použít pro připojení.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-199">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="3fc3d-200">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-200">See below.</span></span> | <span data-ttu-id="3fc3d-201">Další možnosti specifické pro přenos dlouhého cyklického dotazování.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-201">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="3fc3d-202">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-202">See below.</span></span> | <span data-ttu-id="3fc3d-203">Další možnosti specifické pro přenos pomocí protokolu WebSockets</span><span class="sxs-lookup"><span data-stu-id="3fc3d-203">Additional options specific to the WebSockets transport.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-3.0"

| <span data-ttu-id="3fc3d-204">Možnost</span><span class="sxs-lookup"><span data-stu-id="3fc3d-204">Option</span></span> | <span data-ttu-id="3fc3d-205">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="3fc3d-205">Default Value</span></span> | <span data-ttu-id="3fc3d-206">Popis</span><span class="sxs-lookup"><span data-stu-id="3fc3d-206">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="3fc3d-207">32 KB</span><span class="sxs-lookup"><span data-stu-id="3fc3d-207">32 KB</span></span> | <span data-ttu-id="3fc3d-208">Maximální počet bajtů přijatých od klienta, které jsou vyrovnávací paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-208">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="3fc3d-209">Zvýšením této hodnoty umožníte serveru přijímat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-209">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="3fc3d-210">Data se `Authorize` automaticky shromažďují z atributů použitých pro třídu centra.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-210">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="3fc3d-211">Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru</span><span class="sxs-lookup"><span data-stu-id="3fc3d-211">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="3fc3d-212">32 KB</span><span class="sxs-lookup"><span data-stu-id="3fc3d-212">32 KB</span></span> | <span data-ttu-id="3fc3d-213">Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-213">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="3fc3d-214">Zvýšením této hodnoty umožníte serveru odesílat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-214">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="3fc3d-215">Všechny přenosy jsou povolené.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-215">All Transports are enabled.</span></span> | <span data-ttu-id="3fc3d-216">Bitové příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenos, který může klient použít pro připojení.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-216">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="3fc3d-217">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-217">See below.</span></span> | <span data-ttu-id="3fc3d-218">Další možnosti specifické pro přenos dlouhého cyklického dotazování.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-218">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="3fc3d-219">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-219">See below.</span></span> | <span data-ttu-id="3fc3d-220">Další možnosti specifické pro přenos pomocí protokolu WebSockets</span><span class="sxs-lookup"><span data-stu-id="3fc3d-220">Additional options specific to the WebSockets transport.</span></span> |

::: moniker-end

<span data-ttu-id="3fc3d-221">Přenos dlouhého cyklického dotazování má další možnosti, které je možné `LongPolling` konfigurovat pomocí vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="3fc3d-221">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="3fc3d-222">Možnost</span><span class="sxs-lookup"><span data-stu-id="3fc3d-222">Option</span></span> | <span data-ttu-id="3fc3d-223">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="3fc3d-223">Default Value</span></span> | <span data-ttu-id="3fc3d-224">Popis</span><span class="sxs-lookup"><span data-stu-id="3fc3d-224">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="3fc3d-225">90 sekund</span><span class="sxs-lookup"><span data-stu-id="3fc3d-225">90 seconds</span></span> | <span data-ttu-id="3fc3d-226">Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jedné žádosti o cyklické dotazování.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-226">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="3fc3d-227">Snížení této hodnoty způsobí, že klient bude vydávat nové požadavky na dotaz častěji.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-227">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="3fc3d-228">Přenos protokolu WebSocket má další možnosti, které je možné konfigurovat pomocí `WebSockets` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="3fc3d-228">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="3fc3d-229">Možnost</span><span class="sxs-lookup"><span data-stu-id="3fc3d-229">Option</span></span> | <span data-ttu-id="3fc3d-230">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="3fc3d-230">Default Value</span></span> | <span data-ttu-id="3fc3d-231">Popis</span><span class="sxs-lookup"><span data-stu-id="3fc3d-231">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="3fc3d-232">5 sekund</span><span class="sxs-lookup"><span data-stu-id="3fc3d-232">5 seconds</span></span> | <span data-ttu-id="3fc3d-233">Pokud se po ukončení serveru aplikace v tomto časovém intervalu nepovede zavřít, připojení se ukončí.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-233">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="3fc3d-234">Delegát, který lze použít k nastavení `Sec-WebSocket-Protocol` záhlaví na vlastní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-234">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="3fc3d-235">Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že se vrátí požadovaná hodnota.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-235">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="3fc3d-236">Konfigurace možností klienta</span><span class="sxs-lookup"><span data-stu-id="3fc3d-236">Configure client options</span></span>

<span data-ttu-id="3fc3d-237">Možnosti klienta lze nakonfigurovat pro `HubConnectionBuilder` typ (k dispozici v klientech rozhraní .NET a v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="3fc3d-237">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="3fc3d-238">Je také k dispozici v klientovi Java, ale `HttpHubConnectionBuilder` podtřídou je to, co obsahuje možnosti konfigurace tvůrce a také `HubConnection` na sebe sama.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-238">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="3fc3d-239">Konfigurovat protokolování</span><span class="sxs-lookup"><span data-stu-id="3fc3d-239">Configure logging</span></span>

<span data-ttu-id="3fc3d-240">Protokolování je konfigurováno v klientovi .NET pomocí `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-240">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="3fc3d-241">Zprostředkovatelé protokolování a filtry je možné zaregistrovat stejným způsobem jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-241">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="3fc3d-242">Další informace najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="3fc3d-242">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="3fc3d-243">Aby bylo možné registrovat poskytovatele protokolování, je nutné nainstalovat potřebné balíčky.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-243">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="3fc3d-244">Úplný seznam najdete v části [předdefinovaná zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-244">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="3fc3d-245">Chcete-li například povolit protokolování konzoly, nainstalujte `Microsoft.Extensions.Logging.Console` balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-245">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="3fc3d-246">Zavolejte metodu `AddConsole` rozšíření:</span><span class="sxs-lookup"><span data-stu-id="3fc3d-246">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="3fc3d-247">V klientovi jazyka JavaScript existuje podobná `configureLogging` metoda.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-247">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="3fc3d-248">`LogLevel` Zadejte hodnotu, která určuje minimální úroveň zpráv protokolu, které mají být vyprodukovány.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-248">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="3fc3d-249">Protokoly se zapisují do okna konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-249">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3fc3d-250">Místo hodnoty můžete zadat také `string` hodnotu představující název úrovně protokolu. `LogLevel`</span><span class="sxs-lookup"><span data-stu-id="3fc3d-250">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="3fc3d-251">To je užitečné při konfiguraci protokolování signálu v prostředích, kde nemáte přístup k `LogLevel` konstantám.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-251">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="3fc3d-252">V následující tabulce jsou uvedeny dostupné úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-252">The following table lists the available log levels.</span></span> <span data-ttu-id="3fc3d-253">Hodnota, kterou zadáte `configureLogging` , nastaví **minimální** úroveň protokolu, která se bude protokolovat.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-253">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="3fc3d-254">Zprávy zaznamenané na této úrovni **nebo úrovně uvedené za ní v tabulce**budou protokolovány.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-254">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="3fc3d-255">String</span><span class="sxs-lookup"><span data-stu-id="3fc3d-255">String</span></span>                      | <span data-ttu-id="3fc3d-256">LogLevel</span><span class="sxs-lookup"><span data-stu-id="3fc3d-256">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="3fc3d-257">`info`**nebo**`information`</span><span class="sxs-lookup"><span data-stu-id="3fc3d-257">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="3fc3d-258">`warn`**nebo**`warning`</span><span class="sxs-lookup"><span data-stu-id="3fc3d-258">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

::: moniker-end

> [!NOTE]
> <span data-ttu-id="3fc3d-259">Chcete-li protokolování zcela zakázat `signalR.LogLevel.None` , zadejte `configureLogging` v metodě.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-259">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="3fc3d-260">Další informace o protokolování najdete v [dokumentaci k](xref:signalr/diagnostics)nástroji pro diagnostiku signálu.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-260">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="3fc3d-261">Klient Java Signal používá knihovnu [SLF4J](https://www.slf4j.org/) k protokolování.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-261">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="3fc3d-262">Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-262">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="3fc3d-263">Následující fragment kódu ukazuje, jak používat `java.util.logging` s klientem nástroje pro signalizaci v jazyce Java.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-263">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="3fc3d-264">Pokud ve svých závislostech nenakonfigurujete protokolování, SLF4J načte výchozí protokolovací nástroj No-operation s následující zprávou upozornění:</span><span class="sxs-lookup"><span data-stu-id="3fc3d-264">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="3fc3d-265">Tuto možnost lze bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-265">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="3fc3d-266">Konfigurace povolených přenosů</span><span class="sxs-lookup"><span data-stu-id="3fc3d-266">Configure allowed transports</span></span>

<span data-ttu-id="3fc3d-267">Přenosy používané signálem může být nakonfigurována při `WithUrl` volání (`withUrl` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="3fc3d-267">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="3fc3d-268">Bitové nebo hodnoty `HttpTransportType` lze použít k omezení klienta na používání pouze zadaných přenosů.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-268">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="3fc3d-269">Ve výchozím nastavení jsou povolené všechny přenosy.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-269">All transports are enabled by default.</span></span>

<span data-ttu-id="3fc3d-270">Pokud třeba chcete zakázat přenos událostí odeslaných serverem, ale povolit WebSockets a dlouhá připojení s dotazem:</span><span class="sxs-lookup"><span data-stu-id="3fc3d-270">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="3fc3d-271">V klientu jazyka JavaScript jsou přenosy konfigurovány nastavením `transport` pole v objektu Options, který je k dispozici pro: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="3fc3d-271">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="3fc3d-272">V této verzi je jediným dostupným přenosem klientský WebSocket v jazyce Java.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-272">In this version of the Java client websockets is the only available transport.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="3fc3d-273">V klientovi Java se přenos vybere s `withTransport` metodou `HttpHubConnectionBuilder`na.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-273">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="3fc3d-274">Klient Java standardně používá přenos pomocí protokolu WebSockets.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-274">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="3fc3d-275">Klient Java Signale ještě nepodporuje záložní přenos.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-275">The SignalR Java client doesn't support transport fallback yet.</span></span>

::: moniker-end

### <a name="configure-bearer-authentication"></a><span data-ttu-id="3fc3d-276">Konfigurace ověřování nosiče</span><span class="sxs-lookup"><span data-stu-id="3fc3d-276">Configure bearer authentication</span></span>

<span data-ttu-id="3fc3d-277">Chcete-li poskytnout ověřovací data společně s požadavky na signalizaci `AccessTokenProvider` , použijte`accessTokenFactory` možnost (v jazyce JavaScript) k určení funkce, která vrací požadovaný přístupový token.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-277">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="3fc3d-278">V klientovi .NET se tento přístupový token předává jako token ověřování http "Bearer" (s použitím `Authorization` hlavičky s `Bearer`typem).</span><span class="sxs-lookup"><span data-stu-id="3fc3d-278">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="3fc3d-279">V klientu jazyka JavaScript se přístupový token používá jako nosný token, **s výjimkou** případů, kdy rozhraní API prohlížeče omezuje možnost použít hlavičky (konkrétně v požadavcích na události odeslané serverem a objekty WebSockets).</span><span class="sxs-lookup"><span data-stu-id="3fc3d-279">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="3fc3d-280">V těchto případech je přístupový token k dispozici jako hodnota `access_token`řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-280">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="3fc3d-281">V klientu `AccessTokenProvider` .NET lze možnost zadat pomocí delegáta možnosti v `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="3fc3d-281">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="3fc3d-282">V klientovi JavaScriptu je přístupový token nakonfigurovaný nastavením `accessTokenFactory` pole v objektu Options v: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="3fc3d-282">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="3fc3d-283">V klientovi pro signalizaci Java můžete nakonfigurovat nosný token, který se použije pro ověřování, a to tak, že do [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)poskytne továrnu přístupového tokenu.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-283">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="3fc3d-284">Použijte [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) k poskytnutí [> jednoho\<řetězce](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava) .</span><span class="sxs-lookup"><span data-stu-id="3fc3d-284">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="3fc3d-285">Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-285">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="3fc3d-286">Konfigurace možností timeout a Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="3fc3d-286">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="3fc3d-287">Další možnosti konfigurace časového limitu a chování funkce Keep-Alive jsou k `HubConnection` dispozici na samotném objektu:</span><span class="sxs-lookup"><span data-stu-id="3fc3d-287">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>


::: moniker range=">= aspnetcore-2.2"

# <a name="nettabdotnet"></a>[<span data-ttu-id="3fc3d-288">.NET</span><span class="sxs-lookup"><span data-stu-id="3fc3d-288">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="3fc3d-289">Možnost</span><span class="sxs-lookup"><span data-stu-id="3fc3d-289">Option</span></span> | <span data-ttu-id="3fc3d-290">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="3fc3d-290">Default value</span></span> | <span data-ttu-id="3fc3d-291">Popis</span><span class="sxs-lookup"><span data-stu-id="3fc3d-291">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="3fc3d-292">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="3fc3d-292">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="3fc3d-293">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-293">Timeout for server activity.</span></span> <span data-ttu-id="3fc3d-294">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí `Closed` událost (`onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="3fc3d-294">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="3fc3d-295">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-295">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="3fc3d-296">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-296">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="3fc3d-297">15 sekund</span><span class="sxs-lookup"><span data-stu-id="3fc3d-297">15 seconds</span></span> | <span data-ttu-id="3fc3d-298">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-298">Timeout for initial server handshake.</span></span> <span data-ttu-id="3fc3d-299">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a spustí `Closed` událost (`onclose` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="3fc3d-299">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="3fc3d-300">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-300">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="3fc3d-301">Další informace o procesu handshake najdete v tématu [specifikace protokolu centra signalizace](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="3fc3d-301">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="3fc3d-302">15 sekund</span><span class="sxs-lookup"><span data-stu-id="3fc3d-302">15 seconds</span></span> | <span data-ttu-id="3fc3d-303">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-303">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="3fc3d-304">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-304">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="3fc3d-305">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-305">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="3fc3d-306">V klientovi .NET jsou hodnoty časového limitu zadány `TimeSpan` jako hodnoty.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-306">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="3fc3d-307">JavaScript</span><span class="sxs-lookup"><span data-stu-id="3fc3d-307">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="3fc3d-308">Možnost</span><span class="sxs-lookup"><span data-stu-id="3fc3d-308">Option</span></span> | <span data-ttu-id="3fc3d-309">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="3fc3d-309">Default value</span></span> | <span data-ttu-id="3fc3d-310">Popis</span><span class="sxs-lookup"><span data-stu-id="3fc3d-310">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="3fc3d-311">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="3fc3d-311">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="3fc3d-312">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-312">Timeout for server activity.</span></span> <span data-ttu-id="3fc3d-313">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onclose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-313">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="3fc3d-314">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-314">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="3fc3d-315">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-315">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="3fc3d-316">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="3fc3d-316">15 seconds (15,000 miliseconds)</span></span> | <span data-ttu-id="3fc3d-317">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-317">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="3fc3d-318">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-318">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="3fc3d-319">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-319">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="3fc3d-320">Java</span><span class="sxs-lookup"><span data-stu-id="3fc3d-320">Java</span></span>](#tab/java)

| <span data-ttu-id="3fc3d-321">Možnost</span><span class="sxs-lookup"><span data-stu-id="3fc3d-321">Option</span></span> | <span data-ttu-id="3fc3d-322">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="3fc3d-322">Default value</span></span> | <span data-ttu-id="3fc3d-323">Popis</span><span class="sxs-lookup"><span data-stu-id="3fc3d-323">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="3fc3d-324">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="3fc3d-324">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="3fc3d-325">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="3fc3d-325">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="3fc3d-326">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-326">Timeout for server activity.</span></span> <span data-ttu-id="3fc3d-327">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onClose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-327">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="3fc3d-328">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-328">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="3fc3d-329">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-329">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="3fc3d-330">15 sekund</span><span class="sxs-lookup"><span data-stu-id="3fc3d-330">15 seconds</span></span> | <span data-ttu-id="3fc3d-331">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-331">Timeout for initial server handshake.</span></span> <span data-ttu-id="3fc3d-332">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje `onClose` událost.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-332">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="3fc3d-333">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-333">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="3fc3d-334">Další informace o procesu handshake najdete v tématu [specifikace protokolu centra signalizace](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="3fc3d-334">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="3fc3d-335">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="3fc3d-335">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="3fc3d-336">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="3fc3d-336">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="3fc3d-337">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-337">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="3fc3d-338">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-338">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="3fc3d-339">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-339">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="nettabdotnet"></a>[<span data-ttu-id="3fc3d-340">.NET</span><span class="sxs-lookup"><span data-stu-id="3fc3d-340">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="3fc3d-341">Možnost</span><span class="sxs-lookup"><span data-stu-id="3fc3d-341">Option</span></span> | <span data-ttu-id="3fc3d-342">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="3fc3d-342">Default value</span></span> | <span data-ttu-id="3fc3d-343">Popis</span><span class="sxs-lookup"><span data-stu-id="3fc3d-343">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="3fc3d-344">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="3fc3d-344">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="3fc3d-345">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-345">Timeout for server activity.</span></span> <span data-ttu-id="3fc3d-346">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí `Closed` událost (`onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="3fc3d-346">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="3fc3d-347">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-347">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="3fc3d-348">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-348">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="3fc3d-349">15 sekund</span><span class="sxs-lookup"><span data-stu-id="3fc3d-349">15 seconds</span></span> | <span data-ttu-id="3fc3d-350">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-350">Timeout for initial server handshake.</span></span> <span data-ttu-id="3fc3d-351">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a spustí `Closed` událost (`onclose` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="3fc3d-351">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="3fc3d-352">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-352">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="3fc3d-353">Další informace o procesu handshake najdete v tématu [specifikace protokolu centra signalizace](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="3fc3d-353">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="3fc3d-354">V klientovi .NET jsou hodnoty časového limitu zadány `TimeSpan` jako hodnoty.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-354">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="3fc3d-355">JavaScript</span><span class="sxs-lookup"><span data-stu-id="3fc3d-355">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="3fc3d-356">Možnost</span><span class="sxs-lookup"><span data-stu-id="3fc3d-356">Option</span></span> | <span data-ttu-id="3fc3d-357">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="3fc3d-357">Default value</span></span> | <span data-ttu-id="3fc3d-358">Popis</span><span class="sxs-lookup"><span data-stu-id="3fc3d-358">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="3fc3d-359">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="3fc3d-359">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="3fc3d-360">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-360">Timeout for server activity.</span></span> <span data-ttu-id="3fc3d-361">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onclose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-361">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="3fc3d-362">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-362">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="3fc3d-363">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-363">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="3fc3d-364">Java</span><span class="sxs-lookup"><span data-stu-id="3fc3d-364">Java</span></span>](#tab/java)

| <span data-ttu-id="3fc3d-365">Možnost</span><span class="sxs-lookup"><span data-stu-id="3fc3d-365">Option</span></span> | <span data-ttu-id="3fc3d-366">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="3fc3d-366">Default value</span></span> | <span data-ttu-id="3fc3d-367">Popis</span><span class="sxs-lookup"><span data-stu-id="3fc3d-367">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="3fc3d-368">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="3fc3d-368">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="3fc3d-369">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="3fc3d-369">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="3fc3d-370">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-370">Timeout for server activity.</span></span> <span data-ttu-id="3fc3d-371">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onClose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-371">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="3fc3d-372">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-372">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="3fc3d-373">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit k příkazům k zadání času.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-373">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="3fc3d-374">15 sekund</span><span class="sxs-lookup"><span data-stu-id="3fc3d-374">15 seconds</span></span> | <span data-ttu-id="3fc3d-375">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-375">Timeout for initial server handshake.</span></span> <span data-ttu-id="3fc3d-376">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje `onClose` událost.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-376">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="3fc3d-377">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-377">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="3fc3d-378">Další informace o procesu handshake najdete v tématu [specifikace protokolu centra signalizace](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="3fc3d-378">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

::: moniker-end

---

### <a name="configure-additional-options"></a><span data-ttu-id="3fc3d-379">Konfigurovat další možnosti</span><span class="sxs-lookup"><span data-stu-id="3fc3d-379">Configure additional options</span></span>

<span data-ttu-id="3fc3d-380">Další možnosti `WithUrl` lze konfigurovat v metodě (`withUrl` v jazyce JavaScript) na `HubConnectionBuilder` různých konfiguračních rozhraních API `HttpHubConnectionBuilder` v klientovi Java:</span><span class="sxs-lookup"><span data-stu-id="3fc3d-380">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="3fc3d-381">.NET</span><span class="sxs-lookup"><span data-stu-id="3fc3d-381">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="3fc3d-382">Možnost .NET</span><span class="sxs-lookup"><span data-stu-id="3fc3d-382">.NET Option</span></span> |  <span data-ttu-id="3fc3d-383">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="3fc3d-383">Default value</span></span> | <span data-ttu-id="3fc3d-384">Popis</span><span class="sxs-lookup"><span data-stu-id="3fc3d-384">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="3fc3d-385">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-385">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="3fc3d-386">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-386">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="3fc3d-387">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-387">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="3fc3d-388">Toto nastavení se nedá povolit při použití služby signalizace Azure.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-388">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="3fc3d-389">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="3fc3d-389">Empty</span></span> | <span data-ttu-id="3fc3d-390">Kolekce certifikátů TLS pro odeslání na požadavky na ověření.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-390">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="3fc3d-391">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="3fc3d-391">Empty</span></span> | <span data-ttu-id="3fc3d-392">Kolekce souborů cookie protokolu HTTP, které se mají odeslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="3fc3d-392">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="3fc3d-393">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="3fc3d-393">Empty</span></span> | <span data-ttu-id="3fc3d-394">Přihlašovací údaje, které se mají poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="3fc3d-394">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="3fc3d-395">5 sekund</span><span class="sxs-lookup"><span data-stu-id="3fc3d-395">5 seconds</span></span> | <span data-ttu-id="3fc3d-396">Jenom objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-396">WebSockets only.</span></span> <span data-ttu-id="3fc3d-397">Maximální doba, po jejímž uplynutí bude klient čekat na ukončení žádosti o uzavření.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-397">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="3fc3d-398">Pokud server nepotvrdí zavření v této době, klient se odpojí.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-398">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="3fc3d-399">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="3fc3d-399">Empty</span></span> | <span data-ttu-id="3fc3d-400">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="3fc3d-400">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="3fc3d-401">Delegát, který lze použít ke konfiguraci nebo nahrazení `HttpMessageHandler` používaného k odeslání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-401">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="3fc3d-402">Nepoužívá se pro připojení pomocí protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-402">Not used for WebSocket connections.</span></span> <span data-ttu-id="3fc3d-403">Tento delegát musí vracet hodnotu, která není null, a obdrží výchozí hodnotu jako parametr.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-403">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="3fc3d-404">Buď změňte nastavení u této výchozí hodnoty a vraťte je, nebo vraťte novou `HttpMessageHandler` instanci.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-404">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="3fc3d-405">**Při nahrazování obslužné rutiny Nezapomeňte zkopírovat nastavení, která chcete zachovat, od poskytnuté obslužné rutiny, jinak konfigurované možnosti (například soubory cookie a záhlaví) nebudou použity pro novou obslužnou rutinu.**</span><span class="sxs-lookup"><span data-stu-id="3fc3d-405">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="3fc3d-406">Proxy server HTTP, který se má použít při odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-406">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="3fc3d-407">Nastavte tuto logickou hodnotu pro odeslání výchozích přihlašovacích údajů pro požadavky HTTP a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-407">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="3fc3d-408">To umožňuje použití ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-408">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="3fc3d-409">Delegát, který lze použít ke konfiguraci dalších možností protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-409">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="3fc3d-410">Přijímá instanci [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , která se dá použít ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-410">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="3fc3d-411">JavaScript</span><span class="sxs-lookup"><span data-stu-id="3fc3d-411">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="3fc3d-412">Možnost JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="3fc3d-412">JavaScript Option</span></span> | <span data-ttu-id="3fc3d-413">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="3fc3d-413">Default Value</span></span> | <span data-ttu-id="3fc3d-414">Popis</span><span class="sxs-lookup"><span data-stu-id="3fc3d-414">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="3fc3d-415">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-415">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="3fc3d-416">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-416">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="3fc3d-417">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-417">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="3fc3d-418">Toto nastavení se nedá povolit při použití služby signalizace Azure.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-418">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="3fc3d-419">Java</span><span class="sxs-lookup"><span data-stu-id="3fc3d-419">Java</span></span>](#tab/java)

| <span data-ttu-id="3fc3d-420">Možnost jazyka Java</span><span class="sxs-lookup"><span data-stu-id="3fc3d-420">Java Option</span></span> | <span data-ttu-id="3fc3d-421">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="3fc3d-421">Default Value</span></span> | <span data-ttu-id="3fc3d-422">Popis</span><span class="sxs-lookup"><span data-stu-id="3fc3d-422">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="3fc3d-423">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-423">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="3fc3d-424">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-424">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="3fc3d-425">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-425">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="3fc3d-426">Toto nastavení se nedá povolit při použití služby signalizace Azure.</span><span class="sxs-lookup"><span data-stu-id="3fc3d-426">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="3fc3d-427">`withHeader``withHeaders`</span><span class="sxs-lookup"><span data-stu-id="3fc3d-427">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="3fc3d-428">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="3fc3d-428">Empty</span></span> | <span data-ttu-id="3fc3d-429">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="3fc3d-429">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="3fc3d-430">V klientu .NET můžete tyto možnosti upravit pomocí delegáta možností, který je k `WithUrl`dispozici pro:</span><span class="sxs-lookup"><span data-stu-id="3fc3d-430">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="3fc3d-431">V klientovi JavaScriptu lze tyto možnosti poskytnout v objektu jazyka JavaScript, který je k dispozici pro `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="3fc3d-431">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="3fc3d-432">V klientovi Java lze tyto možnosti nakonfigurovat s metodami `HttpHubConnectionBuilder` vrácenými z`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="3fc3d-432">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="3fc3d-433">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="3fc3d-433">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>
