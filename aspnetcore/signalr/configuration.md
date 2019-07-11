---
title: Konfigurace jádra SignalR technologie ASP.NET
author: bradygaster
description: Zjistěte, jak nakonfigurovat aplikace SignalR technologie ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 06/03/2019
uid: signalr/configuration
ms.openlocfilehash: 8c9fcaecb04555718f5da6a42a8e56c258e795af
ms.sourcegitcommit: 8516b586541e6ba402e57228e356639b85dfb2b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67813454"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="5916f-103">Konfigurace jádra SignalR technologie ASP.NET</span><span class="sxs-lookup"><span data-stu-id="5916f-103">ASP.NET Core SignalR configuration</span></span>

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="5916f-104">Serializace JSON/MessagePack možnosti</span><span class="sxs-lookup"><span data-stu-id="5916f-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="5916f-105">Funkce SignalR technologie ASP.NET Core podporuje dva protokoly pro kódování zprávy: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="5916f-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="5916f-106">Všechny protokoly, které obsahuje možnosti konfigurace serializace.</span><span class="sxs-lookup"><span data-stu-id="5916f-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="5916f-107">Serializace JSON lze nastavit na serveru pomocí [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) metody rozšíření, které mohou být přidány po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v vaše `Startup.ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="5916f-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="5916f-108">`AddJsonProtocol` Metoda přijímá delegát, který přijímá `options` objektu.</span><span class="sxs-lookup"><span data-stu-id="5916f-108">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="5916f-109">[PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) vlastnost k tomuto objektu je JSON.NET `JsonSerializerSettings` objekt, který můžete použít ke konfiguraci serializace argumenty a návratové hodnoty.</span><span class="sxs-lookup"><span data-stu-id="5916f-109">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="5916f-110">Najdete v článku [JSON.NET dokumentaci](https://www.newtonsoft.com/json/help/html/Introduction.htm) další podrobnosti.</span><span class="sxs-lookup"><span data-stu-id="5916f-110">See the [JSON.NET Documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm) for more details.</span></span>

<span data-ttu-id="5916f-111">Jako příklad konfigurace serializátoru, který chcete použít místo výchozí názvy "camelCase", "PascalCase" názvy vlastností, pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="5916f-111">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
        new DefaultContractResolver();
    });
```

<span data-ttu-id="5916f-112">V klientovi .NET, stejné `AddJsonProtocol` – metoda rozšíření existuje v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="5916f-112">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="5916f-113">`Microsoft.Extensions.DependencyInjection` Obor názvů musí být importován do resolve – metoda rozšíření:</span><span class="sxs-lookup"><span data-stu-id="5916f-113">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="5916f-114">Není možné konfigurovat pomocí serializace JSON klienta jazyka JavaScript v tuto chvíli.</span><span class="sxs-lookup"><span data-stu-id="5916f-114">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="5916f-115">MessagePack možnosti serializace</span><span class="sxs-lookup"><span data-stu-id="5916f-115">MessagePack serialization options</span></span>

<span data-ttu-id="5916f-116">Poskytnutím delegáta, kterého lze nakonfigurovat MessagePack serializace [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) volání.</span><span class="sxs-lookup"><span data-stu-id="5916f-116">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="5916f-117">Zobrazit [MessagePack v knihovně SignalR](xref:signalr/messagepackhubprotocol) další podrobnosti.</span><span class="sxs-lookup"><span data-stu-id="5916f-117">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="5916f-118">Není možné konfigurovat MessagePack serializace pomocí jazyka JavaScript klienta v tuto chvíli.</span><span class="sxs-lookup"><span data-stu-id="5916f-118">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="5916f-119">Konfigurovat možnosti serveru</span><span class="sxs-lookup"><span data-stu-id="5916f-119">Configure server options</span></span>

<span data-ttu-id="5916f-120">Následující tabulka popisuje možnosti pro konfiguraci rozbočovače SignalR:</span><span class="sxs-lookup"><span data-stu-id="5916f-120">The following table describes options for configuring SignalR hubs:</span></span>

::: moniker range=">= aspnetcore-3.0"

| <span data-ttu-id="5916f-121">Možnost</span><span class="sxs-lookup"><span data-stu-id="5916f-121">Option</span></span> | <span data-ttu-id="5916f-122">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="5916f-122">Default Value</span></span> | <span data-ttu-id="5916f-123">Popis</span><span class="sxs-lookup"><span data-stu-id="5916f-123">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="5916f-124">30 sekund</span><span class="sxs-lookup"><span data-stu-id="5916f-124">30 seconds</span></span> | <span data-ttu-id="5916f-125">Server bude předpokládat, že klient odpojen Pokud neobdržel zprávu (včetně udržování) v tomto intervalu.</span><span class="sxs-lookup"><span data-stu-id="5916f-125">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="5916f-126">Může to trvat déle než tento interval časového limitu klienta ve skutečnosti označit odpojené kvůli jak tato možnost je implementovaná.</span><span class="sxs-lookup"><span data-stu-id="5916f-126">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="5916f-127">Doporučená hodnota je double `KeepAliveInterval` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="5916f-127">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="5916f-128">15 sekund</span><span class="sxs-lookup"><span data-stu-id="5916f-128">15 seconds</span></span> | <span data-ttu-id="5916f-129">Pokud klient nebude odeslat zprávu handshake počáteční v tomto časovém intervalu, je připojení ukončeno.</span><span class="sxs-lookup"><span data-stu-id="5916f-129">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="5916f-130">Toto je upřesňující nastavení, by měla být změněna pouze v případě chyby časového limitu handshake dochází z důvodu závažné sítích s latencí.</span><span class="sxs-lookup"><span data-stu-id="5916f-130">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="5916f-131">Další podrobnosti o procesu najdete v článku [specifikace protokolu rozbočovače SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="5916f-131">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="5916f-132">15 sekund</span><span class="sxs-lookup"><span data-stu-id="5916f-132">15 seconds</span></span> | <span data-ttu-id="5916f-133">Pokud server není v rámci tohoto intervalu odeslal zprávu, je automaticky odeslána zpráva příkazu ping na udržení připojení otevřeného.</span><span class="sxs-lookup"><span data-stu-id="5916f-133">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="5916f-134">Při změně `KeepAliveInterval`, změnit `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="5916f-134">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="5916f-135">Doporučené `ServerTimeout` / `serverTimeoutInMilliseconds` hodnotu double `KeepAliveInterval` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="5916f-135">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="5916f-136">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="5916f-136">All installed protocols</span></span> | <span data-ttu-id="5916f-137">Protokolů podporovaných toto centrum.</span><span class="sxs-lookup"><span data-stu-id="5916f-137">Protocols supported by this hub.</span></span> <span data-ttu-id="5916f-138">Ve výchozím nastavení jsou povolené všechny protokoly, které jsou registrované na serveru, ale protokolů lze odebrat z tohoto seznamu zakázat konkrétní protokoly pro jednotlivé rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="5916f-138">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="5916f-139">Pokud `true`, podrobné zprávy o výjimkách se vrátí ke klientům, když dojde k výjimce v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="5916f-139">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="5916f-140">Výchozí hodnota je `false`, jak tyto zprávy o výjimkách mohou obsahovat citlivé údaje.</span><span class="sxs-lookup"><span data-stu-id="5916f-140">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="5916f-141">Maximální počet položek, které mohou být ukládány do vyrovnávací paměti pro klienta nahrání datových proudů.</span><span class="sxs-lookup"><span data-stu-id="5916f-141">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="5916f-142">Při dosažení tohoto limitu zpracování volání blokovaný, dokud se server zpracuje položek datového proudu.</span><span class="sxs-lookup"><span data-stu-id="5916f-142">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|

