---
title: SignalRKonfigurace ASP.NET Core
author: bradygaster
description: Naučte se konfigurovat SignalR aplikace ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 809bdc777b6307314a7bcde82ab5e0c6888db99b
ms.sourcegitcommit: 4437f4c149f1ef6c28796dcfaa2863b4c088169c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2020
ms.locfileid: "85074465"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="2b287-103">SignalRKonfigurace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2b287-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="2b287-104">Možnosti serializace JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="2b287-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="2b287-105">ASP.NET Core SignalR podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="2b287-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="2b287-106">Každý protokol má možnosti konfigurace serializace.</span><span class="sxs-lookup"><span data-stu-id="2b287-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="2b287-107">Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="2b287-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="2b287-108">`AddJsonProtocol`lze přidat po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2b287-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2b287-109">`AddJsonProtocol`Metoda přebírá delegáta, který přijímá `options` objekt.</span><span class="sxs-lookup"><span data-stu-id="2b287-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="2b287-110">Vlastnost [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) tohoto objektu je `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objekt, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="2b287-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="2b287-111">Další informace najdete v [System.Text.Jsv dokumentaci](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="2b287-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="2b287-112">Chcete-li například nakonfigurovat serializátor, aby neměnil velká a malá písmena názvů vlastností namísto výchozích názvů "camelCase", použijte následující kód v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2b287-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="2b287-113">V klientovi .NET `AddJsonProtocol` existuje stejná rozšiřující metoda v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="2b287-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="2b287-114">`Microsoft.Extensions.DependencyInjection`Obor názvů musí být importován, aby bylo možné přeložit metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="2b287-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="2b287-115">V tuto chvíli není možné konfigurovat serializaci JSON v klientovi jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2b287-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="2b287-116">Přepnout na Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="2b287-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="2b287-117">Pokud potřebujete funkce nástroje `Newtonsoft.Json` , které nejsou podporované v systému `System.Text.Json` , přečtěte si téma [Přepnutí na Newtonsoft.Jsna](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="2b287-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="2b287-118">Možnosti serializace MessagePack</span><span class="sxs-lookup"><span data-stu-id="2b287-118">MessagePack serialization options</span></span>

<span data-ttu-id="2b287-119">Serializaci MessagePack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="2b287-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="2b287-120">Další podrobnosti najdete [v SignalR tématu MessagePack](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="2b287-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="2b287-121">V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi v jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2b287-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="2b287-122">Konfigurovat možnosti serveru</span><span class="sxs-lookup"><span data-stu-id="2b287-122">Configure server options</span></span>

<span data-ttu-id="2b287-123">Následující tabulka popisuje možnosti konfigurace SignalR Center:</span><span class="sxs-lookup"><span data-stu-id="2b287-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="2b287-124">Možnost</span><span class="sxs-lookup"><span data-stu-id="2b287-124">Option</span></span> | <span data-ttu-id="2b287-125">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-125">Default Value</span></span> | <span data-ttu-id="2b287-126">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="2b287-127">30 sekund</span><span class="sxs-lookup"><span data-stu-id="2b287-127">30 seconds</span></span> | <span data-ttu-id="2b287-128">Server bude předpokládat, že je klient odpojený, pokud v tomto intervalu neobdržel zprávu (včetně Keep-Alive).</span><span class="sxs-lookup"><span data-stu-id="2b287-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="2b287-129">Může trvat delší dobu, než je tento časový limit, aby bylo možné klienta ve skutečnosti označit jako odpojený, protože to je implementováno.</span><span class="sxs-lookup"><span data-stu-id="2b287-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="2b287-130">Doporučená hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="2b287-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="2b287-131">15 sekund</span><span class="sxs-lookup"><span data-stu-id="2b287-131">15 seconds</span></span> | <span data-ttu-id="2b287-132">Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře.</span><span class="sxs-lookup"><span data-stu-id="2b287-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="2b287-133">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="2b287-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="2b287-134">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="2b287-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="2b287-135">15 sekund</span><span class="sxs-lookup"><span data-stu-id="2b287-135">15 seconds</span></span> | <span data-ttu-id="2b287-136">Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="2b287-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="2b287-137">Při změně `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení změňte nastavení v klientovi.</span><span class="sxs-lookup"><span data-stu-id="2b287-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="2b287-138">Doporučená `ServerTimeout` / `serverTimeoutInMilliseconds` hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="2b287-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="2b287-139">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="2b287-139">All installed protocols</span></span> | <span data-ttu-id="2b287-140">Protokoly podporované tímto rozbočovačem</span><span class="sxs-lookup"><span data-stu-id="2b287-140">Protocols supported by this hub.</span></span> <span data-ttu-id="2b287-141">Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly.</span><span class="sxs-lookup"><span data-stu-id="2b287-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="2b287-142">Pokud se `true` do klientů vrátí podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="2b287-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="2b287-143">Výchozí hodnota je `false` , protože tyto zprávy o výjimce můžou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="2b287-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="2b287-144">Maximální počet položek, které lze uložit do vyrovnávací paměti pro datové proudy pro odeslání klienta.</span><span class="sxs-lookup"><span data-stu-id="2b287-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="2b287-145">Pokud je dosaženo tohoto limitu, zpracování volání je blokováno, dokud server nezpracovává položky datového proudu.</span><span class="sxs-lookup"><span data-stu-id="2b287-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="2b287-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="2b287-146">32 KB</span></span> | <span data-ttu-id="2b287-147">Maximální velikost jedné příchozí zprávy centra</span><span class="sxs-lookup"><span data-stu-id="2b287-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="2b287-148">Možnosti lze nakonfigurovat pro všechna centra tím, že poskytnete možnosti delegáta pro `AddSignalR` volání v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2b287-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="2b287-149">Možnosti pro jeden rozbočovač přepíší globální možnosti poskytované v `AddSignalR` a můžou být nakonfigurované pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="2b287-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="2b287-150">Rozšířené možnosti konfigurace protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="2b287-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="2b287-151">Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="2b287-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="2b287-152">Tyto možnosti jsou nakonfigurovány předáním delegáta [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="2b287-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="2b287-153">Následující tabulka popisuje možnosti konfigurace SignalR rozšířených možností protokolu HTTP v ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="2b287-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="2b287-154">Možnost</span><span class="sxs-lookup"><span data-stu-id="2b287-154">Option</span></span> | <span data-ttu-id="2b287-155">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-155">Default Value</span></span> | <span data-ttu-id="2b287-156">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="2b287-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="2b287-157">32 KB</span></span> | <span data-ttu-id="2b287-158">Maximální počet bajtů přijatých od klienta, které jsou ukládány do vyrovnávací paměti serveru před použitím protitlaku.</span><span class="sxs-lookup"><span data-stu-id="2b287-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="2b287-159">Zvýšením této hodnoty umožníte, aby server přijímal větší zprávy rychleji bez použití zatížení, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="2b287-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="2b287-160">Data se automaticky shromažďují z `Authorize` atributů použitých pro třídu centra.</span><span class="sxs-lookup"><span data-stu-id="2b287-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="2b287-161">Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru</span><span class="sxs-lookup"><span data-stu-id="2b287-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="2b287-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="2b287-162">32 KB</span></span> | <span data-ttu-id="2b287-163">Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru před pozorováním protitlaku.</span><span class="sxs-lookup"><span data-stu-id="2b287-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="2b287-164">Zvýšením této hodnoty umožníte serveru rychleji ukládat větší zprávy bez nutnosti očekávat zatížení, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="2b287-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="2b287-165">Všechny přenosy jsou povolené.</span><span class="sxs-lookup"><span data-stu-id="2b287-165">All Transports are enabled.</span></span> | <span data-ttu-id="2b287-166">Bitové příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenos, který může klient použít pro připojení.</span><span class="sxs-lookup"><span data-stu-id="2b287-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="2b287-167">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="2b287-167">See below.</span></span> | <span data-ttu-id="2b287-168">Další možnosti specifické pro přenos dlouhého cyklického dotazování.</span><span class="sxs-lookup"><span data-stu-id="2b287-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="2b287-169">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="2b287-169">See below.</span></span> | <span data-ttu-id="2b287-170">Další možnosti specifické pro přenos pomocí protokolu WebSockets</span><span class="sxs-lookup"><span data-stu-id="2b287-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="2b287-171">0</span><span class="sxs-lookup"><span data-stu-id="2b287-171">0</span></span> | <span data-ttu-id="2b287-172">Zadejte minimální verzi protokolu Negotiate.</span><span class="sxs-lookup"><span data-stu-id="2b287-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="2b287-173">Slouží k omezení klientů na novější verze.</span><span class="sxs-lookup"><span data-stu-id="2b287-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="2b287-174">Přenos dlouhého cyklického dotazování má další možnosti, které je možné konfigurovat pomocí `LongPolling` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="2b287-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="2b287-175">Možnost</span><span class="sxs-lookup"><span data-stu-id="2b287-175">Option</span></span> | <span data-ttu-id="2b287-176">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-176">Default Value</span></span> | <span data-ttu-id="2b287-177">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="2b287-178">90 sekund</span><span class="sxs-lookup"><span data-stu-id="2b287-178">90 seconds</span></span> | <span data-ttu-id="2b287-179">Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jedné žádosti o cyklické dotazování.</span><span class="sxs-lookup"><span data-stu-id="2b287-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="2b287-180">Snížení této hodnoty způsobí, že klient bude vydávat nové požadavky na dotaz častěji.</span><span class="sxs-lookup"><span data-stu-id="2b287-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="2b287-181">Přenos protokolu WebSocket má další možnosti, které je možné konfigurovat pomocí `WebSockets` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="2b287-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="2b287-182">Možnost</span><span class="sxs-lookup"><span data-stu-id="2b287-182">Option</span></span> | <span data-ttu-id="2b287-183">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-183">Default Value</span></span> | <span data-ttu-id="2b287-184">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="2b287-185">5 sekund</span><span class="sxs-lookup"><span data-stu-id="2b287-185">5 seconds</span></span> | <span data-ttu-id="2b287-186">Pokud se po ukončení serveru aplikace v tomto časovém intervalu nepovede zavřít, připojení se ukončí.</span><span class="sxs-lookup"><span data-stu-id="2b287-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="2b287-187">Delegát, který lze použít k nastavení `Sec-WebSocket-Protocol` záhlaví na vlastní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="2b287-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="2b287-188">Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že se vrátí požadovaná hodnota.</span><span class="sxs-lookup"><span data-stu-id="2b287-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="2b287-189">Konfigurace možností klienta</span><span class="sxs-lookup"><span data-stu-id="2b287-189">Configure client options</span></span>

<span data-ttu-id="2b287-190">Možnosti klienta lze nakonfigurovat pro `HubConnectionBuilder` typ (k dispozici v klientech rozhraní .NET a v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="2b287-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="2b287-191">Je také k dispozici v klientovi Java, ale `HttpHubConnectionBuilder` podtřídou je to, co obsahuje možnosti konfigurace tvůrce a také na `HubConnection` sebe sama.</span><span class="sxs-lookup"><span data-stu-id="2b287-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="2b287-192">Konfigurovat protokolování</span><span class="sxs-lookup"><span data-stu-id="2b287-192">Configure logging</span></span>

<span data-ttu-id="2b287-193">Protokolování je konfigurováno v klientovi .NET pomocí `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="2b287-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="2b287-194">Zprostředkovatelé protokolování a filtry je možné zaregistrovat stejným způsobem jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="2b287-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="2b287-195">Další informace najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="2b287-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="2b287-196">Aby bylo možné registrovat poskytovatele protokolování, je nutné nainstalovat potřebné balíčky.</span><span class="sxs-lookup"><span data-stu-id="2b287-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="2b287-197">Úplný seznam najdete v části [předdefinovaná zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="2b287-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="2b287-198">Chcete-li například povolit protokolování konzoly, nainstalujte `Microsoft.Extensions.Logging.Console` balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="2b287-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="2b287-199">Zavolejte `AddConsole` metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="2b287-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="2b287-200">V klientovi jazyka JavaScript existuje podobná `configureLogging` metoda.</span><span class="sxs-lookup"><span data-stu-id="2b287-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="2b287-201">Zadejte `LogLevel` hodnotu, která určuje minimální úroveň zpráv protokolu, které mají být vyprodukovány.</span><span class="sxs-lookup"><span data-stu-id="2b287-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="2b287-202">Protokoly se zapisují do okna konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="2b287-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="2b287-203">Místo `LogLevel` hodnoty můžete zadat také `string` hodnotu představující název úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="2b287-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="2b287-204">To je užitečné při konfiguraci SignalR protokolování v prostředích, kde nemáte přístup k `LogLevel` konstantám.</span><span class="sxs-lookup"><span data-stu-id="2b287-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="2b287-205">V následující tabulce jsou uvedeny dostupné úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="2b287-205">The following table lists the available log levels.</span></span> <span data-ttu-id="2b287-206">Hodnota, kterou zadáte, `configureLogging` nastaví **minimální** úroveň protokolu, která se bude protokolovat.</span><span class="sxs-lookup"><span data-stu-id="2b287-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="2b287-207">Zprávy zaznamenané na této úrovni **nebo úrovně uvedené za ní v tabulce**budou protokolovány.</span><span class="sxs-lookup"><span data-stu-id="2b287-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="2b287-208">Řetězec</span><span class="sxs-lookup"><span data-stu-id="2b287-208">String</span></span>                      | <span data-ttu-id="2b287-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="2b287-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="2b287-210">`info` **nebo** `information`</span><span class="sxs-lookup"><span data-stu-id="2b287-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="2b287-211">`warn` **nebo** `warning`</span><span class="sxs-lookup"><span data-stu-id="2b287-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="2b287-212">Chcete-li protokolování zcela zakázat, zadejte `signalR.LogLevel.None` v `configureLogging` metodě.</span><span class="sxs-lookup"><span data-stu-id="2b287-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="2b287-213">Další informace o protokolování naleznete v dokumentaci k [ SignalR diagnostice](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="2b287-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="2b287-214">SignalRKlient Java používá k protokolování knihovnu [SLF4J](https://www.slf4j.org/) .</span><span class="sxs-lookup"><span data-stu-id="2b287-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="2b287-215">Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí.</span><span class="sxs-lookup"><span data-stu-id="2b287-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="2b287-216">Následující fragment kódu ukazuje, jak používat `java.util.logging` s SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="2b287-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="2b287-217">Pokud ve svých závislostech nenakonfigurujete protokolování, SLF4J načte výchozí protokolovací nástroj No-operation s následující zprávou upozornění:</span><span class="sxs-lookup"><span data-stu-id="2b287-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="2b287-218">Tuto možnost lze bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="2b287-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="2b287-219">Konfigurace povolených přenosů</span><span class="sxs-lookup"><span data-stu-id="2b287-219">Configure allowed transports</span></span>

<span data-ttu-id="2b287-220">Přenosy používané nástrojem SignalR lze konfigurovat ve `WithUrl` volání ( `withUrl` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="2b287-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="2b287-221">Bitové nebo hodnoty `HttpTransportType` lze použít k omezení klienta na používání pouze zadaných přenosů.</span><span class="sxs-lookup"><span data-stu-id="2b287-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="2b287-222">Ve výchozím nastavení jsou povolené všechny přenosy.</span><span class="sxs-lookup"><span data-stu-id="2b287-222">All transports are enabled by default.</span></span>

<span data-ttu-id="2b287-223">Pokud třeba chcete zakázat přenos událostí odeslaných serverem, ale povolit WebSockets a dlouhá připojení s dotazem:</span><span class="sxs-lookup"><span data-stu-id="2b287-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="2b287-224">V klientu jazyka JavaScript jsou přenosy konfigurovány nastavením `transport` pole v objektu Options, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="2b287-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="2b287-225">V této verzi je jediným dostupným přenosem klientský WebSocket v jazyce Java.</span><span class="sxs-lookup"><span data-stu-id="2b287-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="2b287-226">V klientovi Java se přenos vybere s `withTransport` metodou na `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="2b287-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="2b287-227">Klient Java standardně používá přenos pomocí protokolu WebSockets.</span><span class="sxs-lookup"><span data-stu-id="2b287-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="2b287-228">SignalRKlient Java ještě nepodporuje přenosovou zálohu.</span><span class="sxs-lookup"><span data-stu-id="2b287-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="2b287-229">Konfigurace ověřování nosiče</span><span class="sxs-lookup"><span data-stu-id="2b287-229">Configure bearer authentication</span></span>

<span data-ttu-id="2b287-230">K poskytnutí ověřovacích dat spolu s SignalR požadavky použijte `AccessTokenProvider` možnost ( `accessTokenFactory` v jazyce JavaScript) k určení funkce, která vrací požadovaný přístupový token.</span><span class="sxs-lookup"><span data-stu-id="2b287-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="2b287-231">V klientovi .NET se tento přístupový token předává jako token ověřování HTTP "Bearer" (s použitím `Authorization` hlavičky s typem `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="2b287-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="2b287-232">V klientu jazyka JavaScript se přístupový token používá jako nosný token, **s výjimkou** případů, kdy rozhraní API prohlížeče omezuje možnost použít hlavičky (konkrétně v požadavcích na události odeslané serverem a objekty WebSockets).</span><span class="sxs-lookup"><span data-stu-id="2b287-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="2b287-233">V těchto případech je přístupový token k dispozici jako hodnota řetězce dotazu `access_token` .</span><span class="sxs-lookup"><span data-stu-id="2b287-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="2b287-234">V klientu .NET `AccessTokenProvider` lze možnost zadat pomocí delegáta možnosti v `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="2b287-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="2b287-235">V klientovi JavaScriptu je přístupový token nakonfigurovaný nastavením `accessTokenFactory` pole v objektu Options v `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="2b287-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="2b287-236">V SignalR klientovi Java můžete nakonfigurovat nosný token, který se bude používat pro ověřování, a to poskytnutím továrny přístupového tokenu [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="2b287-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="2b287-237">Pomocí [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) můžete zadat [RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="2b287-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="2b287-238">Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.</span><span class="sxs-lookup"><span data-stu-id="2b287-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="2b287-239">Konfigurace možností timeout a Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="2b287-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="2b287-240">Další možnosti konfigurace časového limitu a chování funkce Keep-Alive jsou k dispozici na `HubConnection` samotném objektu:</span><span class="sxs-lookup"><span data-stu-id="2b287-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="2b287-241">.NET</span><span class="sxs-lookup"><span data-stu-id="2b287-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="2b287-242">Možnost</span><span class="sxs-lookup"><span data-stu-id="2b287-242">Option</span></span> | <span data-ttu-id="2b287-243">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-243">Default value</span></span> | <span data-ttu-id="2b287-244">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="2b287-245">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="2b287-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="2b287-246">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="2b287-246">Timeout for server activity.</span></span> <span data-ttu-id="2b287-247">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí `Closed` událost ( `onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="2b287-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="2b287-248">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="2b287-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="2b287-249">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="2b287-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="2b287-250">15 sekund</span><span class="sxs-lookup"><span data-stu-id="2b287-250">15 seconds</span></span> | <span data-ttu-id="2b287-251">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="2b287-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="2b287-252">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a spustí `Closed` událost ( `onclose` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="2b287-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="2b287-253">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="2b287-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="2b287-254">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="2b287-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="2b287-255">15 sekund</span><span class="sxs-lookup"><span data-stu-id="2b287-255">15 seconds</span></span> | <span data-ttu-id="2b287-256">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="2b287-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="2b287-257">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="2b287-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="2b287-258">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="2b287-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="2b287-259">V klientovi .NET jsou hodnoty časového limitu zadány jako `TimeSpan` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="2b287-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="2b287-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="2b287-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="2b287-261">Možnost</span><span class="sxs-lookup"><span data-stu-id="2b287-261">Option</span></span> | <span data-ttu-id="2b287-262">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-262">Default value</span></span> | <span data-ttu-id="2b287-263">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="2b287-264">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="2b287-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="2b287-265">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="2b287-265">Timeout for server activity.</span></span> <span data-ttu-id="2b287-266">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onclose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="2b287-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="2b287-267">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="2b287-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="2b287-268">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="2b287-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="2b287-269">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="2b287-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="2b287-270">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="2b287-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="2b287-271">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="2b287-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="2b287-272">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="2b287-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="2b287-273">Java</span><span class="sxs-lookup"><span data-stu-id="2b287-273">Java</span></span>](#tab/java)

| <span data-ttu-id="2b287-274">Možnost</span><span class="sxs-lookup"><span data-stu-id="2b287-274">Option</span></span> | <span data-ttu-id="2b287-275">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-275">Default value</span></span> | <span data-ttu-id="2b287-276">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="2b287-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="2b287-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="2b287-278">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="2b287-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="2b287-279">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="2b287-279">Timeout for server activity.</span></span> <span data-ttu-id="2b287-280">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onClose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="2b287-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="2b287-281">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="2b287-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="2b287-282">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="2b287-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="2b287-283">15 sekund</span><span class="sxs-lookup"><span data-stu-id="2b287-283">15 seconds</span></span> | <span data-ttu-id="2b287-284">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="2b287-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="2b287-285">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje `onClose` událost.</span><span class="sxs-lookup"><span data-stu-id="2b287-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="2b287-286">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="2b287-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="2b287-287">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="2b287-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="2b287-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="2b287-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="2b287-289">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="2b287-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="2b287-290">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="2b287-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="2b287-291">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="2b287-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="2b287-292">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="2b287-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="2b287-293">Konfigurace dalších možností</span><span class="sxs-lookup"><span data-stu-id="2b287-293">Configure additional options</span></span>

<span data-ttu-id="2b287-294">Další možnosti lze konfigurovat v `WithUrl` `withUrl` metodě (v jazyce JavaScript) na `HubConnectionBuilder` různých konfiguračních rozhraních API v `HttpHubConnectionBuilder` klientovi Java:</span><span class="sxs-lookup"><span data-stu-id="2b287-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="2b287-295">.NET</span><span class="sxs-lookup"><span data-stu-id="2b287-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="2b287-296">Možnost .NET</span><span class="sxs-lookup"><span data-stu-id="2b287-296">.NET Option</span></span> |  <span data-ttu-id="2b287-297">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-297">Default value</span></span> | <span data-ttu-id="2b287-298">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="2b287-299">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="2b287-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="2b287-300">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="2b287-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="2b287-301">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="2b287-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="2b287-302">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="2b287-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="2b287-303">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="2b287-303">Empty</span></span> | <span data-ttu-id="2b287-304">Kolekce certifikátů TLS pro odeslání na požadavky na ověření.</span><span class="sxs-lookup"><span data-stu-id="2b287-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="2b287-305">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="2b287-305">Empty</span></span> | <span data-ttu-id="2b287-306">Kolekce souborů cookie protokolu HTTP, které se mají odeslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="2b287-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="2b287-307">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="2b287-307">Empty</span></span> | <span data-ttu-id="2b287-308">Přihlašovací údaje, které se mají poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="2b287-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="2b287-309">5 sekund</span><span class="sxs-lookup"><span data-stu-id="2b287-309">5 seconds</span></span> | <span data-ttu-id="2b287-310">Jenom objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="2b287-310">WebSockets only.</span></span> <span data-ttu-id="2b287-311">Maximální doba, po jejímž uplynutí bude klient čekat na ukončení žádosti o uzavření.</span><span class="sxs-lookup"><span data-stu-id="2b287-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="2b287-312">Pokud server nepotvrdí zavření v této době, klient se odpojí.</span><span class="sxs-lookup"><span data-stu-id="2b287-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="2b287-313">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="2b287-313">Empty</span></span> | <span data-ttu-id="2b287-314">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="2b287-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="2b287-315">Delegát, který lze použít ke konfiguraci nebo nahrazení `HttpMessageHandler` používaného k odeslání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="2b287-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="2b287-316">Nepoužívá se pro připojení pomocí protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="2b287-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="2b287-317">Tento delegát musí vracet hodnotu, která není null, a obdrží výchozí hodnotu jako parametr.</span><span class="sxs-lookup"><span data-stu-id="2b287-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="2b287-318">Buď změňte nastavení u této výchozí hodnoty a vraťte je, nebo vraťte novou `HttpMessageHandler` instanci.</span><span class="sxs-lookup"><span data-stu-id="2b287-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="2b287-319">**Při nahrazování obslužné rutiny Nezapomeňte zkopírovat nastavení, která chcete zachovat, od poskytnuté obslužné rutiny, jinak konfigurované možnosti (například soubory cookie a záhlaví) nebudou použity pro novou obslužnou rutinu.**</span><span class="sxs-lookup"><span data-stu-id="2b287-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="2b287-320">Proxy server HTTP, který se má použít při odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="2b287-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="2b287-321">Nastavte tuto logickou hodnotu pro odeslání výchozích přihlašovacích údajů pro požadavky HTTP a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="2b287-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="2b287-322">To umožňuje použití ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="2b287-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="2b287-323">Delegát, který lze použít ke konfiguraci dalších možností protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="2b287-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="2b287-324">Přijímá instanci [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , která se dá použít ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="2b287-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="2b287-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="2b287-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="2b287-326">Možnost JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="2b287-326">JavaScript Option</span></span> | <span data-ttu-id="2b287-327">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-327">Default Value</span></span> | <span data-ttu-id="2b287-328">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="2b287-329">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="2b287-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `headers` | `null` | <span data-ttu-id="2b287-330">Slovník hlaviček odeslaných s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="2b287-330">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="2b287-331">Odesílání hlaviček v prohlížeči nefunguje pro objekty WebSockets ani <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> Stream.</span><span class="sxs-lookup"><span data-stu-id="2b287-331">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="2b287-332">Nastavte na `true` protokolování bajtů/znaků zpráv odesílaných a přijímaných klientem.</span><span class="sxs-lookup"><span data-stu-id="2b287-332">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="2b287-333">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="2b287-333">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="2b287-334">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="2b287-334">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="2b287-335">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="2b287-335">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="2b287-336">Určuje, jestli se budou odesílat přihlašovací údaje s požadavkem CORS.</span><span class="sxs-lookup"><span data-stu-id="2b287-336">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="2b287-337">Azure App Service používá pro rychlé relace soubory cookie a vyžaduje, aby tato možnost fungovala správně.</span><span class="sxs-lookup"><span data-stu-id="2b287-337">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="2b287-338">Další informace o CORS s SignalR najdete v tématu <xref:signalr/security#cross-origin-resource-sharing> .</span><span class="sxs-lookup"><span data-stu-id="2b287-338">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="2b287-339">Java</span><span class="sxs-lookup"><span data-stu-id="2b287-339">Java</span></span>](#tab/java)

| <span data-ttu-id="2b287-340">Možnost jazyka Java</span><span class="sxs-lookup"><span data-stu-id="2b287-340">Java Option</span></span> | <span data-ttu-id="2b287-341">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-341">Default Value</span></span> | <span data-ttu-id="2b287-342">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-342">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="2b287-343">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="2b287-343">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="2b287-344">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="2b287-344">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="2b287-345">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="2b287-345">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="2b287-346">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="2b287-346">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="2b287-347">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="2b287-347">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="2b287-348">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="2b287-348">Empty</span></span> | <span data-ttu-id="2b287-349">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="2b287-349">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="2b287-350">V klientu .NET můžete tyto možnosti upravit pomocí delegáta možností, který je k dispozici pro `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="2b287-350">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="2b287-351">V klientovi JavaScriptu lze tyto možnosti poskytnout v objektu jazyka JavaScript, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="2b287-351">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="2b287-352">V klientovi Java lze tyto možnosti nakonfigurovat s metodami `HttpHubConnectionBuilder` vrácenými z`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="2b287-352">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="2b287-353">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="2b287-353">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="2b287-354">Možnosti serializace JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="2b287-354">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="2b287-355">ASP.NET Core SignalR podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="2b287-355">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="2b287-356">Každý protokol má možnosti konfigurace serializace.</span><span class="sxs-lookup"><span data-stu-id="2b287-356">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="2b287-357">Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="2b287-357">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="2b287-358">`AddJsonProtocol`lze přidat po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2b287-358">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2b287-359">`AddJsonProtocol`Metoda přebírá delegáta, který přijímá `options` objekt.</span><span class="sxs-lookup"><span data-stu-id="2b287-359">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="2b287-360">Vlastnost [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) tohoto objektu je `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objekt, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="2b287-360">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="2b287-361">Další informace najdete v [System.Text.Jsv dokumentaci](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="2b287-361">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="2b287-362">Chcete-li například nakonfigurovat serializátor, aby neměnil velká a malá písmena názvů vlastností namísto výchozích názvů "camelCase", použijte následující kód v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2b287-362">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="2b287-363">V klientovi .NET `AddJsonProtocol` existuje stejná rozšiřující metoda v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="2b287-363">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="2b287-364">`Microsoft.Extensions.DependencyInjection`Obor názvů musí být importován, aby bylo možné přeložit metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="2b287-364">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="2b287-365">V tuto chvíli není možné konfigurovat serializaci JSON v klientovi jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2b287-365">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="2b287-366">Přepnout na Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="2b287-366">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="2b287-367">Pokud potřebujete funkce nástroje `Newtonsoft.Json` , které nejsou podporované v systému `System.Text.Json` , přečtěte si téma [Přepnutí na Newtonsoft.Jsna](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="2b287-367">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="2b287-368">Možnosti serializace MessagePack</span><span class="sxs-lookup"><span data-stu-id="2b287-368">MessagePack serialization options</span></span>

<span data-ttu-id="2b287-369">Serializaci MessagePack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="2b287-369">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="2b287-370">Další podrobnosti najdete [v SignalR tématu MessagePack](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="2b287-370">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="2b287-371">V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi v jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2b287-371">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="2b287-372">Konfigurovat možnosti serveru</span><span class="sxs-lookup"><span data-stu-id="2b287-372">Configure server options</span></span>

<span data-ttu-id="2b287-373">Následující tabulka popisuje možnosti konfigurace SignalR Center:</span><span class="sxs-lookup"><span data-stu-id="2b287-373">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="2b287-374">Možnost</span><span class="sxs-lookup"><span data-stu-id="2b287-374">Option</span></span> | <span data-ttu-id="2b287-375">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-375">Default Value</span></span> | <span data-ttu-id="2b287-376">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-376">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="2b287-377">30 sekund</span><span class="sxs-lookup"><span data-stu-id="2b287-377">30 seconds</span></span> | <span data-ttu-id="2b287-378">Server bude předpokládat, že je klient odpojený, pokud v tomto intervalu neobdržel zprávu (včetně Keep-Alive).</span><span class="sxs-lookup"><span data-stu-id="2b287-378">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="2b287-379">Může trvat delší dobu, než je tento časový limit, aby bylo možné klienta ve skutečnosti označit jako odpojený, protože to je implementováno.</span><span class="sxs-lookup"><span data-stu-id="2b287-379">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="2b287-380">Doporučená hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="2b287-380">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="2b287-381">15 sekund</span><span class="sxs-lookup"><span data-stu-id="2b287-381">15 seconds</span></span> | <span data-ttu-id="2b287-382">Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře.</span><span class="sxs-lookup"><span data-stu-id="2b287-382">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="2b287-383">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="2b287-383">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="2b287-384">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="2b287-384">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="2b287-385">15 sekund</span><span class="sxs-lookup"><span data-stu-id="2b287-385">15 seconds</span></span> | <span data-ttu-id="2b287-386">Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="2b287-386">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="2b287-387">Při změně `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení změňte nastavení v klientovi.</span><span class="sxs-lookup"><span data-stu-id="2b287-387">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="2b287-388">Doporučená `ServerTimeout` / `serverTimeoutInMilliseconds` hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="2b287-388">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="2b287-389">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="2b287-389">All installed protocols</span></span> | <span data-ttu-id="2b287-390">Protokoly podporované tímto rozbočovačem</span><span class="sxs-lookup"><span data-stu-id="2b287-390">Protocols supported by this hub.</span></span> <span data-ttu-id="2b287-391">Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly.</span><span class="sxs-lookup"><span data-stu-id="2b287-391">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="2b287-392">Pokud se `true` do klientů vrátí podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="2b287-392">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="2b287-393">Výchozí hodnota je `false` , protože tyto zprávy o výjimce můžou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="2b287-393">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="2b287-394">Maximální počet položek, které lze uložit do vyrovnávací paměti pro datové proudy pro odeslání klienta.</span><span class="sxs-lookup"><span data-stu-id="2b287-394">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="2b287-395">Pokud je dosaženo tohoto limitu, zpracování volání je blokováno, dokud server nezpracovává položky datového proudu.</span><span class="sxs-lookup"><span data-stu-id="2b287-395">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="2b287-396">32 KB</span><span class="sxs-lookup"><span data-stu-id="2b287-396">32 KB</span></span> | <span data-ttu-id="2b287-397">Maximální velikost jedné příchozí zprávy centra</span><span class="sxs-lookup"><span data-stu-id="2b287-397">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="2b287-398">Možnosti lze nakonfigurovat pro všechna centra tím, že poskytnete možnosti delegáta pro `AddSignalR` volání v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2b287-398">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="2b287-399">Možnosti pro jeden rozbočovač přepíší globální možnosti poskytované v `AddSignalR` a můžou být nakonfigurované pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="2b287-399">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="2b287-400">Rozšířené možnosti konfigurace protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="2b287-400">Advanced HTTP configuration options</span></span>

<span data-ttu-id="2b287-401">Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="2b287-401">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="2b287-402">Tyto možnosti jsou nakonfigurovány předáním delegáta [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="2b287-402">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="2b287-403">Následující tabulka popisuje možnosti konfigurace SignalR rozšířených možností protokolu HTTP v ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="2b287-403">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="2b287-404">Možnost</span><span class="sxs-lookup"><span data-stu-id="2b287-404">Option</span></span> | <span data-ttu-id="2b287-405">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-405">Default Value</span></span> | <span data-ttu-id="2b287-406">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-406">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="2b287-407">32 KB</span><span class="sxs-lookup"><span data-stu-id="2b287-407">32 KB</span></span> | <span data-ttu-id="2b287-408">Maximální počet bajtů přijatých od klienta, které jsou ukládány do vyrovnávací paměti serveru před použitím protitlaku.</span><span class="sxs-lookup"><span data-stu-id="2b287-408">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="2b287-409">Zvýšením této hodnoty umožníte, aby server přijímal větší zprávy rychleji bez použití zatížení, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="2b287-409">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="2b287-410">Data se automaticky shromažďují z `Authorize` atributů použitých pro třídu centra.</span><span class="sxs-lookup"><span data-stu-id="2b287-410">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="2b287-411">Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru</span><span class="sxs-lookup"><span data-stu-id="2b287-411">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="2b287-412">32 KB</span><span class="sxs-lookup"><span data-stu-id="2b287-412">32 KB</span></span> | <span data-ttu-id="2b287-413">Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru před pozorováním protitlaku.</span><span class="sxs-lookup"><span data-stu-id="2b287-413">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="2b287-414">Zvýšením této hodnoty umožníte serveru rychleji ukládat větší zprávy bez nutnosti očekávat zatížení, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="2b287-414">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="2b287-415">Všechny přenosy jsou povolené.</span><span class="sxs-lookup"><span data-stu-id="2b287-415">All Transports are enabled.</span></span> | <span data-ttu-id="2b287-416">Bitové příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenos, který může klient použít pro připojení.</span><span class="sxs-lookup"><span data-stu-id="2b287-416">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="2b287-417">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="2b287-417">See below.</span></span> | <span data-ttu-id="2b287-418">Další možnosti specifické pro přenos dlouhého cyklického dotazování.</span><span class="sxs-lookup"><span data-stu-id="2b287-418">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="2b287-419">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="2b287-419">See below.</span></span> | <span data-ttu-id="2b287-420">Další možnosti specifické pro přenos pomocí protokolu WebSockets</span><span class="sxs-lookup"><span data-stu-id="2b287-420">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="2b287-421">0</span><span class="sxs-lookup"><span data-stu-id="2b287-421">0</span></span> | <span data-ttu-id="2b287-422">Zadejte minimální verzi protokolu Negotiate.</span><span class="sxs-lookup"><span data-stu-id="2b287-422">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="2b287-423">Slouží k omezení klientů na novější verze.</span><span class="sxs-lookup"><span data-stu-id="2b287-423">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="2b287-424">Přenos dlouhého cyklického dotazování má další možnosti, které je možné konfigurovat pomocí `LongPolling` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="2b287-424">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="2b287-425">Možnost</span><span class="sxs-lookup"><span data-stu-id="2b287-425">Option</span></span> | <span data-ttu-id="2b287-426">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-426">Default Value</span></span> | <span data-ttu-id="2b287-427">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-427">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="2b287-428">90 sekund</span><span class="sxs-lookup"><span data-stu-id="2b287-428">90 seconds</span></span> | <span data-ttu-id="2b287-429">Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jedné žádosti o cyklické dotazování.</span><span class="sxs-lookup"><span data-stu-id="2b287-429">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="2b287-430">Snížení této hodnoty způsobí, že klient bude vydávat nové požadavky na dotaz častěji.</span><span class="sxs-lookup"><span data-stu-id="2b287-430">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="2b287-431">Přenos protokolu WebSocket má další možnosti, které je možné konfigurovat pomocí `WebSockets` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="2b287-431">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="2b287-432">Možnost</span><span class="sxs-lookup"><span data-stu-id="2b287-432">Option</span></span> | <span data-ttu-id="2b287-433">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-433">Default Value</span></span> | <span data-ttu-id="2b287-434">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-434">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="2b287-435">5 sekund</span><span class="sxs-lookup"><span data-stu-id="2b287-435">5 seconds</span></span> | <span data-ttu-id="2b287-436">Pokud se po ukončení serveru aplikace v tomto časovém intervalu nepovede zavřít, připojení se ukončí.</span><span class="sxs-lookup"><span data-stu-id="2b287-436">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="2b287-437">Delegát, který lze použít k nastavení `Sec-WebSocket-Protocol` záhlaví na vlastní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="2b287-437">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="2b287-438">Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že se vrátí požadovaná hodnota.</span><span class="sxs-lookup"><span data-stu-id="2b287-438">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="2b287-439">Konfigurace možností klienta</span><span class="sxs-lookup"><span data-stu-id="2b287-439">Configure client options</span></span>

<span data-ttu-id="2b287-440">Možnosti klienta lze nakonfigurovat pro `HubConnectionBuilder` typ (k dispozici v klientech rozhraní .NET a v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="2b287-440">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="2b287-441">Je také k dispozici v klientovi Java, ale `HttpHubConnectionBuilder` podtřídou je to, co obsahuje možnosti konfigurace tvůrce a také na `HubConnection` sebe sama.</span><span class="sxs-lookup"><span data-stu-id="2b287-441">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="2b287-442">Konfigurovat protokolování</span><span class="sxs-lookup"><span data-stu-id="2b287-442">Configure logging</span></span>

<span data-ttu-id="2b287-443">Protokolování je konfigurováno v klientovi .NET pomocí `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="2b287-443">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="2b287-444">Zprostředkovatelé protokolování a filtry je možné zaregistrovat stejným způsobem jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="2b287-444">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="2b287-445">Další informace najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="2b287-445">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="2b287-446">Aby bylo možné registrovat poskytovatele protokolování, je nutné nainstalovat potřebné balíčky.</span><span class="sxs-lookup"><span data-stu-id="2b287-446">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="2b287-447">Úplný seznam najdete v části [předdefinovaná zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="2b287-447">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="2b287-448">Chcete-li například povolit protokolování konzoly, nainstalujte `Microsoft.Extensions.Logging.Console` balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="2b287-448">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="2b287-449">Zavolejte `AddConsole` metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="2b287-449">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="2b287-450">V klientovi jazyka JavaScript existuje podobná `configureLogging` metoda.</span><span class="sxs-lookup"><span data-stu-id="2b287-450">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="2b287-451">Zadejte `LogLevel` hodnotu, která určuje minimální úroveň zpráv protokolu, které mají být vyprodukovány.</span><span class="sxs-lookup"><span data-stu-id="2b287-451">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="2b287-452">Protokoly se zapisují do okna konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="2b287-452">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="2b287-453">Místo `LogLevel` hodnoty můžete zadat také `string` hodnotu představující název úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="2b287-453">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="2b287-454">To je užitečné při konfiguraci SignalR protokolování v prostředích, kde nemáte přístup k `LogLevel` konstantám.</span><span class="sxs-lookup"><span data-stu-id="2b287-454">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="2b287-455">V následující tabulce jsou uvedeny dostupné úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="2b287-455">The following table lists the available log levels.</span></span> <span data-ttu-id="2b287-456">Hodnota, kterou zadáte, `configureLogging` nastaví **minimální** úroveň protokolu, která se bude protokolovat.</span><span class="sxs-lookup"><span data-stu-id="2b287-456">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="2b287-457">Zprávy zaznamenané na této úrovni **nebo úrovně uvedené za ní v tabulce**budou protokolovány.</span><span class="sxs-lookup"><span data-stu-id="2b287-457">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="2b287-458">Řetězec</span><span class="sxs-lookup"><span data-stu-id="2b287-458">String</span></span>                      | <span data-ttu-id="2b287-459">LogLevel</span><span class="sxs-lookup"><span data-stu-id="2b287-459">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="2b287-460">`info` **nebo** `information`</span><span class="sxs-lookup"><span data-stu-id="2b287-460">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="2b287-461">`warn` **nebo** `warning`</span><span class="sxs-lookup"><span data-stu-id="2b287-461">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="2b287-462">Chcete-li protokolování zcela zakázat, zadejte `signalR.LogLevel.None` v `configureLogging` metodě.</span><span class="sxs-lookup"><span data-stu-id="2b287-462">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="2b287-463">Další informace o protokolování naleznete v dokumentaci k [ SignalR diagnostice](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="2b287-463">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="2b287-464">SignalRKlient Java používá k protokolování knihovnu [SLF4J](https://www.slf4j.org/) .</span><span class="sxs-lookup"><span data-stu-id="2b287-464">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="2b287-465">Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí.</span><span class="sxs-lookup"><span data-stu-id="2b287-465">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="2b287-466">Následující fragment kódu ukazuje, jak používat `java.util.logging` s SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="2b287-466">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="2b287-467">Pokud ve svých závislostech nenakonfigurujete protokolování, SLF4J načte výchozí protokolovací nástroj No-operation s následující zprávou upozornění:</span><span class="sxs-lookup"><span data-stu-id="2b287-467">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="2b287-468">Tuto možnost lze bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="2b287-468">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="2b287-469">Konfigurace povolených přenosů</span><span class="sxs-lookup"><span data-stu-id="2b287-469">Configure allowed transports</span></span>

<span data-ttu-id="2b287-470">Přenosy používané nástrojem SignalR lze konfigurovat ve `WithUrl` volání ( `withUrl` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="2b287-470">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="2b287-471">Bitové nebo hodnoty `HttpTransportType` lze použít k omezení klienta na používání pouze zadaných přenosů.</span><span class="sxs-lookup"><span data-stu-id="2b287-471">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="2b287-472">Ve výchozím nastavení jsou povolené všechny přenosy.</span><span class="sxs-lookup"><span data-stu-id="2b287-472">All transports are enabled by default.</span></span>

<span data-ttu-id="2b287-473">Pokud třeba chcete zakázat přenos událostí odeslaných serverem, ale povolit WebSockets a dlouhá připojení s dotazem:</span><span class="sxs-lookup"><span data-stu-id="2b287-473">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="2b287-474">V klientu jazyka JavaScript jsou přenosy konfigurovány nastavením `transport` pole v objektu Options, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="2b287-474">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="2b287-475">V této verzi je jediným dostupným přenosem klientský WebSocket v jazyce Java.</span><span class="sxs-lookup"><span data-stu-id="2b287-475">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="2b287-476">V klientovi Java se přenos vybere s `withTransport` metodou na `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="2b287-476">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="2b287-477">Klient Java standardně používá přenos pomocí protokolu WebSockets.</span><span class="sxs-lookup"><span data-stu-id="2b287-477">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="2b287-478">SignalRKlient Java ještě nepodporuje přenosovou zálohu.</span><span class="sxs-lookup"><span data-stu-id="2b287-478">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="2b287-479">Konfigurace ověřování nosiče</span><span class="sxs-lookup"><span data-stu-id="2b287-479">Configure bearer authentication</span></span>

<span data-ttu-id="2b287-480">K poskytnutí ověřovacích dat spolu s SignalR požadavky použijte `AccessTokenProvider` možnost ( `accessTokenFactory` v jazyce JavaScript) k určení funkce, která vrací požadovaný přístupový token.</span><span class="sxs-lookup"><span data-stu-id="2b287-480">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="2b287-481">V klientovi .NET se tento přístupový token předává jako token ověřování HTTP "Bearer" (s použitím `Authorization` hlavičky s typem `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="2b287-481">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="2b287-482">V klientu jazyka JavaScript se přístupový token používá jako nosný token, **s výjimkou** případů, kdy rozhraní API prohlížeče omezuje možnost použít hlavičky (konkrétně v požadavcích na události odeslané serverem a objekty WebSockets).</span><span class="sxs-lookup"><span data-stu-id="2b287-482">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="2b287-483">V těchto případech je přístupový token k dispozici jako hodnota řetězce dotazu `access_token` .</span><span class="sxs-lookup"><span data-stu-id="2b287-483">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="2b287-484">V klientu .NET `AccessTokenProvider` lze možnost zadat pomocí delegáta možnosti v `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="2b287-484">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="2b287-485">V klientovi JavaScriptu je přístupový token nakonfigurovaný nastavením `accessTokenFactory` pole v objektu Options v `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="2b287-485">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="2b287-486">V SignalR klientovi Java můžete nakonfigurovat nosný token, který se bude používat pro ověřování, a to poskytnutím továrny přístupového tokenu [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="2b287-486">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="2b287-487">Pomocí [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) můžete zadat [RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="2b287-487">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="2b287-488">Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.</span><span class="sxs-lookup"><span data-stu-id="2b287-488">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="2b287-489">Konfigurace možností timeout a Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="2b287-489">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="2b287-490">Další možnosti konfigurace časového limitu a chování funkce Keep-Alive jsou k dispozici na `HubConnection` samotném objektu:</span><span class="sxs-lookup"><span data-stu-id="2b287-490">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="2b287-491">.NET</span><span class="sxs-lookup"><span data-stu-id="2b287-491">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="2b287-492">Možnost</span><span class="sxs-lookup"><span data-stu-id="2b287-492">Option</span></span> | <span data-ttu-id="2b287-493">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-493">Default value</span></span> | <span data-ttu-id="2b287-494">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-494">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="2b287-495">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="2b287-495">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="2b287-496">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="2b287-496">Timeout for server activity.</span></span> <span data-ttu-id="2b287-497">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí `Closed` událost ( `onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="2b287-497">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="2b287-498">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="2b287-498">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="2b287-499">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="2b287-499">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="2b287-500">15 sekund</span><span class="sxs-lookup"><span data-stu-id="2b287-500">15 seconds</span></span> | <span data-ttu-id="2b287-501">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="2b287-501">Timeout for initial server handshake.</span></span> <span data-ttu-id="2b287-502">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a spustí `Closed` událost ( `onclose` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="2b287-502">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="2b287-503">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="2b287-503">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="2b287-504">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="2b287-504">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="2b287-505">15 sekund</span><span class="sxs-lookup"><span data-stu-id="2b287-505">15 seconds</span></span> | <span data-ttu-id="2b287-506">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="2b287-506">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="2b287-507">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="2b287-507">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="2b287-508">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="2b287-508">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="2b287-509">V klientovi .NET jsou hodnoty časového limitu zadány jako `TimeSpan` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="2b287-509">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="2b287-510">JavaScript</span><span class="sxs-lookup"><span data-stu-id="2b287-510">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="2b287-511">Možnost</span><span class="sxs-lookup"><span data-stu-id="2b287-511">Option</span></span> | <span data-ttu-id="2b287-512">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-512">Default value</span></span> | <span data-ttu-id="2b287-513">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-513">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="2b287-514">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="2b287-514">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="2b287-515">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="2b287-515">Timeout for server activity.</span></span> <span data-ttu-id="2b287-516">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onclose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="2b287-516">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="2b287-517">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="2b287-517">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="2b287-518">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="2b287-518">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="2b287-519">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="2b287-519">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="2b287-520">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="2b287-520">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="2b287-521">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="2b287-521">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="2b287-522">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="2b287-522">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="2b287-523">Java</span><span class="sxs-lookup"><span data-stu-id="2b287-523">Java</span></span>](#tab/java)

| <span data-ttu-id="2b287-524">Možnost</span><span class="sxs-lookup"><span data-stu-id="2b287-524">Option</span></span> | <span data-ttu-id="2b287-525">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-525">Default value</span></span> | <span data-ttu-id="2b287-526">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-526">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="2b287-527">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="2b287-527">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="2b287-528">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="2b287-528">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="2b287-529">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="2b287-529">Timeout for server activity.</span></span> <span data-ttu-id="2b287-530">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onClose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="2b287-530">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="2b287-531">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="2b287-531">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="2b287-532">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="2b287-532">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="2b287-533">15 sekund</span><span class="sxs-lookup"><span data-stu-id="2b287-533">15 seconds</span></span> | <span data-ttu-id="2b287-534">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="2b287-534">Timeout for initial server handshake.</span></span> <span data-ttu-id="2b287-535">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje `onClose` událost.</span><span class="sxs-lookup"><span data-stu-id="2b287-535">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="2b287-536">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="2b287-536">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="2b287-537">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="2b287-537">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="2b287-538">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="2b287-538">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="2b287-539">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="2b287-539">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="2b287-540">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="2b287-540">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="2b287-541">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="2b287-541">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="2b287-542">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="2b287-542">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="2b287-543">Konfigurace dalších možností</span><span class="sxs-lookup"><span data-stu-id="2b287-543">Configure additional options</span></span>

<span data-ttu-id="2b287-544">Další možnosti lze konfigurovat v `WithUrl` `withUrl` metodě (v jazyce JavaScript) na `HubConnectionBuilder` různých konfiguračních rozhraních API v `HttpHubConnectionBuilder` klientovi Java:</span><span class="sxs-lookup"><span data-stu-id="2b287-544">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="2b287-545">.NET</span><span class="sxs-lookup"><span data-stu-id="2b287-545">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="2b287-546">Možnost .NET</span><span class="sxs-lookup"><span data-stu-id="2b287-546">.NET Option</span></span> |  <span data-ttu-id="2b287-547">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-547">Default value</span></span> | <span data-ttu-id="2b287-548">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-548">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="2b287-549">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="2b287-549">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="2b287-550">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="2b287-550">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="2b287-551">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="2b287-551">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="2b287-552">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="2b287-552">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="2b287-553">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="2b287-553">Empty</span></span> | <span data-ttu-id="2b287-554">Kolekce certifikátů TLS pro odeslání na požadavky na ověření.</span><span class="sxs-lookup"><span data-stu-id="2b287-554">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="2b287-555">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="2b287-555">Empty</span></span> | <span data-ttu-id="2b287-556">Kolekce souborů cookie protokolu HTTP, které se mají odeslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="2b287-556">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="2b287-557">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="2b287-557">Empty</span></span> | <span data-ttu-id="2b287-558">Přihlašovací údaje, které se mají poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="2b287-558">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="2b287-559">5 sekund</span><span class="sxs-lookup"><span data-stu-id="2b287-559">5 seconds</span></span> | <span data-ttu-id="2b287-560">Jenom objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="2b287-560">WebSockets only.</span></span> <span data-ttu-id="2b287-561">Maximální doba, po jejímž uplynutí bude klient čekat na ukončení žádosti o uzavření.</span><span class="sxs-lookup"><span data-stu-id="2b287-561">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="2b287-562">Pokud server nepotvrdí zavření v této době, klient se odpojí.</span><span class="sxs-lookup"><span data-stu-id="2b287-562">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="2b287-563">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="2b287-563">Empty</span></span> | <span data-ttu-id="2b287-564">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="2b287-564">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="2b287-565">Delegát, který lze použít ke konfiguraci nebo nahrazení `HttpMessageHandler` používaného k odeslání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="2b287-565">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="2b287-566">Nepoužívá se pro připojení pomocí protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="2b287-566">Not used for WebSocket connections.</span></span> <span data-ttu-id="2b287-567">Tento delegát musí vracet hodnotu, která není null, a obdrží výchozí hodnotu jako parametr.</span><span class="sxs-lookup"><span data-stu-id="2b287-567">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="2b287-568">Buď změňte nastavení u této výchozí hodnoty a vraťte je, nebo vraťte novou `HttpMessageHandler` instanci.</span><span class="sxs-lookup"><span data-stu-id="2b287-568">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="2b287-569">**Při nahrazování obslužné rutiny Nezapomeňte zkopírovat nastavení, která chcete zachovat, od poskytnuté obslužné rutiny, jinak konfigurované možnosti (například soubory cookie a záhlaví) nebudou použity pro novou obslužnou rutinu.**</span><span class="sxs-lookup"><span data-stu-id="2b287-569">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="2b287-570">Proxy server HTTP, který se má použít při odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="2b287-570">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="2b287-571">Nastavte tuto logickou hodnotu pro odeslání výchozích přihlašovacích údajů pro požadavky HTTP a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="2b287-571">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="2b287-572">To umožňuje použití ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="2b287-572">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="2b287-573">Delegát, který lze použít ke konfiguraci dalších možností protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="2b287-573">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="2b287-574">Přijímá instanci [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , která se dá použít ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="2b287-574">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="2b287-575">JavaScript</span><span class="sxs-lookup"><span data-stu-id="2b287-575">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="2b287-576">Možnost JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="2b287-576">JavaScript Option</span></span> | <span data-ttu-id="2b287-577">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-577">Default Value</span></span> | <span data-ttu-id="2b287-578">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-578">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="2b287-579">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="2b287-579">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="2b287-580">Nastavte na `true` protokolování bajtů/znaků zpráv odesílaných a přijímaných klientem.</span><span class="sxs-lookup"><span data-stu-id="2b287-580">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="2b287-581">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="2b287-581">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="2b287-582">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="2b287-582">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="2b287-583">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="2b287-583">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="2b287-584">Java</span><span class="sxs-lookup"><span data-stu-id="2b287-584">Java</span></span>](#tab/java)

| <span data-ttu-id="2b287-585">Možnost jazyka Java</span><span class="sxs-lookup"><span data-stu-id="2b287-585">Java Option</span></span> | <span data-ttu-id="2b287-586">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-586">Default Value</span></span> | <span data-ttu-id="2b287-587">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-587">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="2b287-588">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="2b287-588">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="2b287-589">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="2b287-589">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="2b287-590">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="2b287-590">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="2b287-591">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="2b287-591">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="2b287-592">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="2b287-592">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="2b287-593">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="2b287-593">Empty</span></span> | <span data-ttu-id="2b287-594">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="2b287-594">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="2b287-595">V klientu .NET můžete tyto možnosti upravit pomocí delegáta možností, který je k dispozici pro `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="2b287-595">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="2b287-596">V klientovi JavaScriptu lze tyto možnosti poskytnout v objektu jazyka JavaScript, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="2b287-596">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="2b287-597">V klientovi Java lze tyto možnosti nakonfigurovat s metodami `HttpHubConnectionBuilder` vrácenými z`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="2b287-597">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="2b287-598">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="2b287-598">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="2b287-599">Možnosti serializace JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="2b287-599">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="2b287-600">ASP.NET Core SignalR podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="2b287-600">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="2b287-601">Každý protokol má možnosti konfigurace serializace.</span><span class="sxs-lookup"><span data-stu-id="2b287-601">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="2b287-602">Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="2b287-602">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="2b287-603">`AddJsonProtocol`lze přidat po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2b287-603">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2b287-604">`AddJsonProtocol`Metoda přebírá delegáta, který přijímá `options` objekt.</span><span class="sxs-lookup"><span data-stu-id="2b287-604">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="2b287-605">Vlastnost [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) tohoto objektu je `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objekt, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="2b287-605">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="2b287-606">Další informace najdete v [System.Text.Jsv dokumentaci](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="2b287-606">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="2b287-607">Chcete-li například nakonfigurovat serializátor, aby neměnil velká a malá písmena názvů vlastností namísto výchozích názvů "camelCase", použijte následující kód v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2b287-607">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="2b287-608">V klientovi .NET `AddJsonProtocol` existuje stejná rozšiřující metoda v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="2b287-608">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="2b287-609">`Microsoft.Extensions.DependencyInjection`Obor názvů musí být importován, aby bylo možné přeložit metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="2b287-609">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="2b287-610">V tuto chvíli není možné konfigurovat serializaci JSON v klientovi jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2b287-610">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="2b287-611">Přepnout na Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="2b287-611">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="2b287-612">Pokud potřebujete funkce nástroje `Newtonsoft.Json` , které nejsou podporované v systému `System.Text.Json` , přečtěte si téma [Přepnutí na Newtonsoft.Jsna](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="2b287-612">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="2b287-613">Možnosti serializace MessagePack</span><span class="sxs-lookup"><span data-stu-id="2b287-613">MessagePack serialization options</span></span>

<span data-ttu-id="2b287-614">Serializaci MessagePack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="2b287-614">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="2b287-615">Další podrobnosti najdete [v SignalR tématu MessagePack](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="2b287-615">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="2b287-616">V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi v jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2b287-616">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="2b287-617">Konfigurovat možnosti serveru</span><span class="sxs-lookup"><span data-stu-id="2b287-617">Configure server options</span></span>

<span data-ttu-id="2b287-618">Následující tabulka popisuje možnosti konfigurace SignalR Center:</span><span class="sxs-lookup"><span data-stu-id="2b287-618">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="2b287-619">Možnost</span><span class="sxs-lookup"><span data-stu-id="2b287-619">Option</span></span> | <span data-ttu-id="2b287-620">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-620">Default Value</span></span> | <span data-ttu-id="2b287-621">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-621">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="2b287-622">30 sekund</span><span class="sxs-lookup"><span data-stu-id="2b287-622">30 seconds</span></span> | <span data-ttu-id="2b287-623">Server bude předpokládat, že je klient odpojený, pokud v tomto intervalu neobdržel zprávu (včetně Keep-Alive).</span><span class="sxs-lookup"><span data-stu-id="2b287-623">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="2b287-624">Může trvat delší dobu, než je tento časový limit, aby bylo možné klienta ve skutečnosti označit jako odpojený, protože to je implementováno.</span><span class="sxs-lookup"><span data-stu-id="2b287-624">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="2b287-625">Doporučená hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="2b287-625">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="2b287-626">15 sekund</span><span class="sxs-lookup"><span data-stu-id="2b287-626">15 seconds</span></span> | <span data-ttu-id="2b287-627">Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře.</span><span class="sxs-lookup"><span data-stu-id="2b287-627">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="2b287-628">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="2b287-628">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="2b287-629">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="2b287-629">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="2b287-630">15 sekund</span><span class="sxs-lookup"><span data-stu-id="2b287-630">15 seconds</span></span> | <span data-ttu-id="2b287-631">Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="2b287-631">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="2b287-632">Při změně `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení změňte nastavení v klientovi.</span><span class="sxs-lookup"><span data-stu-id="2b287-632">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="2b287-633">Doporučená `ServerTimeout` / `serverTimeoutInMilliseconds` hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="2b287-633">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="2b287-634">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="2b287-634">All installed protocols</span></span> | <span data-ttu-id="2b287-635">Protokoly podporované tímto rozbočovačem</span><span class="sxs-lookup"><span data-stu-id="2b287-635">Protocols supported by this hub.</span></span> <span data-ttu-id="2b287-636">Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly.</span><span class="sxs-lookup"><span data-stu-id="2b287-636">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="2b287-637">Pokud se `true` do klientů vrátí podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="2b287-637">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="2b287-638">Výchozí hodnota je `false` , protože tyto zprávy o výjimce můžou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="2b287-638">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="2b287-639">Maximální počet položek, které lze uložit do vyrovnávací paměti pro datové proudy pro odeslání klienta.</span><span class="sxs-lookup"><span data-stu-id="2b287-639">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="2b287-640">Pokud je dosaženo tohoto limitu, zpracování volání je blokováno, dokud server nezpracovává položky datového proudu.</span><span class="sxs-lookup"><span data-stu-id="2b287-640">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="2b287-641">32 KB</span><span class="sxs-lookup"><span data-stu-id="2b287-641">32 KB</span></span> | <span data-ttu-id="2b287-642">Maximální velikost jedné příchozí zprávy centra</span><span class="sxs-lookup"><span data-stu-id="2b287-642">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="2b287-643">Možnosti lze nakonfigurovat pro všechna centra tím, že poskytnete možnosti delegáta pro `AddSignalR` volání v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2b287-643">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="2b287-644">Možnosti pro jeden rozbočovač přepíší globální možnosti poskytované v `AddSignalR` a můžou být nakonfigurované pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="2b287-644">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="2b287-645">Rozšířené možnosti konfigurace protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="2b287-645">Advanced HTTP configuration options</span></span>

<span data-ttu-id="2b287-646">Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="2b287-646">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="2b287-647">Tyto možnosti jsou nakonfigurovány předáním delegáta [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="2b287-647">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseRouting();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapHub<ChatHub>("/chathub", options =>
        {
            options.Transports =
                HttpTransportType.WebSockets |
                HttpTransportType.LongPolling;
        });
    });
}
```

<span data-ttu-id="2b287-648">Následující tabulka popisuje možnosti konfigurace SignalR rozšířených možností protokolu HTTP v ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="2b287-648">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="2b287-649">Možnost</span><span class="sxs-lookup"><span data-stu-id="2b287-649">Option</span></span> | <span data-ttu-id="2b287-650">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-650">Default Value</span></span> | <span data-ttu-id="2b287-651">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-651">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="2b287-652">32 KB</span><span class="sxs-lookup"><span data-stu-id="2b287-652">32 KB</span></span> | <span data-ttu-id="2b287-653">Maximální počet bajtů přijatých od klienta, které jsou ukládány do vyrovnávací paměti serveru před použitím protitlaku.</span><span class="sxs-lookup"><span data-stu-id="2b287-653">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="2b287-654">Zvýšením této hodnoty umožníte, aby server přijímal větší zprávy rychleji bez použití zatížení, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="2b287-654">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="2b287-655">Data se automaticky shromažďují z `Authorize` atributů použitých pro třídu centra.</span><span class="sxs-lookup"><span data-stu-id="2b287-655">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="2b287-656">Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru</span><span class="sxs-lookup"><span data-stu-id="2b287-656">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="2b287-657">32 KB</span><span class="sxs-lookup"><span data-stu-id="2b287-657">32 KB</span></span> | <span data-ttu-id="2b287-658">Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru před pozorováním protitlaku.</span><span class="sxs-lookup"><span data-stu-id="2b287-658">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="2b287-659">Zvýšením této hodnoty umožníte serveru rychleji ukládat větší zprávy bez nutnosti očekávat zatížení, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="2b287-659">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="2b287-660">Všechny přenosy jsou povolené.</span><span class="sxs-lookup"><span data-stu-id="2b287-660">All Transports are enabled.</span></span> | <span data-ttu-id="2b287-661">Bitové příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenos, který může klient použít pro připojení.</span><span class="sxs-lookup"><span data-stu-id="2b287-661">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="2b287-662">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="2b287-662">See below.</span></span> | <span data-ttu-id="2b287-663">Další možnosti specifické pro přenos dlouhého cyklického dotazování.</span><span class="sxs-lookup"><span data-stu-id="2b287-663">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="2b287-664">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="2b287-664">See below.</span></span> | <span data-ttu-id="2b287-665">Další možnosti specifické pro přenos pomocí protokolu WebSockets</span><span class="sxs-lookup"><span data-stu-id="2b287-665">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="2b287-666">Přenos dlouhého cyklického dotazování má další možnosti, které je možné konfigurovat pomocí `LongPolling` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="2b287-666">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="2b287-667">Možnost</span><span class="sxs-lookup"><span data-stu-id="2b287-667">Option</span></span> | <span data-ttu-id="2b287-668">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-668">Default Value</span></span> | <span data-ttu-id="2b287-669">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-669">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="2b287-670">90 sekund</span><span class="sxs-lookup"><span data-stu-id="2b287-670">90 seconds</span></span> | <span data-ttu-id="2b287-671">Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jedné žádosti o cyklické dotazování.</span><span class="sxs-lookup"><span data-stu-id="2b287-671">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="2b287-672">Snížení této hodnoty způsobí, že klient bude vydávat nové požadavky na dotaz častěji.</span><span class="sxs-lookup"><span data-stu-id="2b287-672">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="2b287-673">Přenos protokolu WebSocket má další možnosti, které je možné konfigurovat pomocí `WebSockets` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="2b287-673">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="2b287-674">Možnost</span><span class="sxs-lookup"><span data-stu-id="2b287-674">Option</span></span> | <span data-ttu-id="2b287-675">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-675">Default Value</span></span> | <span data-ttu-id="2b287-676">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-676">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="2b287-677">5 sekund</span><span class="sxs-lookup"><span data-stu-id="2b287-677">5 seconds</span></span> | <span data-ttu-id="2b287-678">Pokud se po ukončení serveru aplikace v tomto časovém intervalu nepovede zavřít, připojení se ukončí.</span><span class="sxs-lookup"><span data-stu-id="2b287-678">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="2b287-679">Delegát, který lze použít k nastavení `Sec-WebSocket-Protocol` záhlaví na vlastní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="2b287-679">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="2b287-680">Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že se vrátí požadovaná hodnota.</span><span class="sxs-lookup"><span data-stu-id="2b287-680">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="2b287-681">Konfigurace možností klienta</span><span class="sxs-lookup"><span data-stu-id="2b287-681">Configure client options</span></span>

<span data-ttu-id="2b287-682">Možnosti klienta lze nakonfigurovat pro `HubConnectionBuilder` typ (k dispozici v klientech rozhraní .NET a v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="2b287-682">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="2b287-683">Je také k dispozici v klientovi Java, ale `HttpHubConnectionBuilder` podtřídou je to, co obsahuje možnosti konfigurace tvůrce a také na `HubConnection` sebe sama.</span><span class="sxs-lookup"><span data-stu-id="2b287-683">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="2b287-684">Konfigurovat protokolování</span><span class="sxs-lookup"><span data-stu-id="2b287-684">Configure logging</span></span>

<span data-ttu-id="2b287-685">Protokolování je konfigurováno v klientovi .NET pomocí `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="2b287-685">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="2b287-686">Zprostředkovatelé protokolování a filtry je možné zaregistrovat stejným způsobem jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="2b287-686">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="2b287-687">Další informace najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="2b287-687">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="2b287-688">Aby bylo možné registrovat poskytovatele protokolování, je nutné nainstalovat potřebné balíčky.</span><span class="sxs-lookup"><span data-stu-id="2b287-688">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="2b287-689">Úplný seznam najdete v části [předdefinovaná zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="2b287-689">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="2b287-690">Chcete-li například povolit protokolování konzoly, nainstalujte `Microsoft.Extensions.Logging.Console` balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="2b287-690">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="2b287-691">Zavolejte `AddConsole` metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="2b287-691">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="2b287-692">V klientovi jazyka JavaScript existuje podobná `configureLogging` metoda.</span><span class="sxs-lookup"><span data-stu-id="2b287-692">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="2b287-693">Zadejte `LogLevel` hodnotu, která určuje minimální úroveň zpráv protokolu, které mají být vyprodukovány.</span><span class="sxs-lookup"><span data-stu-id="2b287-693">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="2b287-694">Protokoly se zapisují do okna konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="2b287-694">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="2b287-695">Místo `LogLevel` hodnoty můžete zadat také `string` hodnotu představující název úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="2b287-695">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="2b287-696">To je užitečné při konfiguraci SignalR protokolování v prostředích, kde nemáte přístup k `LogLevel` konstantám.</span><span class="sxs-lookup"><span data-stu-id="2b287-696">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="2b287-697">V následující tabulce jsou uvedeny dostupné úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="2b287-697">The following table lists the available log levels.</span></span> <span data-ttu-id="2b287-698">Hodnota, kterou zadáte, `configureLogging` nastaví **minimální** úroveň protokolu, která se bude protokolovat.</span><span class="sxs-lookup"><span data-stu-id="2b287-698">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="2b287-699">Zprávy zaznamenané na této úrovni **nebo úrovně uvedené za ní v tabulce**budou protokolovány.</span><span class="sxs-lookup"><span data-stu-id="2b287-699">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="2b287-700">Řetězec</span><span class="sxs-lookup"><span data-stu-id="2b287-700">String</span></span>                      | <span data-ttu-id="2b287-701">LogLevel</span><span class="sxs-lookup"><span data-stu-id="2b287-701">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="2b287-702">`info` **nebo** `information`</span><span class="sxs-lookup"><span data-stu-id="2b287-702">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="2b287-703">`warn` **nebo** `warning`</span><span class="sxs-lookup"><span data-stu-id="2b287-703">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="2b287-704">Chcete-li protokolování zcela zakázat, zadejte `signalR.LogLevel.None` v `configureLogging` metodě.</span><span class="sxs-lookup"><span data-stu-id="2b287-704">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="2b287-705">Další informace o protokolování naleznete v dokumentaci k [ SignalR diagnostice](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="2b287-705">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="2b287-706">SignalRKlient Java používá k protokolování knihovnu [SLF4J](https://www.slf4j.org/) .</span><span class="sxs-lookup"><span data-stu-id="2b287-706">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="2b287-707">Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí.</span><span class="sxs-lookup"><span data-stu-id="2b287-707">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="2b287-708">Následující fragment kódu ukazuje, jak používat `java.util.logging` s SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="2b287-708">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="2b287-709">Pokud ve svých závislostech nenakonfigurujete protokolování, SLF4J načte výchozí protokolovací nástroj No-operation s následující zprávou upozornění:</span><span class="sxs-lookup"><span data-stu-id="2b287-709">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="2b287-710">Tuto možnost lze bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="2b287-710">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="2b287-711">Konfigurace povolených přenosů</span><span class="sxs-lookup"><span data-stu-id="2b287-711">Configure allowed transports</span></span>

<span data-ttu-id="2b287-712">Přenosy používané nástrojem SignalR lze konfigurovat ve `WithUrl` volání ( `withUrl` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="2b287-712">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="2b287-713">Bitové nebo hodnoty `HttpTransportType` lze použít k omezení klienta na používání pouze zadaných přenosů.</span><span class="sxs-lookup"><span data-stu-id="2b287-713">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="2b287-714">Ve výchozím nastavení jsou povolené všechny přenosy.</span><span class="sxs-lookup"><span data-stu-id="2b287-714">All transports are enabled by default.</span></span>

<span data-ttu-id="2b287-715">Pokud třeba chcete zakázat přenos událostí odeslaných serverem, ale povolit WebSockets a dlouhá připojení s dotazem:</span><span class="sxs-lookup"><span data-stu-id="2b287-715">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="2b287-716">V klientu jazyka JavaScript jsou přenosy konfigurovány nastavením `transport` pole v objektu Options, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="2b287-716">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="2b287-717">V této verzi je jediným dostupným přenosem klientský WebSocket v jazyce Java.</span><span class="sxs-lookup"><span data-stu-id="2b287-717">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="2b287-718">V klientovi Java se přenos vybere s `withTransport` metodou na `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="2b287-718">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="2b287-719">Klient Java standardně používá přenos pomocí protokolu WebSockets.</span><span class="sxs-lookup"><span data-stu-id="2b287-719">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="2b287-720">SignalRKlient Java ještě nepodporuje přenosovou zálohu.</span><span class="sxs-lookup"><span data-stu-id="2b287-720">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="2b287-721">Konfigurace ověřování nosiče</span><span class="sxs-lookup"><span data-stu-id="2b287-721">Configure bearer authentication</span></span>

<span data-ttu-id="2b287-722">K poskytnutí ověřovacích dat spolu s SignalR požadavky použijte `AccessTokenProvider` možnost ( `accessTokenFactory` v jazyce JavaScript) k určení funkce, která vrací požadovaný přístupový token.</span><span class="sxs-lookup"><span data-stu-id="2b287-722">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="2b287-723">V klientovi .NET se tento přístupový token předává jako token ověřování HTTP "Bearer" (s použitím `Authorization` hlavičky s typem `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="2b287-723">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="2b287-724">V klientu jazyka JavaScript se přístupový token používá jako nosný token, **s výjimkou** případů, kdy rozhraní API prohlížeče omezuje možnost použít hlavičky (konkrétně v požadavcích na události odeslané serverem a objekty WebSockets).</span><span class="sxs-lookup"><span data-stu-id="2b287-724">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="2b287-725">V těchto případech je přístupový token k dispozici jako hodnota řetězce dotazu `access_token` .</span><span class="sxs-lookup"><span data-stu-id="2b287-725">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="2b287-726">V klientu .NET `AccessTokenProvider` lze možnost zadat pomocí delegáta možnosti v `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="2b287-726">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="2b287-727">V klientovi JavaScriptu je přístupový token nakonfigurovaný nastavením `accessTokenFactory` pole v objektu Options v `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="2b287-727">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="2b287-728">V SignalR klientovi Java můžete nakonfigurovat nosný token, který se bude používat pro ověřování, a to poskytnutím továrny přístupového tokenu [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="2b287-728">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="2b287-729">Pomocí [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) můžete zadat [RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="2b287-729">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="2b287-730">Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.</span><span class="sxs-lookup"><span data-stu-id="2b287-730">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="2b287-731">Konfigurace možností timeout a Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="2b287-731">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="2b287-732">Další možnosti konfigurace časového limitu a chování funkce Keep-Alive jsou k dispozici na `HubConnection` samotném objektu:</span><span class="sxs-lookup"><span data-stu-id="2b287-732">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="2b287-733">.NET</span><span class="sxs-lookup"><span data-stu-id="2b287-733">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="2b287-734">Možnost</span><span class="sxs-lookup"><span data-stu-id="2b287-734">Option</span></span> | <span data-ttu-id="2b287-735">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-735">Default value</span></span> | <span data-ttu-id="2b287-736">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-736">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="2b287-737">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="2b287-737">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="2b287-738">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="2b287-738">Timeout for server activity.</span></span> <span data-ttu-id="2b287-739">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí `Closed` událost ( `onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="2b287-739">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="2b287-740">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="2b287-740">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="2b287-741">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="2b287-741">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="2b287-742">15 sekund</span><span class="sxs-lookup"><span data-stu-id="2b287-742">15 seconds</span></span> | <span data-ttu-id="2b287-743">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="2b287-743">Timeout for initial server handshake.</span></span> <span data-ttu-id="2b287-744">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a spustí `Closed` událost ( `onclose` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="2b287-744">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="2b287-745">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="2b287-745">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="2b287-746">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="2b287-746">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="2b287-747">15 sekund</span><span class="sxs-lookup"><span data-stu-id="2b287-747">15 seconds</span></span> | <span data-ttu-id="2b287-748">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="2b287-748">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="2b287-749">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="2b287-749">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="2b287-750">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="2b287-750">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="2b287-751">V klientovi .NET jsou hodnoty časového limitu zadány jako `TimeSpan` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="2b287-751">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="2b287-752">JavaScript</span><span class="sxs-lookup"><span data-stu-id="2b287-752">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="2b287-753">Možnost</span><span class="sxs-lookup"><span data-stu-id="2b287-753">Option</span></span> | <span data-ttu-id="2b287-754">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-754">Default value</span></span> | <span data-ttu-id="2b287-755">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-755">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="2b287-756">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="2b287-756">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="2b287-757">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="2b287-757">Timeout for server activity.</span></span> <span data-ttu-id="2b287-758">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onclose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="2b287-758">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="2b287-759">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="2b287-759">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="2b287-760">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="2b287-760">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="2b287-761">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="2b287-761">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="2b287-762">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="2b287-762">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="2b287-763">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="2b287-763">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="2b287-764">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="2b287-764">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="2b287-765">Java</span><span class="sxs-lookup"><span data-stu-id="2b287-765">Java</span></span>](#tab/java)

| <span data-ttu-id="2b287-766">Možnost</span><span class="sxs-lookup"><span data-stu-id="2b287-766">Option</span></span> | <span data-ttu-id="2b287-767">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-767">Default value</span></span> | <span data-ttu-id="2b287-768">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-768">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="2b287-769">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="2b287-769">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="2b287-770">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="2b287-770">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="2b287-771">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="2b287-771">Timeout for server activity.</span></span> <span data-ttu-id="2b287-772">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onClose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="2b287-772">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="2b287-773">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="2b287-773">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="2b287-774">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="2b287-774">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="2b287-775">15 sekund</span><span class="sxs-lookup"><span data-stu-id="2b287-775">15 seconds</span></span> | <span data-ttu-id="2b287-776">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="2b287-776">Timeout for initial server handshake.</span></span> <span data-ttu-id="2b287-777">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje `onClose` událost.</span><span class="sxs-lookup"><span data-stu-id="2b287-777">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="2b287-778">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="2b287-778">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="2b287-779">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="2b287-779">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="2b287-780">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="2b287-780">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="2b287-781">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="2b287-781">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="2b287-782">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="2b287-782">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="2b287-783">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="2b287-783">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="2b287-784">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="2b287-784">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="2b287-785">Konfigurace dalších možností</span><span class="sxs-lookup"><span data-stu-id="2b287-785">Configure additional options</span></span>

<span data-ttu-id="2b287-786">Další možnosti lze konfigurovat v `WithUrl` `withUrl` metodě (v jazyce JavaScript) na `HubConnectionBuilder` různých konfiguračních rozhraních API v `HttpHubConnectionBuilder` klientovi Java:</span><span class="sxs-lookup"><span data-stu-id="2b287-786">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="2b287-787">.NET</span><span class="sxs-lookup"><span data-stu-id="2b287-787">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="2b287-788">Možnost .NET</span><span class="sxs-lookup"><span data-stu-id="2b287-788">.NET Option</span></span> |  <span data-ttu-id="2b287-789">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-789">Default value</span></span> | <span data-ttu-id="2b287-790">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-790">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="2b287-791">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="2b287-791">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="2b287-792">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="2b287-792">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="2b287-793">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="2b287-793">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="2b287-794">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="2b287-794">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="2b287-795">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="2b287-795">Empty</span></span> | <span data-ttu-id="2b287-796">Kolekce certifikátů TLS pro odeslání na požadavky na ověření.</span><span class="sxs-lookup"><span data-stu-id="2b287-796">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="2b287-797">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="2b287-797">Empty</span></span> | <span data-ttu-id="2b287-798">Kolekce souborů cookie protokolu HTTP, které se mají odeslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="2b287-798">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="2b287-799">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="2b287-799">Empty</span></span> | <span data-ttu-id="2b287-800">Přihlašovací údaje, které se mají poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="2b287-800">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="2b287-801">5 sekund</span><span class="sxs-lookup"><span data-stu-id="2b287-801">5 seconds</span></span> | <span data-ttu-id="2b287-802">Jenom objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="2b287-802">WebSockets only.</span></span> <span data-ttu-id="2b287-803">Maximální doba, po jejímž uplynutí bude klient čekat na ukončení žádosti o uzavření.</span><span class="sxs-lookup"><span data-stu-id="2b287-803">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="2b287-804">Pokud server nepotvrdí zavření v této době, klient se odpojí.</span><span class="sxs-lookup"><span data-stu-id="2b287-804">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="2b287-805">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="2b287-805">Empty</span></span> | <span data-ttu-id="2b287-806">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="2b287-806">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="2b287-807">Delegát, který lze použít ke konfiguraci nebo nahrazení `HttpMessageHandler` používaného k odeslání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="2b287-807">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="2b287-808">Nepoužívá se pro připojení pomocí protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="2b287-808">Not used for WebSocket connections.</span></span> <span data-ttu-id="2b287-809">Tento delegát musí vracet hodnotu, která není null, a obdrží výchozí hodnotu jako parametr.</span><span class="sxs-lookup"><span data-stu-id="2b287-809">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="2b287-810">Buď změňte nastavení u této výchozí hodnoty a vraťte je, nebo vraťte novou `HttpMessageHandler` instanci.</span><span class="sxs-lookup"><span data-stu-id="2b287-810">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="2b287-811">**Při nahrazování obslužné rutiny Nezapomeňte zkopírovat nastavení, která chcete zachovat, od poskytnuté obslužné rutiny, jinak konfigurované možnosti (například soubory cookie a záhlaví) nebudou použity pro novou obslužnou rutinu.**</span><span class="sxs-lookup"><span data-stu-id="2b287-811">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="2b287-812">Proxy server HTTP, který se má použít při odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="2b287-812">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="2b287-813">Nastavte tuto logickou hodnotu pro odeslání výchozích přihlašovacích údajů pro požadavky HTTP a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="2b287-813">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="2b287-814">To umožňuje použití ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="2b287-814">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="2b287-815">Delegát, který lze použít ke konfiguraci dalších možností protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="2b287-815">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="2b287-816">Přijímá instanci [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , která se dá použít ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="2b287-816">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="2b287-817">JavaScript</span><span class="sxs-lookup"><span data-stu-id="2b287-817">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="2b287-818">Možnost JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="2b287-818">JavaScript Option</span></span> | <span data-ttu-id="2b287-819">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-819">Default Value</span></span> | <span data-ttu-id="2b287-820">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-820">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="2b287-821">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="2b287-821">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="2b287-822">Nastavte na `true` protokolování bajtů/znaků zpráv odesílaných a přijímaných klientem.</span><span class="sxs-lookup"><span data-stu-id="2b287-822">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="2b287-823">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="2b287-823">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="2b287-824">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="2b287-824">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="2b287-825">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="2b287-825">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="2b287-826">Java</span><span class="sxs-lookup"><span data-stu-id="2b287-826">Java</span></span>](#tab/java)

| <span data-ttu-id="2b287-827">Možnost jazyka Java</span><span class="sxs-lookup"><span data-stu-id="2b287-827">Java Option</span></span> | <span data-ttu-id="2b287-828">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-828">Default Value</span></span> | <span data-ttu-id="2b287-829">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-829">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="2b287-830">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="2b287-830">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="2b287-831">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="2b287-831">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="2b287-832">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="2b287-832">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="2b287-833">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="2b287-833">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="2b287-834">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="2b287-834">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="2b287-835">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="2b287-835">Empty</span></span> | <span data-ttu-id="2b287-836">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="2b287-836">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="2b287-837">V klientu .NET můžete tyto možnosti upravit pomocí delegáta možností, který je k dispozici pro `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="2b287-837">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="2b287-838">V klientovi JavaScriptu lze tyto možnosti poskytnout v objektu jazyka JavaScript, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="2b287-838">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="2b287-839">V klientovi Java lze tyto možnosti nakonfigurovat s metodami `HttpHubConnectionBuilder` vrácenými z`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="2b287-839">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="2b287-840">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="2b287-840">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="2b287-841">Možnosti serializace JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="2b287-841">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="2b287-842">ASP.NET Core SignalR podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="2b287-842">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="2b287-843">Každý protokol má možnosti konfigurace serializace.</span><span class="sxs-lookup"><span data-stu-id="2b287-843">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="2b287-844">Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , kterou lze přidat po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v `Startup.ConfigureServices` metodě.</span><span class="sxs-lookup"><span data-stu-id="2b287-844">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="2b287-845">`AddJsonProtocol`Metoda přebírá delegáta, který přijímá `options` objekt.</span><span class="sxs-lookup"><span data-stu-id="2b287-845">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="2b287-846">Vlastnost [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) tohoto objektu je `JsonSerializerSettings` objekt JSON.NET, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="2b287-846">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="2b287-847">Další informace najdete v [dokumentaci k JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="2b287-847">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="2b287-848">Chcete-li například nakonfigurovat serializátor pro použití názvů vlastností "PascalCase" namísto výchozích názvů "camelCase", použijte následující kód v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2b287-848">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="2b287-849">V klientovi .NET `AddJsonProtocol` existuje stejná rozšiřující metoda v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="2b287-849">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="2b287-850">`Microsoft.Extensions.DependencyInjection`Obor názvů musí být importován, aby bylo možné přeložit metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="2b287-850">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="2b287-851">V tuto chvíli není možné konfigurovat serializaci JSON v klientovi jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2b287-851">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="2b287-852">Možnosti serializace MessagePack</span><span class="sxs-lookup"><span data-stu-id="2b287-852">MessagePack serialization options</span></span>

<span data-ttu-id="2b287-853">Serializaci MessagePack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="2b287-853">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="2b287-854">Další podrobnosti najdete [v SignalR tématu MessagePack](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="2b287-854">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="2b287-855">V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi v jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2b287-855">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="2b287-856">Konfigurovat možnosti serveru</span><span class="sxs-lookup"><span data-stu-id="2b287-856">Configure server options</span></span>

<span data-ttu-id="2b287-857">Následující tabulka popisuje možnosti konfigurace SignalR Center:</span><span class="sxs-lookup"><span data-stu-id="2b287-857">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="2b287-858">Možnost</span><span class="sxs-lookup"><span data-stu-id="2b287-858">Option</span></span> | <span data-ttu-id="2b287-859">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-859">Default Value</span></span> | <span data-ttu-id="2b287-860">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-860">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="2b287-861">30 sekund</span><span class="sxs-lookup"><span data-stu-id="2b287-861">30 seconds</span></span> | <span data-ttu-id="2b287-862">Server bude předpokládat, že je klient odpojený, pokud v tomto intervalu neobdržel zprávu (včetně Keep-Alive).</span><span class="sxs-lookup"><span data-stu-id="2b287-862">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="2b287-863">Může trvat delší dobu, než je tento časový limit, aby bylo možné klienta ve skutečnosti označit jako odpojený, protože to je implementováno.</span><span class="sxs-lookup"><span data-stu-id="2b287-863">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="2b287-864">Doporučená hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="2b287-864">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="2b287-865">15 sekund</span><span class="sxs-lookup"><span data-stu-id="2b287-865">15 seconds</span></span> | <span data-ttu-id="2b287-866">Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře.</span><span class="sxs-lookup"><span data-stu-id="2b287-866">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="2b287-867">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="2b287-867">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="2b287-868">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="2b287-868">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="2b287-869">15 sekund</span><span class="sxs-lookup"><span data-stu-id="2b287-869">15 seconds</span></span> | <span data-ttu-id="2b287-870">Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="2b287-870">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="2b287-871">Při změně `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení změňte nastavení v klientovi.</span><span class="sxs-lookup"><span data-stu-id="2b287-871">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="2b287-872">Doporučená `ServerTimeout` / `serverTimeoutInMilliseconds` hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="2b287-872">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="2b287-873">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="2b287-873">All installed protocols</span></span> | <span data-ttu-id="2b287-874">Protokoly podporované tímto rozbočovačem</span><span class="sxs-lookup"><span data-stu-id="2b287-874">Protocols supported by this hub.</span></span> <span data-ttu-id="2b287-875">Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly.</span><span class="sxs-lookup"><span data-stu-id="2b287-875">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="2b287-876">Pokud se `true` do klientů vrátí podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="2b287-876">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="2b287-877">Výchozí hodnota je `false` , protože tyto zprávy o výjimce můžou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="2b287-877">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="2b287-878">Možnosti lze nakonfigurovat pro všechna centra tím, že poskytnete možnosti delegáta pro `AddSignalR` volání v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2b287-878">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="2b287-879">Možnosti pro jeden rozbočovač přepíší globální možnosti poskytované v `AddSignalR` a můžou být nakonfigurované pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="2b287-879">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="2b287-880">Rozšířené možnosti konfigurace protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="2b287-880">Advanced HTTP configuration options</span></span>

<span data-ttu-id="2b287-881">Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="2b287-881">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="2b287-882">Tyto možnosti jsou nakonfigurovány předáním delegáta [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="2b287-882">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="2b287-883">Následující tabulka popisuje možnosti konfigurace SignalR rozšířených možností protokolu HTTP v ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="2b287-883">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="2b287-884">Možnost</span><span class="sxs-lookup"><span data-stu-id="2b287-884">Option</span></span> | <span data-ttu-id="2b287-885">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-885">Default Value</span></span> | <span data-ttu-id="2b287-886">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-886">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="2b287-887">32 KB</span><span class="sxs-lookup"><span data-stu-id="2b287-887">32 KB</span></span> | <span data-ttu-id="2b287-888">Maximální počet bajtů přijatých od klienta, které jsou vyrovnávací paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="2b287-888">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="2b287-889">Zvýšením této hodnoty umožníte serveru přijímat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="2b287-889">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="2b287-890">Data se automaticky shromažďují z `Authorize` atributů použitých pro třídu centra.</span><span class="sxs-lookup"><span data-stu-id="2b287-890">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="2b287-891">Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru</span><span class="sxs-lookup"><span data-stu-id="2b287-891">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="2b287-892">32 KB</span><span class="sxs-lookup"><span data-stu-id="2b287-892">32 KB</span></span> | <span data-ttu-id="2b287-893">Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="2b287-893">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="2b287-894">Zvýšením této hodnoty umožníte serveru odesílat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="2b287-894">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="2b287-895">Všechny přenosy jsou povolené.</span><span class="sxs-lookup"><span data-stu-id="2b287-895">All Transports are enabled.</span></span> | <span data-ttu-id="2b287-896">Bitové příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenos, který může klient použít pro připojení.</span><span class="sxs-lookup"><span data-stu-id="2b287-896">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="2b287-897">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="2b287-897">See below.</span></span> | <span data-ttu-id="2b287-898">Další možnosti specifické pro přenos dlouhého cyklického dotazování.</span><span class="sxs-lookup"><span data-stu-id="2b287-898">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="2b287-899">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="2b287-899">See below.</span></span> | <span data-ttu-id="2b287-900">Další možnosti specifické pro přenos pomocí protokolu WebSockets</span><span class="sxs-lookup"><span data-stu-id="2b287-900">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="2b287-901">Přenos dlouhého cyklického dotazování má další možnosti, které je možné konfigurovat pomocí `LongPolling` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="2b287-901">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="2b287-902">Možnost</span><span class="sxs-lookup"><span data-stu-id="2b287-902">Option</span></span> | <span data-ttu-id="2b287-903">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-903">Default Value</span></span> | <span data-ttu-id="2b287-904">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-904">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="2b287-905">90 sekund</span><span class="sxs-lookup"><span data-stu-id="2b287-905">90 seconds</span></span> | <span data-ttu-id="2b287-906">Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jedné žádosti o cyklické dotazování.</span><span class="sxs-lookup"><span data-stu-id="2b287-906">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="2b287-907">Snížení této hodnoty způsobí, že klient bude vydávat nové požadavky na dotaz častěji.</span><span class="sxs-lookup"><span data-stu-id="2b287-907">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="2b287-908">Přenos protokolu WebSocket má další možnosti, které je možné konfigurovat pomocí `WebSockets` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="2b287-908">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="2b287-909">Možnost</span><span class="sxs-lookup"><span data-stu-id="2b287-909">Option</span></span> | <span data-ttu-id="2b287-910">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-910">Default Value</span></span> | <span data-ttu-id="2b287-911">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-911">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="2b287-912">5 sekund</span><span class="sxs-lookup"><span data-stu-id="2b287-912">5 seconds</span></span> | <span data-ttu-id="2b287-913">Pokud se po ukončení serveru aplikace v tomto časovém intervalu nepovede zavřít, připojení se ukončí.</span><span class="sxs-lookup"><span data-stu-id="2b287-913">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="2b287-914">Delegát, který lze použít k nastavení `Sec-WebSocket-Protocol` záhlaví na vlastní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="2b287-914">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="2b287-915">Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že se vrátí požadovaná hodnota.</span><span class="sxs-lookup"><span data-stu-id="2b287-915">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="2b287-916">Konfigurace možností klienta</span><span class="sxs-lookup"><span data-stu-id="2b287-916">Configure client options</span></span>

<span data-ttu-id="2b287-917">Možnosti klienta lze nakonfigurovat pro `HubConnectionBuilder` typ (k dispozici v klientech rozhraní .NET a v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="2b287-917">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="2b287-918">Je také k dispozici v klientovi Java, ale `HttpHubConnectionBuilder` podtřídou je to, co obsahuje možnosti konfigurace tvůrce a také na `HubConnection` sebe sama.</span><span class="sxs-lookup"><span data-stu-id="2b287-918">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="2b287-919">Konfigurovat protokolování</span><span class="sxs-lookup"><span data-stu-id="2b287-919">Configure logging</span></span>

<span data-ttu-id="2b287-920">Protokolování je konfigurováno v klientovi .NET pomocí `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="2b287-920">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="2b287-921">Zprostředkovatelé protokolování a filtry je možné zaregistrovat stejným způsobem jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="2b287-921">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="2b287-922">Další informace najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="2b287-922">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="2b287-923">Aby bylo možné registrovat poskytovatele protokolování, je nutné nainstalovat potřebné balíčky.</span><span class="sxs-lookup"><span data-stu-id="2b287-923">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="2b287-924">Úplný seznam najdete v části [předdefinovaná zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="2b287-924">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="2b287-925">Chcete-li například povolit protokolování konzoly, nainstalujte `Microsoft.Extensions.Logging.Console` balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="2b287-925">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="2b287-926">Zavolejte `AddConsole` metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="2b287-926">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="2b287-927">V klientovi jazyka JavaScript existuje podobná `configureLogging` metoda.</span><span class="sxs-lookup"><span data-stu-id="2b287-927">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="2b287-928">Zadejte `LogLevel` hodnotu, která určuje minimální úroveň zpráv protokolu, které mají být vyprodukovány.</span><span class="sxs-lookup"><span data-stu-id="2b287-928">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="2b287-929">Protokoly se zapisují do okna konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="2b287-929">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="2b287-930">Chcete-li protokolování zcela zakázat, zadejte `signalR.LogLevel.None` v `configureLogging` metodě.</span><span class="sxs-lookup"><span data-stu-id="2b287-930">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="2b287-931">Další informace o protokolování naleznete v dokumentaci k [ SignalR diagnostice](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="2b287-931">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="2b287-932">SignalRKlient Java používá k protokolování knihovnu [SLF4J](https://www.slf4j.org/) .</span><span class="sxs-lookup"><span data-stu-id="2b287-932">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="2b287-933">Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí.</span><span class="sxs-lookup"><span data-stu-id="2b287-933">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="2b287-934">Následující fragment kódu ukazuje, jak používat `java.util.logging` s SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="2b287-934">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="2b287-935">Pokud ve svých závislostech nenakonfigurujete protokolování, SLF4J načte výchozí protokolovací nástroj No-operation s následující zprávou upozornění:</span><span class="sxs-lookup"><span data-stu-id="2b287-935">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="2b287-936">Tuto možnost lze bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="2b287-936">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="2b287-937">Konfigurace povolených přenosů</span><span class="sxs-lookup"><span data-stu-id="2b287-937">Configure allowed transports</span></span>

<span data-ttu-id="2b287-938">Přenosy používané nástrojem SignalR lze konfigurovat ve `WithUrl` volání ( `withUrl` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="2b287-938">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="2b287-939">Bitové nebo hodnoty `HttpTransportType` lze použít k omezení klienta na používání pouze zadaných přenosů.</span><span class="sxs-lookup"><span data-stu-id="2b287-939">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="2b287-940">Ve výchozím nastavení jsou povolené všechny přenosy.</span><span class="sxs-lookup"><span data-stu-id="2b287-940">All transports are enabled by default.</span></span>

<span data-ttu-id="2b287-941">Pokud třeba chcete zakázat přenos událostí odeslaných serverem, ale povolit WebSockets a dlouhá připojení s dotazem:</span><span class="sxs-lookup"><span data-stu-id="2b287-941">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="2b287-942">V klientu jazyka JavaScript jsou přenosy konfigurovány nastavením `transport` pole v objektu Options, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="2b287-942">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="2b287-943">V této verzi je jediným dostupným přenosem klientský WebSocket v jazyce Java.</span><span class="sxs-lookup"><span data-stu-id="2b287-943">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="2b287-944">Konfigurace ověřování nosiče</span><span class="sxs-lookup"><span data-stu-id="2b287-944">Configure bearer authentication</span></span>

<span data-ttu-id="2b287-945">K poskytnutí ověřovacích dat spolu s SignalR požadavky použijte `AccessTokenProvider` možnost ( `accessTokenFactory` v jazyce JavaScript) k určení funkce, která vrací požadovaný přístupový token.</span><span class="sxs-lookup"><span data-stu-id="2b287-945">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="2b287-946">V klientovi .NET se tento přístupový token předává jako token ověřování HTTP "Bearer" (s použitím `Authorization` hlavičky s typem `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="2b287-946">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="2b287-947">V klientu jazyka JavaScript se přístupový token používá jako nosný token, **s výjimkou** případů, kdy rozhraní API prohlížeče omezuje možnost použít hlavičky (konkrétně v požadavcích na události odeslané serverem a objekty WebSockets).</span><span class="sxs-lookup"><span data-stu-id="2b287-947">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="2b287-948">V těchto případech je přístupový token k dispozici jako hodnota řetězce dotazu `access_token` .</span><span class="sxs-lookup"><span data-stu-id="2b287-948">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="2b287-949">V klientu .NET `AccessTokenProvider` lze možnost zadat pomocí delegáta možnosti v `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="2b287-949">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="2b287-950">V klientovi JavaScriptu je přístupový token nakonfigurovaný nastavením `accessTokenFactory` pole v objektu Options v `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="2b287-950">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="2b287-951">V SignalR klientovi Java můžete nakonfigurovat nosný token, který se bude používat pro ověřování, a to poskytnutím továrny přístupového tokenu [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="2b287-951">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="2b287-952">Pomocí [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) můžete zadat [RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="2b287-952">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="2b287-953">Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.</span><span class="sxs-lookup"><span data-stu-id="2b287-953">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="2b287-954">Konfigurace možností timeout a Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="2b287-954">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="2b287-955">Další možnosti konfigurace časového limitu a chování funkce Keep-Alive jsou k dispozici na `HubConnection` samotném objektu:</span><span class="sxs-lookup"><span data-stu-id="2b287-955">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="2b287-956">.NET</span><span class="sxs-lookup"><span data-stu-id="2b287-956">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="2b287-957">Možnost</span><span class="sxs-lookup"><span data-stu-id="2b287-957">Option</span></span> | <span data-ttu-id="2b287-958">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-958">Default value</span></span> | <span data-ttu-id="2b287-959">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-959">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="2b287-960">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="2b287-960">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="2b287-961">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="2b287-961">Timeout for server activity.</span></span> <span data-ttu-id="2b287-962">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí `Closed` událost ( `onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="2b287-962">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="2b287-963">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="2b287-963">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="2b287-964">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="2b287-964">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="2b287-965">15 sekund</span><span class="sxs-lookup"><span data-stu-id="2b287-965">15 seconds</span></span> | <span data-ttu-id="2b287-966">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="2b287-966">Timeout for initial server handshake.</span></span> <span data-ttu-id="2b287-967">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a spustí `Closed` událost ( `onclose` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="2b287-967">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="2b287-968">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="2b287-968">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="2b287-969">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="2b287-969">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="2b287-970">15 sekund</span><span class="sxs-lookup"><span data-stu-id="2b287-970">15 seconds</span></span> | <span data-ttu-id="2b287-971">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="2b287-971">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="2b287-972">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="2b287-972">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="2b287-973">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="2b287-973">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="2b287-974">V klientovi .NET jsou hodnoty časového limitu zadány jako `TimeSpan` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="2b287-974">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="2b287-975">JavaScript</span><span class="sxs-lookup"><span data-stu-id="2b287-975">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="2b287-976">Možnost</span><span class="sxs-lookup"><span data-stu-id="2b287-976">Option</span></span> | <span data-ttu-id="2b287-977">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-977">Default value</span></span> | <span data-ttu-id="2b287-978">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-978">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="2b287-979">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="2b287-979">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="2b287-980">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="2b287-980">Timeout for server activity.</span></span> <span data-ttu-id="2b287-981">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onclose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="2b287-981">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="2b287-982">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="2b287-982">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="2b287-983">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="2b287-983">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="2b287-984">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="2b287-984">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="2b287-985">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="2b287-985">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="2b287-986">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="2b287-986">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="2b287-987">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="2b287-987">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="2b287-988">Java</span><span class="sxs-lookup"><span data-stu-id="2b287-988">Java</span></span>](#tab/java)

| <span data-ttu-id="2b287-989">Možnost</span><span class="sxs-lookup"><span data-stu-id="2b287-989">Option</span></span> | <span data-ttu-id="2b287-990">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-990">Default value</span></span> | <span data-ttu-id="2b287-991">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-991">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="2b287-992">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="2b287-992">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="2b287-993">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="2b287-993">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="2b287-994">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="2b287-994">Timeout for server activity.</span></span> <span data-ttu-id="2b287-995">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onClose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="2b287-995">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="2b287-996">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="2b287-996">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="2b287-997">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="2b287-997">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="2b287-998">15 sekund</span><span class="sxs-lookup"><span data-stu-id="2b287-998">15 seconds</span></span> | <span data-ttu-id="2b287-999">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="2b287-999">Timeout for initial server handshake.</span></span> <span data-ttu-id="2b287-1000">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje `onClose` událost.</span><span class="sxs-lookup"><span data-stu-id="2b287-1000">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="2b287-1001">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="2b287-1001">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="2b287-1002">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="2b287-1002">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="2b287-1003">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="2b287-1003">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="2b287-1004">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="2b287-1004">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="2b287-1005">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="2b287-1005">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="2b287-1006">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="2b287-1006">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="2b287-1007">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="2b287-1007">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="2b287-1008">Konfigurace dalších možností</span><span class="sxs-lookup"><span data-stu-id="2b287-1008">Configure additional options</span></span>

<span data-ttu-id="2b287-1009">Další možnosti lze konfigurovat v `WithUrl` `withUrl` metodě (v jazyce JavaScript) na `HubConnectionBuilder` různých konfiguračních rozhraních API v `HttpHubConnectionBuilder` klientovi Java:</span><span class="sxs-lookup"><span data-stu-id="2b287-1009">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="2b287-1010">.NET</span><span class="sxs-lookup"><span data-stu-id="2b287-1010">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="2b287-1011">Možnost .NET</span><span class="sxs-lookup"><span data-stu-id="2b287-1011">.NET Option</span></span> |  <span data-ttu-id="2b287-1012">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-1012">Default value</span></span> | <span data-ttu-id="2b287-1013">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-1013">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="2b287-1014">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="2b287-1014">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="2b287-1015">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="2b287-1015">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="2b287-1016">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="2b287-1016">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="2b287-1017">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="2b287-1017">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="2b287-1018">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="2b287-1018">Empty</span></span> | <span data-ttu-id="2b287-1019">Kolekce certifikátů TLS pro odeslání na požadavky na ověření.</span><span class="sxs-lookup"><span data-stu-id="2b287-1019">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="2b287-1020">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="2b287-1020">Empty</span></span> | <span data-ttu-id="2b287-1021">Kolekce souborů cookie protokolu HTTP, které se mají odeslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="2b287-1021">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="2b287-1022">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="2b287-1022">Empty</span></span> | <span data-ttu-id="2b287-1023">Přihlašovací údaje, které se mají poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="2b287-1023">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="2b287-1024">5 sekund</span><span class="sxs-lookup"><span data-stu-id="2b287-1024">5 seconds</span></span> | <span data-ttu-id="2b287-1025">Jenom objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="2b287-1025">WebSockets only.</span></span> <span data-ttu-id="2b287-1026">Maximální doba, po jejímž uplynutí bude klient čekat na ukončení žádosti o uzavření.</span><span class="sxs-lookup"><span data-stu-id="2b287-1026">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="2b287-1027">Pokud server nepotvrdí zavření v této době, klient se odpojí.</span><span class="sxs-lookup"><span data-stu-id="2b287-1027">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="2b287-1028">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="2b287-1028">Empty</span></span> | <span data-ttu-id="2b287-1029">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="2b287-1029">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="2b287-1030">Delegát, který lze použít ke konfiguraci nebo nahrazení `HttpMessageHandler` používaného k odeslání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="2b287-1030">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="2b287-1031">Nepoužívá se pro připojení pomocí protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="2b287-1031">Not used for WebSocket connections.</span></span> <span data-ttu-id="2b287-1032">Tento delegát musí vracet hodnotu, která není null, a obdrží výchozí hodnotu jako parametr.</span><span class="sxs-lookup"><span data-stu-id="2b287-1032">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="2b287-1033">Buď změňte nastavení u této výchozí hodnoty a vraťte je, nebo vraťte novou `HttpMessageHandler` instanci.</span><span class="sxs-lookup"><span data-stu-id="2b287-1033">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="2b287-1034">**Při nahrazování obslužné rutiny Nezapomeňte zkopírovat nastavení, která chcete zachovat, od poskytnuté obslužné rutiny, jinak konfigurované možnosti (například soubory cookie a záhlaví) nebudou použity pro novou obslužnou rutinu.**</span><span class="sxs-lookup"><span data-stu-id="2b287-1034">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="2b287-1035">Proxy server HTTP, který se má použít při odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="2b287-1035">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="2b287-1036">Nastavte tuto logickou hodnotu pro odeslání výchozích přihlašovacích údajů pro požadavky HTTP a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="2b287-1036">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="2b287-1037">To umožňuje použití ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="2b287-1037">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="2b287-1038">Delegát, který lze použít ke konfiguraci dalších možností protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="2b287-1038">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="2b287-1039">Přijímá instanci [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , která se dá použít ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="2b287-1039">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="2b287-1040">JavaScript</span><span class="sxs-lookup"><span data-stu-id="2b287-1040">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="2b287-1041">Možnost JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="2b287-1041">JavaScript Option</span></span> | <span data-ttu-id="2b287-1042">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-1042">Default Value</span></span> | <span data-ttu-id="2b287-1043">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-1043">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="2b287-1044">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="2b287-1044">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="2b287-1045">Nastavte na `true` protokolování bajtů/znaků zpráv odesílaných a přijímaných klientem.</span><span class="sxs-lookup"><span data-stu-id="2b287-1045">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="2b287-1046">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="2b287-1046">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="2b287-1047">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="2b287-1047">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="2b287-1048">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="2b287-1048">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="2b287-1049">Java</span><span class="sxs-lookup"><span data-stu-id="2b287-1049">Java</span></span>](#tab/java)

| <span data-ttu-id="2b287-1050">Možnost jazyka Java</span><span class="sxs-lookup"><span data-stu-id="2b287-1050">Java Option</span></span> | <span data-ttu-id="2b287-1051">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-1051">Default Value</span></span> | <span data-ttu-id="2b287-1052">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-1052">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="2b287-1053">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="2b287-1053">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="2b287-1054">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="2b287-1054">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="2b287-1055">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="2b287-1055">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="2b287-1056">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="2b287-1056">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="2b287-1057">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="2b287-1057">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="2b287-1058">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="2b287-1058">Empty</span></span> | <span data-ttu-id="2b287-1059">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="2b287-1059">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="2b287-1060">V klientu .NET můžete tyto možnosti upravit pomocí delegáta možností, který je k dispozici pro `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="2b287-1060">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="2b287-1061">V klientovi JavaScriptu lze tyto možnosti poskytnout v objektu jazyka JavaScript, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="2b287-1061">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="2b287-1062">V klientovi Java lze tyto možnosti nakonfigurovat s metodami `HttpHubConnectionBuilder` vrácenými z`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="2b287-1062">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="2b287-1063">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="2b287-1063">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="2b287-1064">Možnosti serializace JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="2b287-1064">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="2b287-1065">ASP.NET Core SignalR podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="2b287-1065">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="2b287-1066">Každý protokol má možnosti konfigurace serializace.</span><span class="sxs-lookup"><span data-stu-id="2b287-1066">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="2b287-1067">Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , kterou lze přidat po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v `Startup.ConfigureServices` metodě.</span><span class="sxs-lookup"><span data-stu-id="2b287-1067">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="2b287-1068">`AddJsonProtocol`Metoda přebírá delegáta, který přijímá `options` objekt.</span><span class="sxs-lookup"><span data-stu-id="2b287-1068">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="2b287-1069">Vlastnost [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) tohoto objektu je `JsonSerializerSettings` objekt JSON.NET, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="2b287-1069">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="2b287-1070">Další informace najdete v [dokumentaci k JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="2b287-1070">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="2b287-1071">Chcete-li například nakonfigurovat serializátor pro použití názvů vlastností "PascalCase" namísto výchozích názvů "camelCase", použijte následující kód v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2b287-1071">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="2b287-1072">V klientovi .NET `AddJsonProtocol` existuje stejná rozšiřující metoda v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="2b287-1072">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="2b287-1073">`Microsoft.Extensions.DependencyInjection`Obor názvů musí být importován, aby bylo možné přeložit metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="2b287-1073">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="2b287-1074">V tuto chvíli není možné konfigurovat serializaci JSON v klientovi jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2b287-1074">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="2b287-1075">Možnosti serializace MessagePack</span><span class="sxs-lookup"><span data-stu-id="2b287-1075">MessagePack serialization options</span></span>

<span data-ttu-id="2b287-1076">Serializaci MessagePack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="2b287-1076">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="2b287-1077">Další podrobnosti najdete [v SignalR tématu MessagePack](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="2b287-1077">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="2b287-1078">V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi v jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="2b287-1078">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="2b287-1079">Konfigurovat možnosti serveru</span><span class="sxs-lookup"><span data-stu-id="2b287-1079">Configure server options</span></span>

<span data-ttu-id="2b287-1080">Následující tabulka popisuje možnosti konfigurace SignalR Center:</span><span class="sxs-lookup"><span data-stu-id="2b287-1080">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="2b287-1081">Možnost</span><span class="sxs-lookup"><span data-stu-id="2b287-1081">Option</span></span> | <span data-ttu-id="2b287-1082">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-1082">Default Value</span></span> | <span data-ttu-id="2b287-1083">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-1083">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="2b287-1084">15 sekund</span><span class="sxs-lookup"><span data-stu-id="2b287-1084">15 seconds</span></span> | <span data-ttu-id="2b287-1085">Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře.</span><span class="sxs-lookup"><span data-stu-id="2b287-1085">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="2b287-1086">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="2b287-1086">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="2b287-1087">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="2b287-1087">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="2b287-1088">15 sekund</span><span class="sxs-lookup"><span data-stu-id="2b287-1088">15 seconds</span></span> | <span data-ttu-id="2b287-1089">Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="2b287-1089">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="2b287-1090">Při změně `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení změňte nastavení v klientovi.</span><span class="sxs-lookup"><span data-stu-id="2b287-1090">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="2b287-1091">Doporučená `ServerTimeout` / `serverTimeoutInMilliseconds` hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="2b287-1091">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="2b287-1092">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="2b287-1092">All installed protocols</span></span> | <span data-ttu-id="2b287-1093">Protokoly podporované tímto rozbočovačem</span><span class="sxs-lookup"><span data-stu-id="2b287-1093">Protocols supported by this hub.</span></span> <span data-ttu-id="2b287-1094">Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly.</span><span class="sxs-lookup"><span data-stu-id="2b287-1094">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="2b287-1095">Pokud se `true` do klientů vrátí podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="2b287-1095">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="2b287-1096">Výchozí hodnota je `false` , protože tyto zprávy o výjimce můžou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="2b287-1096">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="2b287-1097">Možnosti lze nakonfigurovat pro všechna centra tím, že poskytnete možnosti delegáta pro `AddSignalR` volání v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="2b287-1097">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="2b287-1098">Možnosti pro jeden rozbočovač přepíší globální možnosti poskytované v `AddSignalR` a můžou být nakonfigurované pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="2b287-1098">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="2b287-1099">Rozšířené možnosti konfigurace protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="2b287-1099">Advanced HTTP configuration options</span></span>

<span data-ttu-id="2b287-1100">Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="2b287-1100">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="2b287-1101">Tyto možnosti jsou nakonfigurovány předáním delegáta [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="2b287-1101">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    app.UseSignalR((configure) =>
    {
        var desiredTransports =
            HttpTransportType.WebSockets |
            HttpTransportType.LongPolling;

        configure.MapHub<ChatHub>("/chathub", (options) =>
        {
            options.Transports = desiredTransports;
        });
    });
}
```

<span data-ttu-id="2b287-1102">Následující tabulka popisuje možnosti konfigurace SignalR rozšířených možností protokolu HTTP v ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="2b287-1102">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="2b287-1103">Možnost</span><span class="sxs-lookup"><span data-stu-id="2b287-1103">Option</span></span> | <span data-ttu-id="2b287-1104">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-1104">Default Value</span></span> | <span data-ttu-id="2b287-1105">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-1105">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="2b287-1106">32 KB</span><span class="sxs-lookup"><span data-stu-id="2b287-1106">32 KB</span></span> | <span data-ttu-id="2b287-1107">Maximální počet bajtů přijatých od klienta, které jsou vyrovnávací paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="2b287-1107">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="2b287-1108">Zvýšením této hodnoty umožníte serveru přijímat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="2b287-1108">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="2b287-1109">Data se automaticky shromažďují z `Authorize` atributů použitých pro třídu centra.</span><span class="sxs-lookup"><span data-stu-id="2b287-1109">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="2b287-1110">Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru</span><span class="sxs-lookup"><span data-stu-id="2b287-1110">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="2b287-1111">32 KB</span><span class="sxs-lookup"><span data-stu-id="2b287-1111">32 KB</span></span> | <span data-ttu-id="2b287-1112">Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="2b287-1112">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="2b287-1113">Zvýšením této hodnoty umožníte serveru odesílat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="2b287-1113">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="2b287-1114">Všechny přenosy jsou povolené.</span><span class="sxs-lookup"><span data-stu-id="2b287-1114">All Transports are enabled.</span></span> | <span data-ttu-id="2b287-1115">Bitové příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenos, který může klient použít pro připojení.</span><span class="sxs-lookup"><span data-stu-id="2b287-1115">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="2b287-1116">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="2b287-1116">See below.</span></span> | <span data-ttu-id="2b287-1117">Další možnosti specifické pro přenos dlouhého cyklického dotazování.</span><span class="sxs-lookup"><span data-stu-id="2b287-1117">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="2b287-1118">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="2b287-1118">See below.</span></span> | <span data-ttu-id="2b287-1119">Další možnosti specifické pro přenos pomocí protokolu WebSockets</span><span class="sxs-lookup"><span data-stu-id="2b287-1119">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="2b287-1120">Přenos dlouhého cyklického dotazování má další možnosti, které je možné konfigurovat pomocí `LongPolling` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="2b287-1120">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="2b287-1121">Možnost</span><span class="sxs-lookup"><span data-stu-id="2b287-1121">Option</span></span> | <span data-ttu-id="2b287-1122">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-1122">Default Value</span></span> | <span data-ttu-id="2b287-1123">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-1123">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="2b287-1124">90 sekund</span><span class="sxs-lookup"><span data-stu-id="2b287-1124">90 seconds</span></span> | <span data-ttu-id="2b287-1125">Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jedné žádosti o cyklické dotazování.</span><span class="sxs-lookup"><span data-stu-id="2b287-1125">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="2b287-1126">Snížení této hodnoty způsobí, že klient bude vydávat nové požadavky na dotaz častěji.</span><span class="sxs-lookup"><span data-stu-id="2b287-1126">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="2b287-1127">Přenos protokolu WebSocket má další možnosti, které je možné konfigurovat pomocí `WebSockets` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="2b287-1127">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="2b287-1128">Možnost</span><span class="sxs-lookup"><span data-stu-id="2b287-1128">Option</span></span> | <span data-ttu-id="2b287-1129">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-1129">Default Value</span></span> | <span data-ttu-id="2b287-1130">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-1130">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="2b287-1131">5 sekund</span><span class="sxs-lookup"><span data-stu-id="2b287-1131">5 seconds</span></span> | <span data-ttu-id="2b287-1132">Pokud se po ukončení serveru aplikace v tomto časovém intervalu nepovede zavřít, připojení se ukončí.</span><span class="sxs-lookup"><span data-stu-id="2b287-1132">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="2b287-1133">Delegát, který lze použít k nastavení `Sec-WebSocket-Protocol` záhlaví na vlastní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="2b287-1133">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="2b287-1134">Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že se vrátí požadovaná hodnota.</span><span class="sxs-lookup"><span data-stu-id="2b287-1134">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="2b287-1135">Konfigurace možností klienta</span><span class="sxs-lookup"><span data-stu-id="2b287-1135">Configure client options</span></span>

<span data-ttu-id="2b287-1136">Možnosti klienta lze nakonfigurovat pro `HubConnectionBuilder` typ (k dispozici v klientech rozhraní .NET a v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="2b287-1136">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="2b287-1137">Je také k dispozici v klientovi Java, ale `HttpHubConnectionBuilder` podtřídou je to, co obsahuje možnosti konfigurace tvůrce a také na `HubConnection` sebe sama.</span><span class="sxs-lookup"><span data-stu-id="2b287-1137">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="2b287-1138">Konfigurovat protokolování</span><span class="sxs-lookup"><span data-stu-id="2b287-1138">Configure logging</span></span>

<span data-ttu-id="2b287-1139">Protokolování je konfigurováno v klientovi .NET pomocí `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="2b287-1139">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="2b287-1140">Zprostředkovatelé protokolování a filtry je možné zaregistrovat stejným způsobem jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="2b287-1140">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="2b287-1141">Další informace najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="2b287-1141">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="2b287-1142">Aby bylo možné registrovat poskytovatele protokolování, je nutné nainstalovat potřebné balíčky.</span><span class="sxs-lookup"><span data-stu-id="2b287-1142">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="2b287-1143">Úplný seznam najdete v části [předdefinovaná zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="2b287-1143">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="2b287-1144">Chcete-li například povolit protokolování konzoly, nainstalujte `Microsoft.Extensions.Logging.Console` balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="2b287-1144">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="2b287-1145">Zavolejte `AddConsole` metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="2b287-1145">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="2b287-1146">V klientovi jazyka JavaScript existuje podobná `configureLogging` metoda.</span><span class="sxs-lookup"><span data-stu-id="2b287-1146">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="2b287-1147">Zadejte `LogLevel` hodnotu, která určuje minimální úroveň zpráv protokolu, které mají být vyprodukovány.</span><span class="sxs-lookup"><span data-stu-id="2b287-1147">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="2b287-1148">Protokoly se zapisují do okna konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="2b287-1148">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="2b287-1149">Chcete-li protokolování zcela zakázat, zadejte `signalR.LogLevel.None` v `configureLogging` metodě.</span><span class="sxs-lookup"><span data-stu-id="2b287-1149">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="2b287-1150">Další informace o protokolování naleznete v dokumentaci k [ SignalR diagnostice](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="2b287-1150">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="2b287-1151">SignalRKlient Java používá k protokolování knihovnu [SLF4J](https://www.slf4j.org/) .</span><span class="sxs-lookup"><span data-stu-id="2b287-1151">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="2b287-1152">Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí.</span><span class="sxs-lookup"><span data-stu-id="2b287-1152">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="2b287-1153">Následující fragment kódu ukazuje, jak používat `java.util.logging` s SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="2b287-1153">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="2b287-1154">Pokud ve svých závislostech nenakonfigurujete protokolování, SLF4J načte výchozí protokolovací nástroj No-operation s následující zprávou upozornění:</span><span class="sxs-lookup"><span data-stu-id="2b287-1154">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="2b287-1155">Tuto možnost lze bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="2b287-1155">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="2b287-1156">Konfigurace povolených přenosů</span><span class="sxs-lookup"><span data-stu-id="2b287-1156">Configure allowed transports</span></span>

<span data-ttu-id="2b287-1157">Přenosy používané nástrojem SignalR lze konfigurovat ve `WithUrl` volání ( `withUrl` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="2b287-1157">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="2b287-1158">Bitové nebo hodnoty `HttpTransportType` lze použít k omezení klienta na používání pouze zadaných přenosů.</span><span class="sxs-lookup"><span data-stu-id="2b287-1158">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="2b287-1159">Ve výchozím nastavení jsou povolené všechny přenosy.</span><span class="sxs-lookup"><span data-stu-id="2b287-1159">All transports are enabled by default.</span></span>

<span data-ttu-id="2b287-1160">Pokud třeba chcete zakázat přenos událostí odeslaných serverem, ale povolit WebSockets a dlouhá připojení s dotazem:</span><span class="sxs-lookup"><span data-stu-id="2b287-1160">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="2b287-1161">V klientu jazyka JavaScript jsou přenosy konfigurovány nastavením `transport` pole v objektu Options, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="2b287-1161">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="2b287-1162">Konfigurace ověřování nosiče</span><span class="sxs-lookup"><span data-stu-id="2b287-1162">Configure bearer authentication</span></span>

<span data-ttu-id="2b287-1163">K poskytnutí ověřovacích dat spolu s SignalR požadavky použijte `AccessTokenProvider` možnost ( `accessTokenFactory` v jazyce JavaScript) k určení funkce, která vrací požadovaný přístupový token.</span><span class="sxs-lookup"><span data-stu-id="2b287-1163">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="2b287-1164">V klientovi .NET se tento přístupový token předává jako token ověřování HTTP "Bearer" (s použitím `Authorization` hlavičky s typem `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="2b287-1164">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="2b287-1165">V klientu jazyka JavaScript se přístupový token používá jako nosný token, **s výjimkou** případů, kdy rozhraní API prohlížeče omezuje možnost použít hlavičky (konkrétně v požadavcích na události odeslané serverem a objekty WebSockets).</span><span class="sxs-lookup"><span data-stu-id="2b287-1165">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="2b287-1166">V těchto případech je přístupový token k dispozici jako hodnota řetězce dotazu `access_token` .</span><span class="sxs-lookup"><span data-stu-id="2b287-1166">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="2b287-1167">V klientu .NET `AccessTokenProvider` lze možnost zadat pomocí delegáta možnosti v `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="2b287-1167">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="2b287-1168">V klientovi JavaScriptu je přístupový token nakonfigurovaný nastavením `accessTokenFactory` pole v objektu Options v `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="2b287-1168">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        accessTokenFactory: () => {
            // Get and return the access token.
            // This function can return a JavaScript Promise if asynchronous
            // logic is required to retrieve the access token.
        }
    })
    .build();
```

<span data-ttu-id="2b287-1169">V SignalR klientovi Java můžete nakonfigurovat nosný token, který se bude používat pro ověřování, a to poskytnutím továrny přístupového tokenu [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="2b287-1169">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="2b287-1170">Pomocí [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) můžete zadat [RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="2b287-1170">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="2b287-1171">Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.</span><span class="sxs-lookup"><span data-stu-id="2b287-1171">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="2b287-1172">Konfigurace možností timeout a Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="2b287-1172">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="2b287-1173">Další možnosti konfigurace časového limitu a chování funkce Keep-Alive jsou k dispozici na `HubConnection` samotném objektu:</span><span class="sxs-lookup"><span data-stu-id="2b287-1173">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="2b287-1174">.NET</span><span class="sxs-lookup"><span data-stu-id="2b287-1174">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="2b287-1175">Možnost</span><span class="sxs-lookup"><span data-stu-id="2b287-1175">Option</span></span> | <span data-ttu-id="2b287-1176">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-1176">Default value</span></span> | <span data-ttu-id="2b287-1177">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-1177">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="2b287-1178">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="2b287-1178">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="2b287-1179">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="2b287-1179">Timeout for server activity.</span></span> <span data-ttu-id="2b287-1180">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí `Closed` událost ( `onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="2b287-1180">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="2b287-1181">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="2b287-1181">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="2b287-1182">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="2b287-1182">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="2b287-1183">15 sekund</span><span class="sxs-lookup"><span data-stu-id="2b287-1183">15 seconds</span></span> | <span data-ttu-id="2b287-1184">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="2b287-1184">Timeout for initial server handshake.</span></span> <span data-ttu-id="2b287-1185">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a spustí `Closed` událost ( `onclose` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="2b287-1185">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="2b287-1186">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="2b287-1186">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="2b287-1187">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="2b287-1187">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="2b287-1188">V klientovi .NET jsou hodnoty časového limitu zadány jako `TimeSpan` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="2b287-1188">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="2b287-1189">JavaScript</span><span class="sxs-lookup"><span data-stu-id="2b287-1189">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="2b287-1190">Možnost</span><span class="sxs-lookup"><span data-stu-id="2b287-1190">Option</span></span> | <span data-ttu-id="2b287-1191">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-1191">Default value</span></span> | <span data-ttu-id="2b287-1192">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-1192">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="2b287-1193">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="2b287-1193">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="2b287-1194">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="2b287-1194">Timeout for server activity.</span></span> <span data-ttu-id="2b287-1195">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onclose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="2b287-1195">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="2b287-1196">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="2b287-1196">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="2b287-1197">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="2b287-1197">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="2b287-1198">Java</span><span class="sxs-lookup"><span data-stu-id="2b287-1198">Java</span></span>](#tab/java)

| <span data-ttu-id="2b287-1199">Možnost</span><span class="sxs-lookup"><span data-stu-id="2b287-1199">Option</span></span> | <span data-ttu-id="2b287-1200">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-1200">Default value</span></span> | <span data-ttu-id="2b287-1201">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-1201">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="2b287-1202">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="2b287-1202">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="2b287-1203">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="2b287-1203">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="2b287-1204">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="2b287-1204">Timeout for server activity.</span></span> <span data-ttu-id="2b287-1205">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onClose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="2b287-1205">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="2b287-1206">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="2b287-1206">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="2b287-1207">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit k příkazům k zadání času.</span><span class="sxs-lookup"><span data-stu-id="2b287-1207">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="2b287-1208">15 sekund</span><span class="sxs-lookup"><span data-stu-id="2b287-1208">15 seconds</span></span> | <span data-ttu-id="2b287-1209">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="2b287-1209">Timeout for initial server handshake.</span></span> <span data-ttu-id="2b287-1210">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje `onClose` událost.</span><span class="sxs-lookup"><span data-stu-id="2b287-1210">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="2b287-1211">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="2b287-1211">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="2b287-1212">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="2b287-1212">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="2b287-1213">Konfigurace dalších možností</span><span class="sxs-lookup"><span data-stu-id="2b287-1213">Configure additional options</span></span>

<span data-ttu-id="2b287-1214">Další možnosti lze konfigurovat v `WithUrl` `withUrl` metodě (v jazyce JavaScript) na `HubConnectionBuilder` různých konfiguračních rozhraních API v `HttpHubConnectionBuilder` klientovi Java:</span><span class="sxs-lookup"><span data-stu-id="2b287-1214">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="2b287-1215">.NET</span><span class="sxs-lookup"><span data-stu-id="2b287-1215">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="2b287-1216">Možnost .NET</span><span class="sxs-lookup"><span data-stu-id="2b287-1216">.NET Option</span></span> |  <span data-ttu-id="2b287-1217">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-1217">Default value</span></span> | <span data-ttu-id="2b287-1218">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-1218">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="2b287-1219">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="2b287-1219">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="2b287-1220">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="2b287-1220">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="2b287-1221">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="2b287-1221">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="2b287-1222">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="2b287-1222">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="2b287-1223">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="2b287-1223">Empty</span></span> | <span data-ttu-id="2b287-1224">Kolekce certifikátů TLS pro odeslání na požadavky na ověření.</span><span class="sxs-lookup"><span data-stu-id="2b287-1224">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="2b287-1225">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="2b287-1225">Empty</span></span> | <span data-ttu-id="2b287-1226">Kolekce souborů cookie protokolu HTTP, které se mají odeslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="2b287-1226">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="2b287-1227">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="2b287-1227">Empty</span></span> | <span data-ttu-id="2b287-1228">Přihlašovací údaje, které se mají poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="2b287-1228">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="2b287-1229">5 sekund</span><span class="sxs-lookup"><span data-stu-id="2b287-1229">5 seconds</span></span> | <span data-ttu-id="2b287-1230">Jenom objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="2b287-1230">WebSockets only.</span></span> <span data-ttu-id="2b287-1231">Maximální doba, po jejímž uplynutí bude klient čekat na ukončení žádosti o uzavření.</span><span class="sxs-lookup"><span data-stu-id="2b287-1231">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="2b287-1232">Pokud server nepotvrdí zavření v této době, klient se odpojí.</span><span class="sxs-lookup"><span data-stu-id="2b287-1232">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="2b287-1233">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="2b287-1233">Empty</span></span> | <span data-ttu-id="2b287-1234">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="2b287-1234">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="2b287-1235">Delegát, který lze použít ke konfiguraci nebo nahrazení `HttpMessageHandler` používaného k odeslání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="2b287-1235">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="2b287-1236">Nepoužívá se pro připojení pomocí protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="2b287-1236">Not used for WebSocket connections.</span></span> <span data-ttu-id="2b287-1237">Tento delegát musí vracet hodnotu, která není null, a obdrží výchozí hodnotu jako parametr.</span><span class="sxs-lookup"><span data-stu-id="2b287-1237">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="2b287-1238">Buď změňte nastavení u této výchozí hodnoty a vraťte je, nebo vraťte novou `HttpMessageHandler` instanci.</span><span class="sxs-lookup"><span data-stu-id="2b287-1238">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="2b287-1239">**Při nahrazování obslužné rutiny Nezapomeňte zkopírovat nastavení, která chcete zachovat, od poskytnuté obslužné rutiny, jinak konfigurované možnosti (například soubory cookie a záhlaví) nebudou použity pro novou obslužnou rutinu.**</span><span class="sxs-lookup"><span data-stu-id="2b287-1239">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="2b287-1240">Proxy server HTTP, který se má použít při odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="2b287-1240">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="2b287-1241">Nastavte tuto logickou hodnotu pro odeslání výchozích přihlašovacích údajů pro požadavky HTTP a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="2b287-1241">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="2b287-1242">To umožňuje použití ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="2b287-1242">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="2b287-1243">Delegát, který lze použít ke konfiguraci dalších možností protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="2b287-1243">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="2b287-1244">Přijímá instanci [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , která se dá použít ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="2b287-1244">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="2b287-1245">JavaScript</span><span class="sxs-lookup"><span data-stu-id="2b287-1245">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="2b287-1246">Možnost JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="2b287-1246">JavaScript Option</span></span> | <span data-ttu-id="2b287-1247">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-1247">Default Value</span></span> | <span data-ttu-id="2b287-1248">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-1248">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="2b287-1249">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="2b287-1249">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="2b287-1250">Nastavte na `true` protokolování bajtů/znaků zpráv odesílaných a přijímaných klientem.</span><span class="sxs-lookup"><span data-stu-id="2b287-1250">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="2b287-1251">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="2b287-1251">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="2b287-1252">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="2b287-1252">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="2b287-1253">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="2b287-1253">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="2b287-1254">Java</span><span class="sxs-lookup"><span data-stu-id="2b287-1254">Java</span></span>](#tab/java)

| <span data-ttu-id="2b287-1255">Možnost jazyka Java</span><span class="sxs-lookup"><span data-stu-id="2b287-1255">Java Option</span></span> | <span data-ttu-id="2b287-1256">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="2b287-1256">Default Value</span></span> | <span data-ttu-id="2b287-1257">Popis</span><span class="sxs-lookup"><span data-stu-id="2b287-1257">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="2b287-1258">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="2b287-1258">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="2b287-1259">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="2b287-1259">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="2b287-1260">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="2b287-1260">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="2b287-1261">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="2b287-1261">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="2b287-1262">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="2b287-1262">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="2b287-1263">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="2b287-1263">Empty</span></span> | <span data-ttu-id="2b287-1264">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="2b287-1264">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="2b287-1265">V klientu .NET můžete tyto možnosti upravit pomocí delegáta možností, který je k dispozici pro `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="2b287-1265">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="2b287-1266">V klientovi JavaScriptu lze tyto možnosti poskytnout v objektu jazyka JavaScript, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="2b287-1266">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="2b287-1267">V klientovi Java lze tyto možnosti nakonfigurovat s metodami `HttpHubConnectionBuilder` vrácenými z`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="2b287-1267">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="2b287-1268">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="2b287-1268">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
