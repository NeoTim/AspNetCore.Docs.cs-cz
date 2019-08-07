---
title: Konfigurace signalizace ASP.NET Core
author: bradygaster
description: Naučte se konfigurovat aplikace pro signalizaci ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 08/05/2019
uid: signalr/configuration
ms.openlocfilehash: 4706a1e2774fa9f6fb40085da944e8a82476ef05
ms.sourcegitcommit: 2eb605f4f20ac4dd9de6c3b3e3453e108a357a21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68820044"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="0b7ba-103">Konfigurace signalizace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0b7ba-103">ASP.NET Core SignalR configuration</span></span>

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="0b7ba-104">Možnosti serializace JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="0b7ba-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="0b7ba-105">ASP.NET Core Signal podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="0b7ba-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="0b7ba-106">Každý protokol má možnosti konfigurace serializace.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="0b7ba-107">Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , kterou lze přidat po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v `Startup.ConfigureServices` metodě.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="0b7ba-108">Metoda přebírá delegáta, který `options` přijímá objekt. `AddJsonProtocol`</span><span class="sxs-lookup"><span data-stu-id="0b7ba-108">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="0b7ba-109">Vlastnost [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) tohoto objektu je objekt JSON.NET `JsonSerializerSettings` , který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-109">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="0b7ba-110">Další podrobnosti najdete v [dokumentaci k JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm) .</span><span class="sxs-lookup"><span data-stu-id="0b7ba-110">See the [JSON.NET Documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm) for more details.</span></span>

<span data-ttu-id="0b7ba-111">Chcete-li například nakonfigurovat serializátor pro použití názvů vlastností "PascalCase" namísto výchozích názvů "camelCase", použijte následující kód:</span><span class="sxs-lookup"><span data-stu-id="0b7ba-111">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
        new DefaultContractResolver();
    });