::: moniker-end

::: moniker range="= aspnetcore-2.2"

| <span data-ttu-id="5916f-143">Možnost</span><span class="sxs-lookup"><span data-stu-id="5916f-143">Option</span></span> | <span data-ttu-id="5916f-144">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="5916f-144">Default Value</span></span> | <span data-ttu-id="5916f-145">Popis</span><span class="sxs-lookup"><span data-stu-id="5916f-145">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="5916f-146">30 sekund</span><span class="sxs-lookup"><span data-stu-id="5916f-146">30 seconds</span></span> | <span data-ttu-id="5916f-147">Server bude předpokládat, že klient odpojen Pokud neobdržel zprávu (včetně udržování) v tomto intervalu.</span><span class="sxs-lookup"><span data-stu-id="5916f-147">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="5916f-148">Může to trvat déle než tento interval časového limitu klienta ve skutečnosti označit odpojené kvůli jak tato možnost je implementovaná.</span><span class="sxs-lookup"><span data-stu-id="5916f-148">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="5916f-149">Doporučená hodnota je double `KeepAliveInterval` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="5916f-149">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="5916f-150">15 sekund</span><span class="sxs-lookup"><span data-stu-id="5916f-150">15 seconds</span></span> | <span data-ttu-id="5916f-151">Pokud klient nebude odeslat zprávu handshake počáteční v tomto časovém intervalu, je připojení ukončeno.</span><span class="sxs-lookup"><span data-stu-id="5916f-151">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="5916f-152">Toto je upřesňující nastavení, by měla být změněna pouze v případě chyby časového limitu handshake dochází z důvodu závažné sítích s latencí.</span><span class="sxs-lookup"><span data-stu-id="5916f-152">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="5916f-153">Další podrobnosti o procesu najdete v článku [specifikace protokolu rozbočovače SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="5916f-153">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="5916f-154">15 sekund</span><span class="sxs-lookup"><span data-stu-id="5916f-154">15 seconds</span></span> | <span data-ttu-id="5916f-155">Pokud server není v rámci tohoto intervalu odeslal zprávu, je automaticky odeslána zpráva příkazu ping na udržení připojení otevřeného.</span><span class="sxs-lookup"><span data-stu-id="5916f-155">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="5916f-156">Při změně `KeepAliveInterval`, změnit `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="5916f-156">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="5916f-157">Doporučené `ServerTimeout` / `serverTimeoutInMilliseconds` hodnotu double `KeepAliveInterval` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="5916f-157">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="5916f-158">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="5916f-158">All installed protocols</span></span> | <span data-ttu-id="5916f-159">Protokolů podporovaných toto centrum.</span><span class="sxs-lookup"><span data-stu-id="5916f-159">Protocols supported by this hub.</span></span> <span data-ttu-id="5916f-160">Ve výchozím nastavení jsou povolené všechny protokoly, které jsou registrované na serveru, ale protokolů lze odebrat z tohoto seznamu zakázat konkrétní protokoly pro jednotlivé rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="5916f-160">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="5916f-161">Pokud `true`, podrobné zprávy o výjimkách se vrátí ke klientům, když dojde k výjimce v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="5916f-161">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="5916f-162">Výchozí hodnota je `false`, jak tyto zprávy o výjimkách mohou obsahovat citlivé údaje.</span><span class="sxs-lookup"><span data-stu-id="5916f-162">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

::: moniker range="= aspnetcore-2.1"

| <span data-ttu-id="5916f-163">Možnost</span><span class="sxs-lookup"><span data-stu-id="5916f-163">Option</span></span> | <span data-ttu-id="5916f-164">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="5916f-164">Default Value</span></span> | <span data-ttu-id="5916f-165">Popis</span><span class="sxs-lookup"><span data-stu-id="5916f-165">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="5916f-166">15 sekund</span><span class="sxs-lookup"><span data-stu-id="5916f-166">15 seconds</span></span> | <span data-ttu-id="5916f-167">Pokud klient nebude odeslat zprávu handshake počáteční v tomto časovém intervalu, je připojení ukončeno.</span><span class="sxs-lookup"><span data-stu-id="5916f-167">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="5916f-168">Toto je upřesňující nastavení, by měla být změněna pouze v případě chyby časového limitu handshake dochází z důvodu závažné sítích s latencí.</span><span class="sxs-lookup"><span data-stu-id="5916f-168">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="5916f-169">Další podrobnosti o procesu najdete v článku [specifikace protokolu rozbočovače SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="5916f-169">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="5916f-170">15 sekund</span><span class="sxs-lookup"><span data-stu-id="5916f-170">15 seconds</span></span> | <span data-ttu-id="5916f-171">Pokud server není v rámci tohoto intervalu odeslal zprávu, je automaticky odeslána zpráva příkazu ping na udržení připojení otevřeného.</span><span class="sxs-lookup"><span data-stu-id="5916f-171">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="5916f-172">Při změně `KeepAliveInterval`, změnit `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení na straně klienta.</span><span class="sxs-lookup"><span data-stu-id="5916f-172">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="5916f-173">Doporučené `ServerTimeout` / `serverTimeoutInMilliseconds` hodnotu double `KeepAliveInterval` hodnotu.</span><span class="sxs-lookup"><span data-stu-id="5916f-173">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="5916f-174">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="5916f-174">All installed protocols</span></span> | <span data-ttu-id="5916f-175">Protokolů podporovaných toto centrum.</span><span class="sxs-lookup"><span data-stu-id="5916f-175">Protocols supported by this hub.</span></span> <span data-ttu-id="5916f-176">Ve výchozím nastavení jsou povolené všechny protokoly, které jsou registrované na serveru, ale protokolů lze odebrat z tohoto seznamu zakázat konkrétní protokoly pro jednotlivé rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="5916f-176">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="5916f-177">Pokud `true`, podrobné zprávy o výjimkách se vrátí ke klientům, když dojde k výjimce v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="5916f-177">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="5916f-178">Výchozí hodnota je `false`, jak tyto zprávy o výjimkách mohou obsahovat citlivé údaje.</span><span class="sxs-lookup"><span data-stu-id="5916f-178">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

::: moniker-end

<span data-ttu-id="5916f-179">Možnosti je možné nakonfigurovat pro všechna centra poskytnutím delegáta možnosti k `AddSignalR` volání v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="5916f-179">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="5916f-180">Možnosti pro jedno centrum přepsat globální možnosti uvedené v `AddSignalR` a dá se nakonfigurovat pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span><span class="sxs-lookup"><span data-stu-id="5916f-180">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="5916f-181">Rozšířené možnosti konfigurace protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="5916f-181">Advanced HTTP configuration options</span></span>

<span data-ttu-id="5916f-182">Použití `HttpConnectionDispatcherOptions` konfigurace upřesňujících nastavení související s přenosy a správa vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="5916f-182">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="5916f-183">Tyto možnosti jsou nakonfigurované pomocí předání delegáta, kterého [MapHub\<T >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="5916f-183">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="5916f-184">Následující tabulka popisuje možnosti pro konfiguraci rozšířené možnosti HTTP SignalR technologie ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="5916f-184">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="5916f-185">Možnost</span><span class="sxs-lookup"><span data-stu-id="5916f-185">Option</span></span> | <span data-ttu-id="5916f-186">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="5916f-186">Default Value</span></span> | <span data-ttu-id="5916f-187">Popis</span><span class="sxs-lookup"><span data-stu-id="5916f-187">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="5916f-188">32 KB.</span><span class="sxs-lookup"><span data-stu-id="5916f-188">32 KB</span></span> | <span data-ttu-id="5916f-189">Maximální počet bajtů přijatých z klienta, který vyrovnávací paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="5916f-189">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="5916f-190">Zvýšení hodnoty tuto umožňuje serveru pro příjem větší zprávy, ale může mít negativní vliv na využití paměti.</span><span class="sxs-lookup"><span data-stu-id="5916f-190">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="5916f-191">Data jsou automaticky shromážděna z `Authorize` atributy použité u třídy rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="5916f-191">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="5916f-192">Seznam [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objekty sloužící k určení, pokud je klient autorizaci k připojení k rozbočovači.</span><span class="sxs-lookup"><span data-stu-id="5916f-192">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="5916f-193">32 KB.</span><span class="sxs-lookup"><span data-stu-id="5916f-193">32 KB</span></span> | <span data-ttu-id="5916f-194">Maximální počet bajtů odeslaných aplikace, která vyrovnávací paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="5916f-194">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="5916f-195">Zvýšení hodnoty tuto umožňuje serveru odesílat větší zprávy, ale může mít negativní vliv na využití paměti.</span><span class="sxs-lookup"><span data-stu-id="5916f-195">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="5916f-196">Všechny přenosy jsou povolené.</span><span class="sxs-lookup"><span data-stu-id="5916f-196">All Transports are enabled.</span></span> | <span data-ttu-id="5916f-197">Bitová maska z `HttpTransportType` hodnoty, které můžete omezit přenosy klienta můžete použít pro připojení.</span><span class="sxs-lookup"><span data-stu-id="5916f-197">A bitmask of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="5916f-198">Níže jsou uvedeny.</span><span class="sxs-lookup"><span data-stu-id="5916f-198">See below.</span></span> | <span data-ttu-id="5916f-199">Další možnosti specifické pro dlouhé dotazování přenosu.</span><span class="sxs-lookup"><span data-stu-id="5916f-199">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="5916f-200">Níže jsou uvedeny.</span><span class="sxs-lookup"><span data-stu-id="5916f-200">See below.</span></span> | <span data-ttu-id="5916f-201">Další možnosti specifické pro přenos objekty Websocket.</span><span class="sxs-lookup"><span data-stu-id="5916f-201">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="5916f-202">Dlouhý interval dotazování přenosu má další možnosti, které lze konfigurovat pomocí `LongPolling` vlastnost:</span><span class="sxs-lookup"><span data-stu-id="5916f-202">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="5916f-203">Možnost</span><span class="sxs-lookup"><span data-stu-id="5916f-203">Option</span></span> | <span data-ttu-id="5916f-204">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="5916f-204">Default Value</span></span> | <span data-ttu-id="5916f-205">Popis</span><span class="sxs-lookup"><span data-stu-id="5916f-205">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="5916f-206">90 sekund</span><span class="sxs-lookup"><span data-stu-id="5916f-206">90 seconds</span></span> | <span data-ttu-id="5916f-207">Maximální množství času na server čeká na zprávu k odeslání do klienta před ukončením žádosti o jednotné hlasování.</span><span class="sxs-lookup"><span data-stu-id="5916f-207">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="5916f-208">Snížení hodnoty způsobí, že klient k vydávání nových žádostí o dotazování častěji.</span><span class="sxs-lookup"><span data-stu-id="5916f-208">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="5916f-209">Přenos pomocí protokolu WebSocket má další možnosti, které lze konfigurovat pomocí `WebSockets` vlastnost:</span><span class="sxs-lookup"><span data-stu-id="5916f-209">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="5916f-210">Možnost</span><span class="sxs-lookup"><span data-stu-id="5916f-210">Option</span></span> | <span data-ttu-id="5916f-211">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="5916f-211">Default Value</span></span> | <span data-ttu-id="5916f-212">Popis</span><span class="sxs-lookup"><span data-stu-id="5916f-212">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="5916f-213">5 sekund</span><span class="sxs-lookup"><span data-stu-id="5916f-213">5 seconds</span></span> | <span data-ttu-id="5916f-214">Po zavření serveru, pokud se klientovi nepodaří zavřete v tomto časovém intervalu, připojení se ukončí.</span><span class="sxs-lookup"><span data-stu-id="5916f-214">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="5916f-215">Delegát, který je možné nastavit `Sec-WebSocket-Protocol` vlastní hodnoty záhlaví.</span><span class="sxs-lookup"><span data-stu-id="5916f-215">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="5916f-216">Delegát přijme hodnoty vyžádané klientem jako vstup a měl by vrátit na požadovanou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="5916f-216">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="5916f-217">Konfigurovat možnosti klienta</span><span class="sxs-lookup"><span data-stu-id="5916f-217">Configure client options</span></span>

<span data-ttu-id="5916f-218">Možnosti klienta lze nakonfigurovat podle `HubConnectionBuilder` typ (k dispozici v klientech .NET a JavaScript).</span><span class="sxs-lookup"><span data-stu-id="5916f-218">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="5916f-219">Je také k dispozici v jazyce Java pro klienta, ale `HttpHubConnectionBuilder` podtřídy je, co obsahuje Tvůrce možnosti konfigurace, stejně jako na `HubConnection` samotný.</span><span class="sxs-lookup"><span data-stu-id="5916f-219">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="5916f-220">Konfigurace protokolování</span><span class="sxs-lookup"><span data-stu-id="5916f-220">Configure logging</span></span>

<span data-ttu-id="5916f-221">Protokolování je nakonfigurován v klientu .NET pomocí `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="5916f-221">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="5916f-222">Protokolování poskytovatelů a filtry můžete zaregistrovat stejně, jako jsou na serveru.</span><span class="sxs-lookup"><span data-stu-id="5916f-222">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="5916f-223">Zobrazit [protokolování v ASP.NET Core](xref:fundamentals/logging/index) Další informace naleznete v dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="5916f-223">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="5916f-224">Aby bylo možné zaregistrovat poskytovatele protokolování, je nutné nainstalovat potřebné balíčky.</span><span class="sxs-lookup"><span data-stu-id="5916f-224">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="5916f-225">Najdete v článku [vestavěné protokolování poskytovatelé](xref:fundamentals/logging/index#built-in-logging-providers) část dokumentace pro úplný seznam.</span><span class="sxs-lookup"><span data-stu-id="5916f-225">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="5916f-226">Například pokud chcete povolit protokolování konzoly, nainstalujte `Microsoft.Extensions.Logging.Console` balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="5916f-226">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="5916f-227">Volání `AddConsole` – metoda rozšíření:</span><span class="sxs-lookup"><span data-stu-id="5916f-227">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="5916f-228">V klientovi JavaScript podobný `configureLogging` metody existuje.</span><span class="sxs-lookup"><span data-stu-id="5916f-228">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="5916f-229">Zadejte `LogLevel` hodnotu, která minimální úroveň zprávy protokolu k vytvoření.</span><span class="sxs-lookup"><span data-stu-id="5916f-229">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="5916f-230">Protokoly se zapisují do okna konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="5916f-230">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="5916f-231">Místo `LogLevel` hodnoty, můžete zadat taky `string` hodnotu představující název úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="5916f-231">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="5916f-232">To je užitečné při konfiguraci SignalR protokolování v prostředí, ve kterém nemáte přístup k `LogLevel` konstanty.</span><span class="sxs-lookup"><span data-stu-id="5916f-232">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="5916f-233">Následující tabulka uvádí dostupné protokolu úrovně.</span><span class="sxs-lookup"><span data-stu-id="5916f-233">The following table lists the available log levels.</span></span> <span data-ttu-id="5916f-234">Hodnota poskytnete `configureLogging` nastaví **minimální** úrovně, která se zaznamenala protokolování.</span><span class="sxs-lookup"><span data-stu-id="5916f-234">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="5916f-235">Zprávy zaprotokolované na této úrovni **nebo úrovně uvedených za něj v tabulce**, budou protokolovány.</span><span class="sxs-lookup"><span data-stu-id="5916f-235">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="5916f-236">odkazy řetězců</span><span class="sxs-lookup"><span data-stu-id="5916f-236">string</span></span> | <span data-ttu-id="5916f-237">LogLevel</span><span class="sxs-lookup"><span data-stu-id="5916f-237">LogLevel</span></span> |
| - | - |
| `"trace"` | `LogLevel.Trace` |
| `"debug"` | `LogLevel.Debug` |
| <span data-ttu-id="5916f-238">`"info"` **Nebo** `"information"`</span><span class="sxs-lookup"><span data-stu-id="5916f-238">`"info"` **or** `"information"`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="5916f-239">`"warn"` **Nebo** `"warning"`</span><span class="sxs-lookup"><span data-stu-id="5916f-239">`"warn"` **or** `"warning"`</span></span> | `LogLevel.Warning` |
| `"error"` | `LogLevel.Error` |
| `"critical"` | `LogLevel.Critical` |
| `"none"` | `LogLevel.None` |

::: moniker-end

> [!NOTE]
> <span data-ttu-id="5916f-240">Chcete-li zakázat protokolování úplně, zadejte `signalR.LogLevel.None` v `configureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="5916f-240">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="5916f-241">Další informace o protokolování, najdete v článku [dokumentace k nástroji Diagnostika SignalR](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="5916f-241">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="5916f-242">Používá klientskou sadou SignalR Java [SLF4J](https://www.slf4j.org/) knihovny pro protokolování.</span><span class="sxs-lookup"><span data-stu-id="5916f-242">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="5916f-243">Jde o rozhraní API vysoké úrovně protokolování, který umožňuje uživatelům knihovny zvolili vlastní implementace protokolování konkrétní díky možnostem protokolování konkrétní závislost.</span><span class="sxs-lookup"><span data-stu-id="5916f-243">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="5916f-244">Následující fragment kódu ukazuje, jak používat `java.util.logging` s klientskou sadou SignalR Java.</span><span class="sxs-lookup"><span data-stu-id="5916f-244">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="5916f-245">Pokud nenakonfigurujete přihlášení závislostí, načte SLF4J protokolovacího nástroje výchozí žádné operace s tímto upozorněním:</span><span class="sxs-lookup"><span data-stu-id="5916f-245">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="5916f-246">To můžete bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="5916f-246">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="5916f-247">Nakonfigurovat povolené přenosy</span><span class="sxs-lookup"><span data-stu-id="5916f-247">Configure allowed transports</span></span>

<span data-ttu-id="5916f-248">Přenosy systém signalr se dá nakonfigurovat v `WithUrl` volání (`withUrl` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="5916f-248">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="5916f-249">Bitový OR hodnoty `HttpTransportType` slouží k omezení klienta používat pouze zadaný přenosy.</span><span class="sxs-lookup"><span data-stu-id="5916f-249">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="5916f-250">Ve výchozím nastavení jsou povolené všechny přenosy.</span><span class="sxs-lookup"><span data-stu-id="5916f-250">All transports are enabled by default.</span></span>

<span data-ttu-id="5916f-251">Například zakázat přenos Server-Sent události, ale povolte připojení objekty Websocket a dlouhý interval dotazování:</span><span class="sxs-lookup"><span data-stu-id="5916f-251">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="5916f-252">V klientovi JavaScript přenosy jsou nakonfigurovány tak, že nastavíte `transport` na objekt možnosti poskytnuté `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="5916f-252">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="5916f-253">V této verzi jazyka Java klienta websocket je k dispozici pouze přenosu.</span><span class="sxs-lookup"><span data-stu-id="5916f-253">In this version of the Java client websockets is the only available transport.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-3.0"

<span data-ttu-id="5916f-254">Klientskou sadou Java, přenos je vybraná s `withTransport` metodu `HttpHubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="5916f-254">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="5916f-255">Klientskou sadou Java ve výchozím nastavení používá přenos objekty Websocket.</span><span class="sxs-lookup"><span data-stu-id="5916f-255">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="5916f-256">Klientskou sadou SignalR Java přenosu záložní ještě nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="5916f-256">The SignalR Java client doesn't support transport fallback yet.</span></span>

::: moniker-end

### <a name="configure-bearer-authentication"></a><span data-ttu-id="5916f-257">Konfigurace ověřování nosiče</span><span class="sxs-lookup"><span data-stu-id="5916f-257">Configure bearer authentication</span></span>

<span data-ttu-id="5916f-258">Pokud chcete poskytnout ověřovací data spolu s knihovnou SignalR požadavky, použijte `AccessTokenProvider` možnost (`accessTokenFactory` v JavaScriptu) k určení funkce, která vrací požadovaný přístupový token.</span><span class="sxs-lookup"><span data-stu-id="5916f-258">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="5916f-259">V klientovi .NET Tento přístupový token předaný jako HTTP "Ověřování nosiče" token (použití `Authorization` záhlaví s typem `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="5916f-259">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="5916f-260">V klientovi JavaScript přístupový token se používá jako nosný token **s výjimkou** v několika případech, kdy prohlížeč rozhraní API omezit schopnosti uplatňovat hlavičky (konkrétně v Server-Sent události a protokoly Websocket požadavky).</span><span class="sxs-lookup"><span data-stu-id="5916f-260">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="5916f-261">V těchto případech je přístupový token k dispozici jako hodnotu řetězce dotazu `access_token`.</span><span class="sxs-lookup"><span data-stu-id="5916f-261">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="5916f-262">V klientovi .NET `AccessTokenProvider` možnost lze zadat pomocí možnosti delegáta v `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="5916f-262">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="5916f-263">V klientovi JavaScript přístupový token je nakonfigurovaný tak, že nastavíte `accessTokenFactory` na objekt možnosti v `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="5916f-263">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="5916f-264">V klientovi SignalR Java můžete nakonfigurovat nosný token pro účely ověření zadáním tokenu továrnu přístup ke [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="5916f-264">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="5916f-265">Použití [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) k poskytování [RxJava](https://github.com/ReactiveX/RxJava) [jeden\<řetězec >](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="5916f-265">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="5916f-266">Voláním [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), můžou psát logiku k vytvoření přístupové tokeny pro vašeho klienta.</span><span class="sxs-lookup"><span data-stu-id="5916f-266">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="5916f-267">Konfigurace časového limitu a udržování možnosti</span><span class="sxs-lookup"><span data-stu-id="5916f-267">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="5916f-268">Další možnosti pro konfiguraci časového limitu a zachování chování, které jsou k dispozici na `HubConnection` samotného objektu:</span><span class="sxs-lookup"><span data-stu-id="5916f-268">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="5916f-269">.NET</span><span class="sxs-lookup"><span data-stu-id="5916f-269">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="5916f-270">Možnost</span><span class="sxs-lookup"><span data-stu-id="5916f-270">Option</span></span> | <span data-ttu-id="5916f-271">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="5916f-271">Default value</span></span> | <span data-ttu-id="5916f-272">Popis</span><span class="sxs-lookup"><span data-stu-id="5916f-272">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="5916f-273">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="5916f-273">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="5916f-274">Časový limit pro aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="5916f-274">Timeout for server activity.</span></span> <span data-ttu-id="5916f-275">Pokud server není v tomto intervalu odeslal zprávu, klient bude považovat za server odpojen a aktivační události `Closed` událostí (`onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="5916f-275">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="5916f-276">Tato hodnota musí být dostatečně velký pro zprávu ping na odeslaných ze serveru **a** klient obdrží během intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="5916f-276">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="5916f-277">Doporučená hodnota je čísla alespoň double server `KeepAliveInterval` hodnotu, abyste měli čas pro příkazy ping pro zjištění dorazí.</span><span class="sxs-lookup"><span data-stu-id="5916f-277">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="5916f-278">15 sekund</span><span class="sxs-lookup"><span data-stu-id="5916f-278">15 seconds</span></span> | <span data-ttu-id="5916f-279">Časový limit pro počáteční server handshake.</span><span class="sxs-lookup"><span data-stu-id="5916f-279">Timeout for initial server handshake.</span></span> <span data-ttu-id="5916f-280">Pokud server není v tomto intervalu odeslání odpovědi handshake, klient zruší handshake a aktivační události `Closed` událostí (`onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="5916f-280">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="5916f-281">Toto je upřesňující nastavení, by měla být změněna pouze v případě chyby časového limitu handshake dochází z důvodu závažné sítích s latencí.</span><span class="sxs-lookup"><span data-stu-id="5916f-281">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="5916f-282">Další podrobnosti o procesu najdete v článku [specifikace protokolu rozbočovače SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="5916f-282">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="5916f-283">V klientovi .NET jsou zadané hodnoty časového limitu jako `TimeSpan` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="5916f-283">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="5916f-284">JavaScript</span><span class="sxs-lookup"><span data-stu-id="5916f-284">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="5916f-285">Možnost</span><span class="sxs-lookup"><span data-stu-id="5916f-285">Option</span></span> | <span data-ttu-id="5916f-286">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="5916f-286">Default value</span></span> | <span data-ttu-id="5916f-287">Popis</span><span class="sxs-lookup"><span data-stu-id="5916f-287">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="5916f-288">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="5916f-288">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="5916f-289">Časový limit pro aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="5916f-289">Timeout for server activity.</span></span> <span data-ttu-id="5916f-290">Pokud server není v tomto intervalu odeslal zprávu, klient bude považovat za server odpojen a aktivační události `onclose` událostí.</span><span class="sxs-lookup"><span data-stu-id="5916f-290">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="5916f-291">Tato hodnota musí být dostatečně velký pro zprávu ping na odeslaných ze serveru **a** klient obdrží během intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="5916f-291">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="5916f-292">Doporučená hodnota je čísla alespoň double server `KeepAliveInterval` hodnotu, abyste měli čas pro příkazy ping pro zjištění dorazí.</span><span class="sxs-lookup"><span data-stu-id="5916f-292">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="5916f-293">Java</span><span class="sxs-lookup"><span data-stu-id="5916f-293">Java</span></span>](#tab/java)

| <span data-ttu-id="5916f-294">Možnost</span><span class="sxs-lookup"><span data-stu-id="5916f-294">Option</span></span> | <span data-ttu-id="5916f-295">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="5916f-295">Default value</span></span> | <span data-ttu-id="5916f-296">Popis</span><span class="sxs-lookup"><span data-stu-id="5916f-296">Description</span></span> |
| ----------- | ------------- | ----------- |
|<span data-ttu-id="5916f-297">`getServerTimeout``setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="5916f-297">`getServerTimeout` `setServerTimeout`</span></span> | <span data-ttu-id="5916f-298">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="5916f-298">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="5916f-299">Časový limit pro aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="5916f-299">Timeout for server activity.</span></span> <span data-ttu-id="5916f-300">Pokud server není v tomto intervalu odeslal zprávu, klient bude považovat za server odpojen a aktivační události `onClose` událostí.</span><span class="sxs-lookup"><span data-stu-id="5916f-300">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="5916f-301">Tato hodnota musí být dostatečně velký pro zprávu ping na odeslaných ze serveru **a** klient obdrží během intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="5916f-301">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="5916f-302">Doporučená hodnota je čísla alespoň double server `KeepAliveInterval` hodnotu, abyste měli čas pro příkazy ping pro zjištění dorazí.</span><span class="sxs-lookup"><span data-stu-id="5916f-302">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="5916f-303">15 sekund</span><span class="sxs-lookup"><span data-stu-id="5916f-303">15 seconds</span></span> | <span data-ttu-id="5916f-304">Časový limit pro počáteční server handshake.</span><span class="sxs-lookup"><span data-stu-id="5916f-304">Timeout for initial server handshake.</span></span> <span data-ttu-id="5916f-305">Pokud server není v tomto intervalu odeslání odpovědi handshake, klient zruší handshake a aktivační události `onClose` událostí.</span><span class="sxs-lookup"><span data-stu-id="5916f-305">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="5916f-306">Toto je upřesňující nastavení, by měla být změněna pouze v případě chyby časového limitu handshake dochází z důvodu závažné sítích s latencí.</span><span class="sxs-lookup"><span data-stu-id="5916f-306">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="5916f-307">Další podrobnosti o procesu najdete v článku [specifikace protokolu rozbočovače SignalR](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="5916f-307">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="5916f-308">Konfigurace dalších možností</span><span class="sxs-lookup"><span data-stu-id="5916f-308">Configure additional options</span></span>

<span data-ttu-id="5916f-309">Další možnosti se dá nakonfigurovat v `WithUrl` (`withUrl` v JavaScriptu) metodu na `HubConnectionBuilder` nebo v různých rozhraní API pro konfiguraci na `HttpHubConnectionBuilder` v klientskou sadou Java:</span><span class="sxs-lookup"><span data-stu-id="5916f-309">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="5916f-310">.NET</span><span class="sxs-lookup"><span data-stu-id="5916f-310">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="5916f-311">.NET – možnost</span><span class="sxs-lookup"><span data-stu-id="5916f-311">.NET Option</span></span> |  <span data-ttu-id="5916f-312">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="5916f-312">Default value</span></span> | <span data-ttu-id="5916f-313">Popis</span><span class="sxs-lookup"><span data-stu-id="5916f-313">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="5916f-314">Funkce vrátí řetězec, který je k dispozici jako token nosiče ověřování v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="5916f-314">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="5916f-315">Nastavte na `true` přeskočit krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="5916f-315">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="5916f-316">**Podporuje jenom při přenosu objekty Websocket je jediný povolený přenos**.</span><span class="sxs-lookup"><span data-stu-id="5916f-316">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="5916f-317">Toto nastavení není možné při použití služby Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="5916f-317">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="5916f-318">prázdný</span><span class="sxs-lookup"><span data-stu-id="5916f-318">Empty</span></span> | <span data-ttu-id="5916f-319">Kolekce certifikáty TLS odeslat k ověření požadavků.</span><span class="sxs-lookup"><span data-stu-id="5916f-319">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="5916f-320">prázdný</span><span class="sxs-lookup"><span data-stu-id="5916f-320">Empty</span></span> | <span data-ttu-id="5916f-321">Kolekce souborů cookie protokolu HTTP k odeslání při každé žádosti protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="5916f-321">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="5916f-322">prázdný</span><span class="sxs-lookup"><span data-stu-id="5916f-322">Empty</span></span> | <span data-ttu-id="5916f-323">Přihlašovací údaje pro odesílání při každé žádosti protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="5916f-323">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="5916f-324">5 sekund</span><span class="sxs-lookup"><span data-stu-id="5916f-324">5 seconds</span></span> | <span data-ttu-id="5916f-325">Pouze objekty Websocket.</span><span class="sxs-lookup"><span data-stu-id="5916f-325">WebSockets only.</span></span> <span data-ttu-id="5916f-326">Maximální množství času, klient počká po uzavření pro server a potvrďte žádosti o uzavření.</span><span class="sxs-lookup"><span data-stu-id="5916f-326">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="5916f-327">Pokud server není v tuto chvíli Potvrdit uzavření, klient neodpojí.</span><span class="sxs-lookup"><span data-stu-id="5916f-327">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="5916f-328">prázdný</span><span class="sxs-lookup"><span data-stu-id="5916f-328">Empty</span></span> | <span data-ttu-id="5916f-329">Mapa dodatečné hlavičky protokolu HTTP k odeslání při každé žádosti protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="5916f-329">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="5916f-330">Delegát, který lze použít ke konfiguraci nebo nahradit `HttpMessageHandler` používaný k odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="5916f-330">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="5916f-331">Není možné použít u připojení pomocí protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="5916f-331">Not used for WebSocket connections.</span></span> <span data-ttu-id="5916f-332">Tento delegát musí vrátit nenulovou hodnotu, a přijímá jako parametr výchozí hodnotu.</span><span class="sxs-lookup"><span data-stu-id="5916f-332">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="5916f-333">Upravte nastavení podle této výchozí hodnoty a vrácení nebo vrátí novou `HttpMessageHandler` instance.</span><span class="sxs-lookup"><span data-stu-id="5916f-333">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="5916f-334">**Při nahrazování obslužnou rutinu nezapomeňte si zkopírovat na nastavení, která chcete zachovat od zadané obslužné rutiny, v opačném případě nakonfigurovaných možností (například soubory cookie a hlavičky) se nevztahuje na novou obslužnou rutinu.**</span><span class="sxs-lookup"><span data-stu-id="5916f-334">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="5916f-335">Proxy server HTTP při odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="5916f-335">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="5916f-336">Nastavte tuto logickou hodnotu k odeslání výchozí přihlašovací údaje pro požadavky HTTP a Websocket.</span><span class="sxs-lookup"><span data-stu-id="5916f-336">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="5916f-337">To umožňuje použití ověřování Windows.</span><span class="sxs-lookup"><span data-stu-id="5916f-337">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="5916f-338">Delegát, který slouží k nakonfigurování dalších možností protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="5916f-338">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="5916f-339">Obdrží instanci [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , který lze použít ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="5916f-339">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="5916f-340">JavaScript</span><span class="sxs-lookup"><span data-stu-id="5916f-340">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="5916f-341">Možnost jazyka JavaScript</span><span class="sxs-lookup"><span data-stu-id="5916f-341">JavaScript Option</span></span> | <span data-ttu-id="5916f-342">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="5916f-342">Default Value</span></span> | <span data-ttu-id="5916f-343">Popis</span><span class="sxs-lookup"><span data-stu-id="5916f-343">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="5916f-344">Funkce vrátí řetězec, který je k dispozici jako token nosiče ověřování v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="5916f-344">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="5916f-345">Nastavte na `true` přeskočit krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="5916f-345">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="5916f-346">**Podporuje jenom při přenosu objekty Websocket je jediný povolený přenos**.</span><span class="sxs-lookup"><span data-stu-id="5916f-346">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="5916f-347">Toto nastavení není možné při použití služby Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="5916f-347">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="5916f-348">Java</span><span class="sxs-lookup"><span data-stu-id="5916f-348">Java</span></span>](#tab/java)

| <span data-ttu-id="5916f-349">Možnost Java</span><span class="sxs-lookup"><span data-stu-id="5916f-349">Java Option</span></span> | <span data-ttu-id="5916f-350">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="5916f-350">Default Value</span></span> | <span data-ttu-id="5916f-351">Popis</span><span class="sxs-lookup"><span data-stu-id="5916f-351">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="5916f-352">Funkce vrátí řetězec, který je k dispozici jako token nosiče ověřování v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="5916f-352">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="5916f-353">Nastavte na `true` přeskočit krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="5916f-353">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="5916f-354">**Podporuje jenom při přenosu objekty Websocket je jediný povolený přenos**.</span><span class="sxs-lookup"><span data-stu-id="5916f-354">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="5916f-355">Toto nastavení není možné při použití služby Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="5916f-355">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="5916f-356">`withHeader``withHeaders`</span><span class="sxs-lookup"><span data-stu-id="5916f-356">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="5916f-357">prázdný</span><span class="sxs-lookup"><span data-stu-id="5916f-357">Empty</span></span> | <span data-ttu-id="5916f-358">Mapa dodatečné hlavičky protokolu HTTP k odeslání při každé žádosti protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="5916f-358">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="5916f-359">V klientovi .NET tyto možnosti je možné upravovat prostřednictvím delegáta možnosti poskytnuté `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="5916f-359">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="5916f-360">V klientovi JavaScript tyto možnosti lze zadat v objektu jazyka JavaScript poskytuje k `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="5916f-360">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="5916f-361">V klientskou sadou Java, tyto možnosti v lze nakonfigurovat pomocí metod `HttpHubConnectionBuilder` vrácených `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="5916f-361">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="5916f-362">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="5916f-362">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>