```

<span data-ttu-id="0b7ba-112">V klientovi .NET existuje stejná `AddJsonProtocol` rozšiřující metoda v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="0b7ba-112">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="0b7ba-113">`Microsoft.Extensions.DependencyInjection` Obor názvů musí být importován, aby bylo možné přeložit metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="0b7ba-113">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="0b7ba-114">V tuto chvíli není možné konfigurovat serializaci JSON v klientovi jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-114">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="0b7ba-115">Možnosti serializace MessagePack</span><span class="sxs-lookup"><span data-stu-id="0b7ba-115">MessagePack serialization options</span></span>

<span data-ttu-id="0b7ba-116">Serializaci MessagePack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="0b7ba-116">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="0b7ba-117">Další podrobnosti najdete [v tématu MessagePack v nástroji Signal](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="0b7ba-117">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="0b7ba-118">V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi v jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-118">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="0b7ba-119">Konfigurovat možnosti serveru</span><span class="sxs-lookup"><span data-stu-id="0b7ba-119">Configure server options</span></span>

<span data-ttu-id="0b7ba-120">Následující tabulka obsahuje popis možností konfigurace Center pro signály:</span><span class="sxs-lookup"><span data-stu-id="0b7ba-120">The following table describes options for configuring SignalR hubs:</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="0b7ba-121">Možnost</span><span class="sxs-lookup"><span data-stu-id="0b7ba-121">Option</span></span> | <span data-ttu-id="0b7ba-122">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="0b7ba-122">Default Value</span></span> | <span data-ttu-id="0b7ba-123">Popis</span><span class="sxs-lookup"><span data-stu-id="0b7ba-123">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="0b7ba-124">30 sekund</span><span class="sxs-lookup"><span data-stu-id="0b7ba-124">30 seconds</span></span> | <span data-ttu-id="0b7ba-125">Server bude předpokládat, že je klient odpojený, pokud v tomto intervalu neobdržel zprávu (včetně Keep-Alive).</span><span class="sxs-lookup"><span data-stu-id="0b7ba-125">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="0b7ba-126">Může trvat delší dobu, než je tento časový limit, aby bylo možné klienta ve skutečnosti označit jako odpojený, protože to je implementováno.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-126">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="0b7ba-127">Doporučená hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-127">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="0b7ba-128">15 sekund</span><span class="sxs-lookup"><span data-stu-id="0b7ba-128">15 seconds</span></span> | <span data-ttu-id="0b7ba-129">Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-129">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="0b7ba-130">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-130">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0b7ba-131">Další informace o procesu handshake najdete v tématu [specifikace protokolu centra signalizace](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="0b7ba-131">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="0b7ba-132">15 sekund</span><span class="sxs-lookup"><span data-stu-id="0b7ba-132">15 seconds</span></span> | <span data-ttu-id="0b7ba-133">Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-133">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="0b7ba-134">Při změně `KeepAliveInterval` `ServerTimeout` nastavení/ změňte nastavení v klientovi. `serverTimeoutInMilliseconds`</span><span class="sxs-lookup"><span data-stu-id="0b7ba-134">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="0b7ba-135">Doporučená `ServerTimeout` / hodnota je dvojnásobná`KeepAliveInterval` hodnota. `serverTimeoutInMilliseconds`</span><span class="sxs-lookup"><span data-stu-id="0b7ba-135">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="0b7ba-136">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="0b7ba-136">All installed protocols</span></span> | <span data-ttu-id="0b7ba-137">Protokoly podporované tímto rozbočovačem</span><span class="sxs-lookup"><span data-stu-id="0b7ba-137">Protocols supported by this hub.</span></span> <span data-ttu-id="0b7ba-138">Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-138">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="0b7ba-139">Pokud `true`se do klientů vrátí podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-139">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="0b7ba-140">Výchozí hodnota je `false`, protože tyto zprávy o výjimce můžou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-140">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="0b7ba-141">Maximální počet položek, které lze uložit do vyrovnávací paměti pro datové proudy pro odeslání klienta.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-141">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="0b7ba-142">Pokud je dosaženo tohoto limitu, zpracování volání je blokováno, dokud server nezpracovává položky datového proudu.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-142">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|

::: moniker-end

::: moniker range="= aspnetcore-2.2"

| <span data-ttu-id="0b7ba-143">Možnost</span><span class="sxs-lookup"><span data-stu-id="0b7ba-143">Option</span></span> | <span data-ttu-id="0b7ba-144">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="0b7ba-144">Default Value</span></span> | <span data-ttu-id="0b7ba-145">Popis</span><span class="sxs-lookup"><span data-stu-id="0b7ba-145">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="0b7ba-146">30 sekund</span><span class="sxs-lookup"><span data-stu-id="0b7ba-146">30 seconds</span></span> | <span data-ttu-id="0b7ba-147">Server bude předpokládat, že je klient odpojený, pokud v tomto intervalu neobdržel zprávu (včetně Keep-Alive).</span><span class="sxs-lookup"><span data-stu-id="0b7ba-147">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="0b7ba-148">Může trvat delší dobu, než je tento časový limit, aby bylo možné klienta ve skutečnosti označit jako odpojený, protože to je implementováno.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-148">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="0b7ba-149">Doporučená hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-149">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="0b7ba-150">15 sekund</span><span class="sxs-lookup"><span data-stu-id="0b7ba-150">15 seconds</span></span> | <span data-ttu-id="0b7ba-151">Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-151">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="0b7ba-152">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-152">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0b7ba-153">Další informace o procesu handshake najdete v tématu [specifikace protokolu centra signalizace](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="0b7ba-153">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="0b7ba-154">15 sekund</span><span class="sxs-lookup"><span data-stu-id="0b7ba-154">15 seconds</span></span> | <span data-ttu-id="0b7ba-155">Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-155">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="0b7ba-156">Při změně `KeepAliveInterval` `ServerTimeout` nastavení/ změňte nastavení v klientovi. `serverTimeoutInMilliseconds`</span><span class="sxs-lookup"><span data-stu-id="0b7ba-156">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="0b7ba-157">Doporučená `ServerTimeout` / hodnota je dvojnásobná`KeepAliveInterval` hodnota. `serverTimeoutInMilliseconds`</span><span class="sxs-lookup"><span data-stu-id="0b7ba-157">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="0b7ba-158">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="0b7ba-158">All installed protocols</span></span> | <span data-ttu-id="0b7ba-159">Protokoly podporované tímto rozbočovačem</span><span class="sxs-lookup"><span data-stu-id="0b7ba-159">Protocols supported by this hub.</span></span> <span data-ttu-id="0b7ba-160">Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-160">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="0b7ba-161">Pokud `true`se do klientů vrátí podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-161">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="0b7ba-162">Výchozí hodnota je `false`, protože tyto zprávy o výjimce můžou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-162">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| <span data-ttu-id="0b7ba-163">Možnost</span><span class="sxs-lookup"><span data-stu-id="0b7ba-163">Option</span></span> | <span data-ttu-id="0b7ba-164">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="0b7ba-164">Default Value</span></span> | <span data-ttu-id="0b7ba-165">Popis</span><span class="sxs-lookup"><span data-stu-id="0b7ba-165">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="0b7ba-166">15 sekund</span><span class="sxs-lookup"><span data-stu-id="0b7ba-166">15 seconds</span></span> | <span data-ttu-id="0b7ba-167">Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-167">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="0b7ba-168">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-168">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0b7ba-169">Další informace o procesu handshake najdete v tématu [specifikace protokolu centra signalizace](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="0b7ba-169">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="0b7ba-170">15 sekund</span><span class="sxs-lookup"><span data-stu-id="0b7ba-170">15 seconds</span></span> | <span data-ttu-id="0b7ba-171">Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-171">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="0b7ba-172">Při změně `KeepAliveInterval` `ServerTimeout` nastavení/ změňte nastavení v klientovi. `serverTimeoutInMilliseconds`</span><span class="sxs-lookup"><span data-stu-id="0b7ba-172">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="0b7ba-173">Doporučená `ServerTimeout` / hodnota je dvojnásobná`KeepAliveInterval` hodnota. `serverTimeoutInMilliseconds`</span><span class="sxs-lookup"><span data-stu-id="0b7ba-173">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="0b7ba-174">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="0b7ba-174">All installed protocols</span></span> | <span data-ttu-id="0b7ba-175">Protokoly podporované tímto rozbočovačem</span><span class="sxs-lookup"><span data-stu-id="0b7ba-175">Protocols supported by this hub.</span></span> <span data-ttu-id="0b7ba-176">Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-176">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="0b7ba-177">Pokud `true`se do klientů vrátí podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-177">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="0b7ba-178">Výchozí hodnota je `false`, protože tyto zprávy o výjimce můžou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-178">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

<span data-ttu-id="0b7ba-179">Možnosti lze nakonfigurovat pro všechna centra tím, že poskytnete možnosti delegáta `AddSignalR` pro volání `Startup.ConfigureServices`v.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-179">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="0b7ba-180">Možnosti pro jeden rozbočovač přepíší globální možnosti poskytované v `AddSignalR` a můžou být nakonfigurované pomocí: <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*></span><span class="sxs-lookup"><span data-stu-id="0b7ba-180">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="0b7ba-181">Rozšířené možnosti konfigurace protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="0b7ba-181">Advanced HTTP configuration options</span></span>

<span data-ttu-id="0b7ba-182">Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-182">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="0b7ba-183">Tyto možnosti jsou nakonfigurovány předáním delegáta [MapHub\<T >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-183">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="0b7ba-184">Následující tabulka popisuje možnosti konfigurace upřesňujících možností protokolu HTTP pro ASP.NET Core signalizaci:</span><span class="sxs-lookup"><span data-stu-id="0b7ba-184">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="0b7ba-185">Možnost</span><span class="sxs-lookup"><span data-stu-id="0b7ba-185">Option</span></span> | <span data-ttu-id="0b7ba-186">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="0b7ba-186">Default Value</span></span> | <span data-ttu-id="0b7ba-187">Popis</span><span class="sxs-lookup"><span data-stu-id="0b7ba-187">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="0b7ba-188">32 KB</span><span class="sxs-lookup"><span data-stu-id="0b7ba-188">32 KB</span></span> | <span data-ttu-id="0b7ba-189">Maximální počet bajtů přijatých od klienta, které jsou vyrovnávací paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-189">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="0b7ba-190">Zvýšením této hodnoty umožníte serveru přijímat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-190">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="0b7ba-191">Data se `Authorize` automaticky shromažďují z atributů použitých pro třídu centra.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-191">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="0b7ba-192">Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru</span><span class="sxs-lookup"><span data-stu-id="0b7ba-192">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="0b7ba-193">32 KB</span><span class="sxs-lookup"><span data-stu-id="0b7ba-193">32 KB</span></span> | <span data-ttu-id="0b7ba-194">Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-194">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="0b7ba-195">Zvýšením této hodnoty umožníte serveru odesílat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-195">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="0b7ba-196">Všechny přenosy jsou povolené.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-196">All Transports are enabled.</span></span> | <span data-ttu-id="0b7ba-197">Bitová maska `HttpTransportType` hodnot, která může omezit přenosů, které může klient použít pro připojení.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-197">A bitmask of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="0b7ba-198">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-198">See below.</span></span> | <span data-ttu-id="0b7ba-199">Další možnosti specifické pro přenos dlouhého cyklického dotazování.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-199">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="0b7ba-200">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-200">See below.</span></span> | <span data-ttu-id="0b7ba-201">Další možnosti specifické pro přenos pomocí protokolu WebSockets</span><span class="sxs-lookup"><span data-stu-id="0b7ba-201">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="0b7ba-202">Přenos dlouhého cyklického dotazování má další možnosti, které je možné `LongPolling` konfigurovat pomocí vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="0b7ba-202">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="0b7ba-203">Možnost</span><span class="sxs-lookup"><span data-stu-id="0b7ba-203">Option</span></span> | <span data-ttu-id="0b7ba-204">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="0b7ba-204">Default Value</span></span> | <span data-ttu-id="0b7ba-205">Popis</span><span class="sxs-lookup"><span data-stu-id="0b7ba-205">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="0b7ba-206">90 sekund</span><span class="sxs-lookup"><span data-stu-id="0b7ba-206">90 seconds</span></span> | <span data-ttu-id="0b7ba-207">Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jedné žádosti o cyklické dotazování.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-207">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="0b7ba-208">Snížení této hodnoty způsobí, že klient bude vydávat nové požadavky na dotaz častěji.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-208">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="0b7ba-209">Přenos protokolu WebSocket má další možnosti, které je možné konfigurovat pomocí `WebSockets` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="0b7ba-209">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="0b7ba-210">Možnost</span><span class="sxs-lookup"><span data-stu-id="0b7ba-210">Option</span></span> | <span data-ttu-id="0b7ba-211">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="0b7ba-211">Default Value</span></span> | <span data-ttu-id="0b7ba-212">Popis</span><span class="sxs-lookup"><span data-stu-id="0b7ba-212">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="0b7ba-213">5 sekund</span><span class="sxs-lookup"><span data-stu-id="0b7ba-213">5 seconds</span></span> | <span data-ttu-id="0b7ba-214">Pokud se po ukončení serveru aplikace v tomto časovém intervalu nepovede zavřít, připojení se ukončí.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-214">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="0b7ba-215">Delegát, který lze použít k nastavení `Sec-WebSocket-Protocol` záhlaví na vlastní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-215">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="0b7ba-216">Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že se vrátí požadovaná hodnota.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-216">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="0b7ba-217">Konfigurace možností klienta</span><span class="sxs-lookup"><span data-stu-id="0b7ba-217">Configure client options</span></span>

<span data-ttu-id="0b7ba-218">Možnosti klienta lze nakonfigurovat pro `HubConnectionBuilder` typ (k dispozici v klientech rozhraní .NET a v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0b7ba-218">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="0b7ba-219">Je také k dispozici v klientovi Java, ale `HttpHubConnectionBuilder` podtřídou je to, co obsahuje možnosti konfigurace tvůrce a také `HubConnection` na sebe sama.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-219">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="0b7ba-220">Konfigurovat protokolování</span><span class="sxs-lookup"><span data-stu-id="0b7ba-220">Configure logging</span></span>

<span data-ttu-id="0b7ba-221">Protokolování je konfigurováno v klientovi .NET pomocí `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-221">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="0b7ba-222">Zprostředkovatelé protokolování a filtry je možné zaregistrovat stejným způsobem jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-222">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="0b7ba-223">Další informace najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="0b7ba-223">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="0b7ba-224">Aby bylo možné registrovat poskytovatele protokolování, je nutné nainstalovat potřebné balíčky.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-224">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="0b7ba-225">Úplný seznam najdete v části [předdefinovaná zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-225">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="0b7ba-226">Chcete-li například povolit protokolování konzoly, nainstalujte `Microsoft.Extensions.Logging.Console` balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-226">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="0b7ba-227">Zavolejte metodu `AddConsole` rozšíření:</span><span class="sxs-lookup"><span data-stu-id="0b7ba-227">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="0b7ba-228">V klientovi jazyka JavaScript existuje podobná `configureLogging` metoda.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-228">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="0b7ba-229">`LogLevel` Zadejte hodnotu, která určuje minimální úroveň zpráv protokolu, které mají být vyprodukovány.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-229">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="0b7ba-230">Protokoly se zapisují do okna konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-230">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="0b7ba-231">Místo hodnoty můžete zadat také `string` hodnotu představující název úrovně protokolu. `LogLevel`</span><span class="sxs-lookup"><span data-stu-id="0b7ba-231">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="0b7ba-232">To je užitečné při konfiguraci protokolování signálu v prostředích, kde nemáte přístup k `LogLevel` konstantám.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-232">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="0b7ba-233">V následující tabulce jsou uvedeny dostupné úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-233">The following table lists the available log levels.</span></span> <span data-ttu-id="0b7ba-234">Hodnota, kterou zadáte `configureLogging` , nastaví **minimální** úroveň protokolu, která se bude protokolovat.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-234">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="0b7ba-235">Zprávy zaznamenané na této úrovni **nebo úrovně uvedené za ní v tabulce**budou protokolovány.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-235">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="0b7ba-236">String</span><span class="sxs-lookup"><span data-stu-id="0b7ba-236">String</span></span>                      | <span data-ttu-id="0b7ba-237">LogLevel</span><span class="sxs-lookup"><span data-stu-id="0b7ba-237">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="0b7ba-238">`info`**nebo**`information`</span><span class="sxs-lookup"><span data-stu-id="0b7ba-238">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="0b7ba-239">`warn`**nebo**`warning`</span><span class="sxs-lookup"><span data-stu-id="0b7ba-239">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

::: moniker-end

> [!NOTE]
> <span data-ttu-id="0b7ba-240">Chcete-li protokolování zcela zakázat `signalR.LogLevel.None` , zadejte `configureLogging` v metodě.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-240">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="0b7ba-241">Další informace o protokolování najdete v [dokumentaci k](xref:signalr/diagnostics)nástroji pro diagnostiku signálu.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-241">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="0b7ba-242">Klient Java Signal používá knihovnu [SLF4J](https://www.slf4j.org/) k protokolování.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-242">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="0b7ba-243">Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-243">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="0b7ba-244">Následující fragment kódu ukazuje, jak používat `java.util.logging` s klientem nástroje pro signalizaci v jazyce Java.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-244">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="0b7ba-245">Pokud ve svých závislostech nenakonfigurujete protokolování, SLF4J načte výchozí protokolovací nástroj No-operation s následující zprávou upozornění:</span><span class="sxs-lookup"><span data-stu-id="0b7ba-245">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="0b7ba-246">Tuto možnost lze bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-246">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="0b7ba-247">Konfigurace povolených přenosů</span><span class="sxs-lookup"><span data-stu-id="0b7ba-247">Configure allowed transports</span></span>

<span data-ttu-id="0b7ba-248">Přenosy používané signálem může být nakonfigurována při `WithUrl` volání (`withUrl` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0b7ba-248">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="0b7ba-249">Bitové nebo hodnoty `HttpTransportType` lze použít k omezení klienta na používání pouze zadaných přenosů.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-249">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="0b7ba-250">Ve výchozím nastavení jsou povolené všechny přenosy.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-250">All transports are enabled by default.</span></span>

<span data-ttu-id="0b7ba-251">Pokud třeba chcete zakázat přenos událostí odeslaných serverem, ale povolit WebSockets a dlouhá připojení s dotazem:</span><span class="sxs-lookup"><span data-stu-id="0b7ba-251">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="0b7ba-252">V klientu jazyka JavaScript jsou přenosy konfigurovány nastavením `transport` pole v objektu Options, který je k dispozici pro: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="0b7ba-252">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="0b7ba-253">V této verzi je jediným dostupným přenosem klientský WebSocket v jazyce Java.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-253">In this version of the Java client websockets is the only available transport.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="0b7ba-254">V klientovi Java se přenos vybere s `withTransport` metodou `HttpHubConnectionBuilder`na.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-254">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="0b7ba-255">Klient Java standardně používá přenos pomocí protokolu WebSockets.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-255">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="0b7ba-256">Klient Java Signale ještě nepodporuje záložní přenos.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-256">The SignalR Java client doesn't support transport fallback yet.</span></span>

::: moniker-end

### <a name="configure-bearer-authentication"></a><span data-ttu-id="0b7ba-257">Konfigurace ověřování nosiče</span><span class="sxs-lookup"><span data-stu-id="0b7ba-257">Configure bearer authentication</span></span>

<span data-ttu-id="0b7ba-258">Chcete-li poskytnout ověřovací data společně s požadavky na signalizaci `AccessTokenProvider` , použijte`accessTokenFactory` možnost (v jazyce JavaScript) k určení funkce, která vrací požadovaný přístupový token.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-258">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="0b7ba-259">V klientovi .NET se tento přístupový token předává jako token ověřování http "Bearer" (s použitím `Authorization` hlavičky s `Bearer`typem).</span><span class="sxs-lookup"><span data-stu-id="0b7ba-259">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="0b7ba-260">V klientu jazyka JavaScript se přístupový token používá jako nosný token, **s výjimkou** případů, kdy rozhraní API prohlížeče omezuje možnost použít hlavičky (konkrétně v požadavcích na události odeslané serverem a objekty WebSockets).</span><span class="sxs-lookup"><span data-stu-id="0b7ba-260">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="0b7ba-261">V těchto případech je přístupový token k dispozici jako hodnota `access_token`řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-261">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="0b7ba-262">V klientu `AccessTokenProvider` .NET lze možnost zadat pomocí delegáta možnosti v `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="0b7ba-262">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="0b7ba-263">V klientovi JavaScriptu je přístupový token nakonfigurovaný nastavením `accessTokenFactory` pole v objektu Options v: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="0b7ba-263">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="0b7ba-264">V klientovi pro signalizaci Java můžete nakonfigurovat nosný token, který se použije pro ověřování, a to tak, že do [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)poskytne továrnu přístupového tokenu.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-264">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="0b7ba-265">Použijte [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) k poskytnutí [> jednoho\<řetězce](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava) .</span><span class="sxs-lookup"><span data-stu-id="0b7ba-265">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="0b7ba-266">Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-266">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="0b7ba-267">Konfigurace možností timeout a Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="0b7ba-267">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="0b7ba-268">Další možnosti konfigurace časového limitu a chování funkce Keep-Alive jsou k `HubConnection` dispozici na samotném objektu:</span><span class="sxs-lookup"><span data-stu-id="0b7ba-268">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>


::: moniker range=">= aspnetcore-2.2"

# <a name="nettabdotnet"></a>[<span data-ttu-id="0b7ba-269">.NET</span><span class="sxs-lookup"><span data-stu-id="0b7ba-269">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="0b7ba-270">Možnost</span><span class="sxs-lookup"><span data-stu-id="0b7ba-270">Option</span></span> | <span data-ttu-id="0b7ba-271">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="0b7ba-271">Default value</span></span> | <span data-ttu-id="0b7ba-272">Popis</span><span class="sxs-lookup"><span data-stu-id="0b7ba-272">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="0b7ba-273">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="0b7ba-273">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0b7ba-274">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-274">Timeout for server activity.</span></span> <span data-ttu-id="0b7ba-275">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí `Closed` událost (`onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="0b7ba-275">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="0b7ba-276">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-276">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0b7ba-277">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-277">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="0b7ba-278">15 sekund</span><span class="sxs-lookup"><span data-stu-id="0b7ba-278">15 seconds</span></span> | <span data-ttu-id="0b7ba-279">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-279">Timeout for initial server handshake.</span></span> <span data-ttu-id="0b7ba-280">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a spustí `Closed` událost (`onclose` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0b7ba-280">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="0b7ba-281">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-281">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0b7ba-282">Další informace o procesu handshake najdete v tématu [specifikace protokolu centra signalizace](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="0b7ba-282">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="0b7ba-283">15 sekund</span><span class="sxs-lookup"><span data-stu-id="0b7ba-283">15 seconds</span></span> | <span data-ttu-id="0b7ba-284">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-284">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="0b7ba-285">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-285">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="0b7ba-286">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-286">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="0b7ba-287">V klientovi .NET jsou hodnoty časového limitu zadány `TimeSpan` jako hodnoty.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-287">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="0b7ba-288">JavaScript</span><span class="sxs-lookup"><span data-stu-id="0b7ba-288">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="0b7ba-289">Možnost</span><span class="sxs-lookup"><span data-stu-id="0b7ba-289">Option</span></span> | <span data-ttu-id="0b7ba-290">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="0b7ba-290">Default value</span></span> | <span data-ttu-id="0b7ba-291">Popis</span><span class="sxs-lookup"><span data-stu-id="0b7ba-291">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="0b7ba-292">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="0b7ba-292">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0b7ba-293">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-293">Timeout for server activity.</span></span> <span data-ttu-id="0b7ba-294">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onclose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-294">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="0b7ba-295">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-295">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0b7ba-296">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-296">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="0b7ba-297">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="0b7ba-297">15 seconds (15,000 miliseconds)</span></span> | <span data-ttu-id="0b7ba-298">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-298">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="0b7ba-299">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-299">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="0b7ba-300">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-300">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="0b7ba-301">Java</span><span class="sxs-lookup"><span data-stu-id="0b7ba-301">Java</span></span>](#tab/java)

| <span data-ttu-id="0b7ba-302">Možnost</span><span class="sxs-lookup"><span data-stu-id="0b7ba-302">Option</span></span> | <span data-ttu-id="0b7ba-303">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="0b7ba-303">Default value</span></span> | <span data-ttu-id="0b7ba-304">Popis</span><span class="sxs-lookup"><span data-stu-id="0b7ba-304">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="0b7ba-305">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="0b7ba-305">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="0b7ba-306">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="0b7ba-306">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0b7ba-307">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-307">Timeout for server activity.</span></span> <span data-ttu-id="0b7ba-308">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onClose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-308">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="0b7ba-309">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-309">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0b7ba-310">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-310">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="0b7ba-311">15 sekund</span><span class="sxs-lookup"><span data-stu-id="0b7ba-311">15 seconds</span></span> | <span data-ttu-id="0b7ba-312">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-312">Timeout for initial server handshake.</span></span> <span data-ttu-id="0b7ba-313">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje `onClose` událost.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-313">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="0b7ba-314">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-314">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0b7ba-315">Další informace o procesu handshake najdete v tématu [specifikace protokolu centra signalizace](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="0b7ba-315">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="0b7ba-316">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="0b7ba-316">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="0b7ba-317">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="0b7ba-317">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="0b7ba-318">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-318">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="0b7ba-319">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-319">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="0b7ba-320">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-320">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

::: moniker-end

::: moniker range="< aspnetcore-2.2"

# <a name="nettabdotnet"></a>[<span data-ttu-id="0b7ba-321">.NET</span><span class="sxs-lookup"><span data-stu-id="0b7ba-321">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="0b7ba-322">Možnost</span><span class="sxs-lookup"><span data-stu-id="0b7ba-322">Option</span></span> | <span data-ttu-id="0b7ba-323">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="0b7ba-323">Default value</span></span> | <span data-ttu-id="0b7ba-324">Popis</span><span class="sxs-lookup"><span data-stu-id="0b7ba-324">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="0b7ba-325">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="0b7ba-325">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0b7ba-326">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-326">Timeout for server activity.</span></span> <span data-ttu-id="0b7ba-327">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí `Closed` událost (`onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="0b7ba-327">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="0b7ba-328">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-328">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0b7ba-329">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-329">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="0b7ba-330">15 sekund</span><span class="sxs-lookup"><span data-stu-id="0b7ba-330">15 seconds</span></span> | <span data-ttu-id="0b7ba-331">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-331">Timeout for initial server handshake.</span></span> <span data-ttu-id="0b7ba-332">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a spustí `Closed` událost (`onclose` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="0b7ba-332">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="0b7ba-333">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-333">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0b7ba-334">Další informace o procesu handshake najdete v tématu [specifikace protokolu centra signalizace](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="0b7ba-334">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="0b7ba-335">V klientovi .NET jsou hodnoty časového limitu zadány `TimeSpan` jako hodnoty.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-335">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="0b7ba-336">JavaScript</span><span class="sxs-lookup"><span data-stu-id="0b7ba-336">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="0b7ba-337">Možnost</span><span class="sxs-lookup"><span data-stu-id="0b7ba-337">Option</span></span> | <span data-ttu-id="0b7ba-338">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="0b7ba-338">Default value</span></span> | <span data-ttu-id="0b7ba-339">Popis</span><span class="sxs-lookup"><span data-stu-id="0b7ba-339">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="0b7ba-340">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="0b7ba-340">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0b7ba-341">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-341">Timeout for server activity.</span></span> <span data-ttu-id="0b7ba-342">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onclose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-342">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="0b7ba-343">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-343">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0b7ba-344">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-344">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="0b7ba-345">Java</span><span class="sxs-lookup"><span data-stu-id="0b7ba-345">Java</span></span>](#tab/java)

| <span data-ttu-id="0b7ba-346">Možnost</span><span class="sxs-lookup"><span data-stu-id="0b7ba-346">Option</span></span> | <span data-ttu-id="0b7ba-347">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="0b7ba-347">Default value</span></span> | <span data-ttu-id="0b7ba-348">Popis</span><span class="sxs-lookup"><span data-stu-id="0b7ba-348">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="0b7ba-349">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="0b7ba-349">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="0b7ba-350">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="0b7ba-350">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="0b7ba-351">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-351">Timeout for server activity.</span></span> <span data-ttu-id="0b7ba-352">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onClose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-352">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="0b7ba-353">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-353">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="0b7ba-354">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit k příkazům k zadání času.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-354">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="0b7ba-355">15 sekund</span><span class="sxs-lookup"><span data-stu-id="0b7ba-355">15 seconds</span></span> | <span data-ttu-id="0b7ba-356">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-356">Timeout for initial server handshake.</span></span> <span data-ttu-id="0b7ba-357">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje `onClose` událost.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-357">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="0b7ba-358">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-358">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="0b7ba-359">Další informace o procesu handshake najdete v tématu [specifikace protokolu centra signalizace](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="0b7ba-359">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

::: moniker-end

---

### <a name="configure-additional-options"></a><span data-ttu-id="0b7ba-360">Konfigurovat další možnosti</span><span class="sxs-lookup"><span data-stu-id="0b7ba-360">Configure additional options</span></span>

<span data-ttu-id="0b7ba-361">Další možnosti `WithUrl` lze konfigurovat v metodě (`withUrl` v jazyce JavaScript) na `HubConnectionBuilder` různých konfiguračních rozhraních API `HttpHubConnectionBuilder` v klientovi Java:</span><span class="sxs-lookup"><span data-stu-id="0b7ba-361">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="0b7ba-362">.NET</span><span class="sxs-lookup"><span data-stu-id="0b7ba-362">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="0b7ba-363">Možnost .NET</span><span class="sxs-lookup"><span data-stu-id="0b7ba-363">.NET Option</span></span> |  <span data-ttu-id="0b7ba-364">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="0b7ba-364">Default value</span></span> | <span data-ttu-id="0b7ba-365">Popis</span><span class="sxs-lookup"><span data-stu-id="0b7ba-365">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="0b7ba-366">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-366">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="0b7ba-367">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-367">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="0b7ba-368">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-368">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="0b7ba-369">Toto nastavení se nedá povolit při použití služby signalizace Azure.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-369">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="0b7ba-370">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="0b7ba-370">Empty</span></span> | <span data-ttu-id="0b7ba-371">Kolekce certifikátů TLS pro odeslání na požadavky na ověření.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-371">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="0b7ba-372">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="0b7ba-372">Empty</span></span> | <span data-ttu-id="0b7ba-373">Kolekce souborů cookie protokolu HTTP, které se mají odeslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="0b7ba-373">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="0b7ba-374">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="0b7ba-374">Empty</span></span> | <span data-ttu-id="0b7ba-375">Přihlašovací údaje, které se mají poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="0b7ba-375">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="0b7ba-376">5 sekund</span><span class="sxs-lookup"><span data-stu-id="0b7ba-376">5 seconds</span></span> | <span data-ttu-id="0b7ba-377">Jenom objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-377">WebSockets only.</span></span> <span data-ttu-id="0b7ba-378">Maximální doba, po jejímž uplynutí bude klient čekat na ukončení žádosti o uzavření.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-378">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="0b7ba-379">Pokud server nepotvrdí zavření v této době, klient se odpojí.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-379">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="0b7ba-380">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="0b7ba-380">Empty</span></span> | <span data-ttu-id="0b7ba-381">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="0b7ba-381">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="0b7ba-382">Delegát, který lze použít ke konfiguraci nebo nahrazení `HttpMessageHandler` používaného k odeslání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-382">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="0b7ba-383">Nepoužívá se pro připojení pomocí protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-383">Not used for WebSocket connections.</span></span> <span data-ttu-id="0b7ba-384">Tento delegát musí vracet hodnotu, která není null, a obdrží výchozí hodnotu jako parametr.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-384">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="0b7ba-385">Buď změňte nastavení u této výchozí hodnoty a vraťte je, nebo vraťte novou `HttpMessageHandler` instanci.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-385">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="0b7ba-386">**Při nahrazování obslužné rutiny Nezapomeňte zkopírovat nastavení, která chcete zachovat, od poskytnuté obslužné rutiny, jinak konfigurované možnosti (například soubory cookie a záhlaví) nebudou použity pro novou obslužnou rutinu.**</span><span class="sxs-lookup"><span data-stu-id="0b7ba-386">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="0b7ba-387">Proxy server HTTP, který se má použít při odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-387">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="0b7ba-388">Nastavte tuto logickou hodnotu pro odeslání výchozích přihlašovacích údajů pro požadavky HTTP a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-388">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="0b7ba-389">To umožňuje použití ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-389">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="0b7ba-390">Delegát, který lze použít ke konfiguraci dalších možností protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-390">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="0b7ba-391">Přijímá instanci [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , která se dá použít ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-391">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="0b7ba-392">JavaScript</span><span class="sxs-lookup"><span data-stu-id="0b7ba-392">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="0b7ba-393">Možnost JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="0b7ba-393">JavaScript Option</span></span> | <span data-ttu-id="0b7ba-394">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="0b7ba-394">Default Value</span></span> | <span data-ttu-id="0b7ba-395">Popis</span><span class="sxs-lookup"><span data-stu-id="0b7ba-395">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="0b7ba-396">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-396">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="0b7ba-397">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-397">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="0b7ba-398">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-398">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="0b7ba-399">Toto nastavení se nedá povolit při použití služby signalizace Azure.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-399">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="0b7ba-400">Java</span><span class="sxs-lookup"><span data-stu-id="0b7ba-400">Java</span></span>](#tab/java)

| <span data-ttu-id="0b7ba-401">Možnost jazyka Java</span><span class="sxs-lookup"><span data-stu-id="0b7ba-401">Java Option</span></span> | <span data-ttu-id="0b7ba-402">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="0b7ba-402">Default Value</span></span> | <span data-ttu-id="0b7ba-403">Popis</span><span class="sxs-lookup"><span data-stu-id="0b7ba-403">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="0b7ba-404">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-404">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="0b7ba-405">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-405">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="0b7ba-406">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-406">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="0b7ba-407">Toto nastavení se nedá povolit při použití služby signalizace Azure.</span><span class="sxs-lookup"><span data-stu-id="0b7ba-407">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="0b7ba-408">`withHeader``withHeaders`</span><span class="sxs-lookup"><span data-stu-id="0b7ba-408">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="0b7ba-409">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="0b7ba-409">Empty</span></span> | <span data-ttu-id="0b7ba-410">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="0b7ba-410">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="0b7ba-411">V klientu .NET můžete tyto možnosti upravit pomocí delegáta možností, který je k `WithUrl`dispozici pro:</span><span class="sxs-lookup"><span data-stu-id="0b7ba-411">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="0b7ba-412">V klientovi JavaScriptu lze tyto možnosti poskytnout v objektu jazyka JavaScript, který je k dispozici pro `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="0b7ba-412">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="0b7ba-413">V klientovi Java lze tyto možnosti nakonfigurovat s metodami `HttpHubConnectionBuilder` vrácenými z`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="0b7ba-413">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="0b7ba-414">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="0b7ba-414">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>
