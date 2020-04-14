---
title: ASP.NET SignalR konfigurace jádra
author: bradygaster
description: Přečtěte si, SignalR jak nakonfigurovat aplikace ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 9f98a9c6371ef7e38586b0d728c670b0eecb8f6e
ms.sourcegitcommit: fbdb8b9ab5a52656384b117ff6e7c92ae070813c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81228137"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="72ad9-103">ASP.NET konfigurace Core SignalR</span><span class="sxs-lookup"><span data-stu-id="72ad9-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="72ad9-104">Možnosti serializace balíčku JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="72ad9-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="72ad9-105">ASP.NET Core SignalR podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="72ad9-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="72ad9-106">Každý protokol má možnosti konfigurace serializace.</span><span class="sxs-lookup"><span data-stu-id="72ad9-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="72ad9-107">Serializaci JSON lze na serveru nakonfigurovat pomocí metody rozšíření [AddJsonProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)</span><span class="sxs-lookup"><span data-stu-id="72ad9-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="72ad9-108">`AddJsonProtocol`lze přidat po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="72ad9-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="72ad9-109">Metoda `AddJsonProtocol` trvá delegáta, který `options` obdrží objekt.</span><span class="sxs-lookup"><span data-stu-id="72ad9-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="72ad9-110">[Vlastnost PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) u tohoto `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objektu je objekt, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="72ad9-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="72ad9-111">Další informace naleznete v [dokumentaci System.Text.Json](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="72ad9-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="72ad9-112">Chcete-li například nakonfigurovat serializátor tak, aby neměnil názvy vlastností, namísto výchozích názvů camelCase použijte následující kód v aplikaci `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="72ad9-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="72ad9-113">V klientovi .NET `AddJsonProtocol` existuje stejná metoda rozšíření v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="72ad9-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="72ad9-114">Obor `Microsoft.Extensions.DependencyInjection` názvů musí být importován, aby se vyřešila metoda rozšíření:</span><span class="sxs-lookup"><span data-stu-id="72ad9-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="72ad9-115">V tuto chvíli není možné konfigurovat serializaci JSON v klientovi JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="72ad9-116">Přejít na Newtonsoft.Json</span><span class="sxs-lookup"><span data-stu-id="72ad9-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="72ad9-117">Pokud potřebujete `Newtonsoft.Json` funkce, které nejsou `System.Text.Json`podporovány v , viz [Přepnout na Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="72ad9-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="72ad9-118">Možnosti serializace messagepacku</span><span class="sxs-lookup"><span data-stu-id="72ad9-118">MessagePack serialization options</span></span>

<span data-ttu-id="72ad9-119">Serializace messagepack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)</span><span class="sxs-lookup"><span data-stu-id="72ad9-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="72ad9-120">Další podrobnosti najdete [v tématu MessagePack v SignalR.](xref:signalr/messagepackhubprotocol)</span><span class="sxs-lookup"><span data-stu-id="72ad9-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="72ad9-121">V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="72ad9-122">Konfigurace možností serveru</span><span class="sxs-lookup"><span data-stu-id="72ad9-122">Configure server options</span></span>

<span data-ttu-id="72ad9-123">Následující tabulka popisuje možnosti konfigurace rozbočovačů SignalR:</span><span class="sxs-lookup"><span data-stu-id="72ad9-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="72ad9-124">Možnost</span><span class="sxs-lookup"><span data-stu-id="72ad9-124">Option</span></span> | <span data-ttu-id="72ad9-125">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="72ad9-125">Default Value</span></span> | <span data-ttu-id="72ad9-126">Popis</span><span class="sxs-lookup"><span data-stu-id="72ad9-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="72ad9-127">30 sekund</span><span class="sxs-lookup"><span data-stu-id="72ad9-127">30 seconds</span></span> | <span data-ttu-id="72ad9-128">Server bude považovat klientodpojený, pokud neobdržel zprávu (včetně keep-alive) v tomto intervalu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="72ad9-129">Může trvat déle než tento časový interval pro klienta skutečně označeny odpojen, z důvodu, jak je implementována.</span><span class="sxs-lookup"><span data-stu-id="72ad9-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="72ad9-130">Doporučená hodnota je `KeepAliveInterval` dvojnásobek hodnoty.</span><span class="sxs-lookup"><span data-stu-id="72ad9-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="72ad9-131">15 sekund</span><span class="sxs-lookup"><span data-stu-id="72ad9-131">15 seconds</span></span> | <span data-ttu-id="72ad9-132">Pokud klient neodešle počáteční zprávu handshake v tomto časovém intervalu, připojení je ukončeno.</span><span class="sxs-lookup"><span data-stu-id="72ad9-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="72ad9-133">Toto je rozšířené nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake z důvodu závažné latence sítě.</span><span class="sxs-lookup"><span data-stu-id="72ad9-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="72ad9-134">Další podrobnosti o procesu handshake naleznete v tématu [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="72ad9-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="72ad9-135">15 sekund</span><span class="sxs-lookup"><span data-stu-id="72ad9-135">15 seconds</span></span> | <span data-ttu-id="72ad9-136">Pokud server neodeslal zprávu v tomto intervalu, je automaticky odeslána zpráva ping, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="72ad9-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="72ad9-137">Při `KeepAliveInterval`změně změňte `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení klienta.</span><span class="sxs-lookup"><span data-stu-id="72ad9-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="72ad9-138">Doporučená `ServerTimeout` / `serverTimeoutInMilliseconds` hodnota je `KeepAliveInterval` dvojnásobek hodnoty.</span><span class="sxs-lookup"><span data-stu-id="72ad9-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="72ad9-139">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="72ad9-139">All installed protocols</span></span> | <span data-ttu-id="72ad9-140">Protokoly podporované tímto centrem.</span><span class="sxs-lookup"><span data-stu-id="72ad9-140">Protocols supported by this hub.</span></span> <span data-ttu-id="72ad9-141">Ve výchozím nastavení jsou povoleny všechny protokoly registrované na serveru, ale protokoly mohou být odebrány z tohoto seznamu zakázat konkrétní protokoly pro jednotlivé rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="72ad9-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="72ad9-142">Pokud `true`, podrobné zprávy o výjimce jsou vráceny klientům při vyvolání výjimky v Hub metody.</span><span class="sxs-lookup"><span data-stu-id="72ad9-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="72ad9-143">Výchozí hodnota `false`je , protože tyto zprávy o výjimce mohou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="72ad9-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="72ad9-144">Maximální počet položek, které mohou být uloženy do vyrovnávací paměti pro datové proudy pro odesílání klienta.</span><span class="sxs-lookup"><span data-stu-id="72ad9-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="72ad9-145">Pokud je dosaženo tohoto limitu, zpracování vyvolání je blokován, dokud server zpracovává položky datového proudu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="72ad9-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="72ad9-146">32 KB</span></span> | <span data-ttu-id="72ad9-147">Maximální velikost jedné příchozí zprávy rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="72ad9-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="72ad9-148">Možnosti lze nakonfigurovat pro všechny rozbočovače `Startup.ConfigureServices`poskytnutím možnosti delegáta volání v aplikaci `AddSignalR` .</span><span class="sxs-lookup"><span data-stu-id="72ad9-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="72ad9-149">Možnosti pro jeden rozbočovač přepíší globální možnosti uvedené v `AddSignalR` aplikaci a lze je konfigurovat pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span><span class="sxs-lookup"><span data-stu-id="72ad9-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="72ad9-150">Rozšířené možnosti konfigurace protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="72ad9-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="72ad9-151">Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="72ad9-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="72ad9-152">Tyto možnosti jsou konfigurovány předáním delegáta [maphub\<t>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="72ad9-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="72ad9-153">Následující tabulka popisuje možnosti konfigurace ASP.NET pokročilých možností protokolu HTTP nástroje Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="72ad9-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="72ad9-154">Možnost</span><span class="sxs-lookup"><span data-stu-id="72ad9-154">Option</span></span> | <span data-ttu-id="72ad9-155">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="72ad9-155">Default Value</span></span> | <span data-ttu-id="72ad9-156">Popis</span><span class="sxs-lookup"><span data-stu-id="72ad9-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="72ad9-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="72ad9-157">32 KB</span></span> | <span data-ttu-id="72ad9-158">Maximální počet bajtů přijatých od klienta, které server vyrovnávací paměti před použitím protitlaku.</span><span class="sxs-lookup"><span data-stu-id="72ad9-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="72ad9-159">Zvýšení této hodnoty umožňuje serveru přijímat větší zprávy rychleji bez použití protitlaku, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="72ad9-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="72ad9-160">Data automaticky získaná z `Authorize` atributů použitých pro třídu Hub.</span><span class="sxs-lookup"><span data-stu-id="72ad9-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="72ad9-161">Seznam objektů [IAuthorizeData,](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) které slouží k určení, zda je klient oprávněn připojit k rozbočovači.</span><span class="sxs-lookup"><span data-stu-id="72ad9-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="72ad9-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="72ad9-162">32 KB</span></span> | <span data-ttu-id="72ad9-163">Maximální počet bajtů odeslaných aplikací, které server vyrovnávací paměti před sledováním protitlaku.</span><span class="sxs-lookup"><span data-stu-id="72ad9-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="72ad9-164">Zvýšení této hodnoty umožňuje serveru do vyrovnávací paměti větší zprávy rychleji bez čekání na protitlak, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="72ad9-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="72ad9-165">Všechny přenosy jsou povoleny.</span><span class="sxs-lookup"><span data-stu-id="72ad9-165">All Transports are enabled.</span></span> | <span data-ttu-id="72ad9-166">Bit příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenosy klienta můžete použít k připojení.</span><span class="sxs-lookup"><span data-stu-id="72ad9-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="72ad9-167">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="72ad9-167">See below.</span></span> | <span data-ttu-id="72ad9-168">Další možnosti specifické pro přenos dlouhé dotazování.</span><span class="sxs-lookup"><span data-stu-id="72ad9-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="72ad9-169">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="72ad9-169">See below.</span></span> | <span data-ttu-id="72ad9-170">Další možnosti specifické pro přenos WebSockets.</span><span class="sxs-lookup"><span data-stu-id="72ad9-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="72ad9-171">0</span><span class="sxs-lookup"><span data-stu-id="72ad9-171">0</span></span> | <span data-ttu-id="72ad9-172">Zadejte minimální verzi protokolu negotiate.</span><span class="sxs-lookup"><span data-stu-id="72ad9-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="72ad9-173">Používá se k omezení klientů na novější verze.</span><span class="sxs-lookup"><span data-stu-id="72ad9-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="72ad9-174">Přenos dlouhé dotazování má další možnosti, které lze nakonfigurovat pomocí vlastnosti: `LongPolling`</span><span class="sxs-lookup"><span data-stu-id="72ad9-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="72ad9-175">Možnost</span><span class="sxs-lookup"><span data-stu-id="72ad9-175">Option</span></span> | <span data-ttu-id="72ad9-176">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="72ad9-176">Default Value</span></span> | <span data-ttu-id="72ad9-177">Popis</span><span class="sxs-lookup"><span data-stu-id="72ad9-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="72ad9-178">90 sekund</span><span class="sxs-lookup"><span data-stu-id="72ad9-178">90 seconds</span></span> | <span data-ttu-id="72ad9-179">Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jednoho požadavku na dotazování.</span><span class="sxs-lookup"><span data-stu-id="72ad9-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="72ad9-180">Snížení této hodnoty způsobí, že klient vydávat nové požadavky na dotazování častěji.</span><span class="sxs-lookup"><span data-stu-id="72ad9-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="72ad9-181">Přenos WebSocket má další možnosti, `WebSockets` které lze nakonfigurovat pomocí vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="72ad9-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="72ad9-182">Možnost</span><span class="sxs-lookup"><span data-stu-id="72ad9-182">Option</span></span> | <span data-ttu-id="72ad9-183">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="72ad9-183">Default Value</span></span> | <span data-ttu-id="72ad9-184">Popis</span><span class="sxs-lookup"><span data-stu-id="72ad9-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="72ad9-185">5 sekund</span><span class="sxs-lookup"><span data-stu-id="72ad9-185">5 seconds</span></span> | <span data-ttu-id="72ad9-186">Po ukončení serveru, pokud se klientovi nepodaří zavřít v tomto časovém intervalu, je připojení ukončeno.</span><span class="sxs-lookup"><span data-stu-id="72ad9-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="72ad9-187">Delegát, který lze použít `Sec-WebSocket-Protocol` k nastavení záhlaví na vlastní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="72ad9-188">Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že vrátí požadovanou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="72ad9-189">Konfigurace možností klienta</span><span class="sxs-lookup"><span data-stu-id="72ad9-189">Configure client options</span></span>

<span data-ttu-id="72ad9-190">Možnosti klienta lze `HubConnectionBuilder` konfigurovat na typu (k dispozici v klientech .NET a JavaScript).</span><span class="sxs-lookup"><span data-stu-id="72ad9-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="72ad9-191">Je také k dispozici v klientovi `HttpHubConnectionBuilder` Java, ale podtřída je to, co `HubConnection` obsahuje možnosti konfigurace tvůrce, stejně jako na sobě.</span><span class="sxs-lookup"><span data-stu-id="72ad9-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="72ad9-192">Konfigurace protokolování</span><span class="sxs-lookup"><span data-stu-id="72ad9-192">Configure logging</span></span>

<span data-ttu-id="72ad9-193">Protokolování je konfigurováno v `ConfigureLogging` klientovi .NET pomocí metody.</span><span class="sxs-lookup"><span data-stu-id="72ad9-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="72ad9-194">Zprostředkovatelé protokolování a filtry mohou být registrovány stejným způsobem jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="72ad9-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="72ad9-195">Další informace naleznete v dokumentaci [protokolování ASP.NET jádra.](xref:fundamentals/logging/index)</span><span class="sxs-lookup"><span data-stu-id="72ad9-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="72ad9-196">Chcete-li zaregistrovat zprostředkovatele protokolování, je nutné nainstalovat potřebné balíčky.</span><span class="sxs-lookup"><span data-stu-id="72ad9-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="72ad9-197">Úplný seznam najdete v části [Vestavěné zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentech.</span><span class="sxs-lookup"><span data-stu-id="72ad9-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="72ad9-198">Chcete-li například povolit `Microsoft.Extensions.Logging.Console` protokolování konzoly, nainstalujte balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="72ad9-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="72ad9-199">Volání `AddConsole` metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="72ad9-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="72ad9-200">V klientovi JavaScript `configureLogging` existuje podobná metoda.</span><span class="sxs-lookup"><span data-stu-id="72ad9-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="72ad9-201">Zadejte `LogLevel` hodnotu označující minimální úroveň zpráv protokolu k vytvoření.</span><span class="sxs-lookup"><span data-stu-id="72ad9-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="72ad9-202">Protokoly jsou zapsány do okna konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="72ad9-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="72ad9-203">Namísto `LogLevel` hodnoty můžete také zadat `string` hodnotu představující název na úrovni protokolu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="72ad9-204">To je užitečné při konfiguraci protokolování SignalR v prostředích, kde nemáte přístup k `LogLevel` konstantám.</span><span class="sxs-lookup"><span data-stu-id="72ad9-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="72ad9-205">V následující tabulce jsou uvedeny dostupné úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-205">The following table lists the available log levels.</span></span> <span data-ttu-id="72ad9-206">Hodnota, kterou `configureLogging` zadáte pro nastavení **minimální** úroveň protokolu, která bude zaznamenána.</span><span class="sxs-lookup"><span data-stu-id="72ad9-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="72ad9-207">Zprávy zaznamenané na této úrovni **nebo úrovně uvedené za ní v tabulce**budou zaznamenány.</span><span class="sxs-lookup"><span data-stu-id="72ad9-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="72ad9-208">Řetězec</span><span class="sxs-lookup"><span data-stu-id="72ad9-208">String</span></span>                      | <span data-ttu-id="72ad9-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="72ad9-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="72ad9-210">`info`**nebo**`information`</span><span class="sxs-lookup"><span data-stu-id="72ad9-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="72ad9-211">`warn`**nebo**`warning`</span><span class="sxs-lookup"><span data-stu-id="72ad9-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="72ad9-212">Chcete-li protokolování `signalR.LogLevel.None` zcela `configureLogging` zakázat, zadejte v metodě.</span><span class="sxs-lookup"><span data-stu-id="72ad9-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="72ad9-213">Další informace o protokolování naleznete v [dokumentaci k diagnostice signalr .](xref:signalr/diagnostics)</span><span class="sxs-lookup"><span data-stu-id="72ad9-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="72ad9-214">Klient Java SignalR používá pro protokolování knihovnu [SLF4J.](https://www.slf4j.org/)</span><span class="sxs-lookup"><span data-stu-id="72ad9-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="72ad9-215">Jedná se o rozhraní API pro protokolování vysoké úrovně, které umožňuje uživatelům knihovny zvolit vlastní implementaci protokolování tím, že přivedou konkrétní závislost protokolování.</span><span class="sxs-lookup"><span data-stu-id="72ad9-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="72ad9-216">Následující fragment kódu ukazuje, jak `java.util.logging` se používat s klientem Java SignalR.</span><span class="sxs-lookup"><span data-stu-id="72ad9-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="72ad9-217">Pokud nenakonfigurujete protokolování v závislostech, SLF4J načte výchozí protokolovací nástroj bez operace s následující varovnou zprávou:</span><span class="sxs-lookup"><span data-stu-id="72ad9-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="72ad9-218">To lze bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="72ad9-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="72ad9-219">Konfigurace povolených přenosů</span><span class="sxs-lookup"><span data-stu-id="72ad9-219">Configure allowed transports</span></span>

<span data-ttu-id="72ad9-220">Přenosy používané SignalR lze konfigurovat `WithUrl` ve`withUrl` volání (v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="72ad9-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="72ad9-221">Bitové nebo hodnoty `HttpTransportType` lze omezit klienta používat pouze zadané přenosy.</span><span class="sxs-lookup"><span data-stu-id="72ad9-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="72ad9-222">Všechny přenosy jsou ve výchozím nastavení povoleny.</span><span class="sxs-lookup"><span data-stu-id="72ad9-222">All transports are enabled by default.</span></span>

<span data-ttu-id="72ad9-223">Chcete-li například zakázat přenos událostí odeslaných serverem, ale povolit připojení WebSockets a Long Polling:</span><span class="sxs-lookup"><span data-stu-id="72ad9-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="72ad9-224">V klientovi JavaScript jsou přenosy `transport` konfigurovány nastavením pole `withUrl`na objektu options, který je k dispozici :</span><span class="sxs-lookup"><span data-stu-id="72ad9-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="72ad9-225">V této verzi webových zásuvek klienta Java je pouze dostupný přenos.</span><span class="sxs-lookup"><span data-stu-id="72ad9-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="72ad9-226">V klientovi Java je přenos `withTransport` vybrán s `HttpHubConnectionBuilder`metodou na .</span><span class="sxs-lookup"><span data-stu-id="72ad9-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="72ad9-227">Klient Java ve výchozím nastavení používá přenos WebSockets.</span><span class="sxs-lookup"><span data-stu-id="72ad9-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="72ad9-228">Klient Java SignalR ještě nepodporuje záložní přenos.</span><span class="sxs-lookup"><span data-stu-id="72ad9-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="72ad9-229">Konfigurace ověřování nosiče</span><span class="sxs-lookup"><span data-stu-id="72ad9-229">Configure bearer authentication</span></span>

<span data-ttu-id="72ad9-230">Chcete-li poskytnout ověřovací data spolu s `AccessTokenProvider` požadavky`accessTokenFactory` SignalR, použijte možnost ( v Jazyce JavaScript a určete funkci, která vrátí požadovaný přístupový token.</span><span class="sxs-lookup"><span data-stu-id="72ad9-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="72ad9-231">V klientovi .NET je tento přístupový token předán jako http "Bearer Authentication" token (Pomocí `Authorization` hlavičky `Bearer`s typem ).</span><span class="sxs-lookup"><span data-stu-id="72ad9-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="72ad9-232">V klientovi JavaScript se přístupový token používá jako token Nosiče, **s výjimkou** několika případů, kdy rozhraní API prohlížeče omezují možnost použití záhlaví (konkrétně v požadavcích Události odeslané serverem a WebSockets).</span><span class="sxs-lookup"><span data-stu-id="72ad9-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="72ad9-233">V těchto případech je přístupový token k `access_token`dispozici jako hodnota řetězce dotazu .</span><span class="sxs-lookup"><span data-stu-id="72ad9-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="72ad9-234">V klientovi .NET `AccessTokenProvider` lze tuto možnost zadat `WithUrl`pomocí možností delegáta v aplikaci :</span><span class="sxs-lookup"><span data-stu-id="72ad9-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="72ad9-235">V klientovi JavaScript je přístupový token `accessTokenFactory` konfigurován nastavením `withUrl`pole na objektu options v aplikaci :</span><span class="sxs-lookup"><span data-stu-id="72ad9-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="72ad9-236">V klientovi SignalR Java můžete nakonfigurovat token nosiče pro ověřování poskytnutím objektu [HttpHubConnectionBuilder.](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)</span><span class="sxs-lookup"><span data-stu-id="72ad9-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="72ad9-237">Použijte [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) poskytnout [RxJava](https://github.com/ReactiveX/RxJava) [single\<string>](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="72ad9-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="72ad9-238">S [volánísingle.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), můžete napsat logiku k vytvoření přístupové tokeny pro vašeho klienta.</span><span class="sxs-lookup"><span data-stu-id="72ad9-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="72ad9-239">Konfigurace možností časového limitu a udržování v životě</span><span class="sxs-lookup"><span data-stu-id="72ad9-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="72ad9-240">Další možnosti pro konfiguraci časového limitu a `HubConnection` chování udržování naživu jsou k dispozici na samotném objektu:</span><span class="sxs-lookup"><span data-stu-id="72ad9-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="72ad9-241">.NET</span><span class="sxs-lookup"><span data-stu-id="72ad9-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="72ad9-242">Možnost</span><span class="sxs-lookup"><span data-stu-id="72ad9-242">Option</span></span> | <span data-ttu-id="72ad9-243">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="72ad9-243">Default value</span></span> | <span data-ttu-id="72ad9-244">Popis</span><span class="sxs-lookup"><span data-stu-id="72ad9-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="72ad9-245">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="72ad9-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="72ad9-246">Časový čas pro aktivitu serveru.</span><span class="sxs-lookup"><span data-stu-id="72ad9-246">Timeout for server activity.</span></span> <span data-ttu-id="72ad9-247">Pokud server v tomto intervalu neodeslal zprávu, klient uváží server `Closed` odpojený`onclose` a spustí událost (v Jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="72ad9-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="72ad9-248">Tato hodnota musí být dostatečně velká, aby zpráva ping byla odeslána ze serveru **a** přijata klientem v časovém intervalu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="72ad9-249">Doporučená hodnota je číslo alespoň dvojnásobek `KeepAliveInterval` hodnoty serveru povolit čas pro příkaz ping k doručení.</span><span class="sxs-lookup"><span data-stu-id="72ad9-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="72ad9-250">15 sekund</span><span class="sxs-lookup"><span data-stu-id="72ad9-250">15 seconds</span></span> | <span data-ttu-id="72ad9-251">Časový limit pro počáteční server handshake.</span><span class="sxs-lookup"><span data-stu-id="72ad9-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="72ad9-252">Pokud server neodešle odpověď handshake v tomto intervalu, klient zruší `Closed` handshake`onclose` a spustí událost (v Jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="72ad9-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="72ad9-253">Toto je rozšířené nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake z důvodu závažné latence sítě.</span><span class="sxs-lookup"><span data-stu-id="72ad9-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="72ad9-254">Další podrobnosti o procesu handshake naleznete v tématu [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="72ad9-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="72ad9-255">15 sekund</span><span class="sxs-lookup"><span data-stu-id="72ad9-255">15 seconds</span></span> | <span data-ttu-id="72ad9-256">Určuje interval, ve kterém klient odesílá zprávy ping.</span><span class="sxs-lookup"><span data-stu-id="72ad9-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="72ad9-257">Odesláním libovolné zprávy z klienta obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="72ad9-258">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, server považuje klienta za odpojeného.</span><span class="sxs-lookup"><span data-stu-id="72ad9-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="72ad9-259">V klientovi .NET jsou hodnoty `TimeSpan` časového času určeny jako hodnoty.</span><span class="sxs-lookup"><span data-stu-id="72ad9-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="72ad9-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="72ad9-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="72ad9-261">Možnost</span><span class="sxs-lookup"><span data-stu-id="72ad9-261">Option</span></span> | <span data-ttu-id="72ad9-262">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="72ad9-262">Default value</span></span> | <span data-ttu-id="72ad9-263">Popis</span><span class="sxs-lookup"><span data-stu-id="72ad9-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="72ad9-264">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="72ad9-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="72ad9-265">Časový čas pro aktivitu serveru.</span><span class="sxs-lookup"><span data-stu-id="72ad9-265">Timeout for server activity.</span></span> <span data-ttu-id="72ad9-266">Pokud server neodeslal zprávu v tomto intervalu, klient považuje server odpojena a spustí `onclose` událost.</span><span class="sxs-lookup"><span data-stu-id="72ad9-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="72ad9-267">Tato hodnota musí být dostatečně velká, aby zpráva ping byla odeslána ze serveru **a** přijata klientem v časovém intervalu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="72ad9-268">Doporučená hodnota je číslo alespoň dvojnásobek `KeepAliveInterval` hodnoty serveru povolit čas pro příkaz ping k doručení.</span><span class="sxs-lookup"><span data-stu-id="72ad9-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="72ad9-269">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="72ad9-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="72ad9-270">Určuje interval, ve kterém klient odesílá zprávy ping.</span><span class="sxs-lookup"><span data-stu-id="72ad9-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="72ad9-271">Odesláním libovolné zprávy z klienta obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="72ad9-272">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, server považuje klienta za odpojeného.</span><span class="sxs-lookup"><span data-stu-id="72ad9-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="72ad9-273">Java</span><span class="sxs-lookup"><span data-stu-id="72ad9-273">Java</span></span>](#tab/java)

| <span data-ttu-id="72ad9-274">Možnost</span><span class="sxs-lookup"><span data-stu-id="72ad9-274">Option</span></span> | <span data-ttu-id="72ad9-275">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="72ad9-275">Default value</span></span> | <span data-ttu-id="72ad9-276">Popis</span><span class="sxs-lookup"><span data-stu-id="72ad9-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="72ad9-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="72ad9-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="72ad9-278">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="72ad9-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="72ad9-279">Časový čas pro aktivitu serveru.</span><span class="sxs-lookup"><span data-stu-id="72ad9-279">Timeout for server activity.</span></span> <span data-ttu-id="72ad9-280">Pokud server neodeslal zprávu v tomto intervalu, klient považuje server odpojena a spustí `onClose` událost.</span><span class="sxs-lookup"><span data-stu-id="72ad9-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="72ad9-281">Tato hodnota musí být dostatečně velká, aby zpráva ping byla odeslána ze serveru **a** přijata klientem v časovém intervalu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="72ad9-282">Doporučená hodnota je číslo alespoň dvojnásobek `KeepAliveInterval` hodnoty serveru povolit čas pro příkaz ping k doručení.</span><span class="sxs-lookup"><span data-stu-id="72ad9-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="72ad9-283">15 sekund</span><span class="sxs-lookup"><span data-stu-id="72ad9-283">15 seconds</span></span> | <span data-ttu-id="72ad9-284">Časový limit pro počáteční server handshake.</span><span class="sxs-lookup"><span data-stu-id="72ad9-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="72ad9-285">Pokud server neodešle odpověď handshake v tomto intervalu, klient zruší `onClose` handshake a spustí událost.</span><span class="sxs-lookup"><span data-stu-id="72ad9-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="72ad9-286">Toto je rozšířené nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake z důvodu závažné latence sítě.</span><span class="sxs-lookup"><span data-stu-id="72ad9-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="72ad9-287">Další podrobnosti o procesu handshake naleznete v tématu [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="72ad9-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="72ad9-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="72ad9-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="72ad9-289">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="72ad9-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="72ad9-290">Určuje interval, ve kterém klient odesílá zprávy ping.</span><span class="sxs-lookup"><span data-stu-id="72ad9-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="72ad9-291">Odesláním libovolné zprávy z klienta obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="72ad9-292">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, server považuje klienta za odpojeného.</span><span class="sxs-lookup"><span data-stu-id="72ad9-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="72ad9-293">Konfigurace dalších možností</span><span class="sxs-lookup"><span data-stu-id="72ad9-293">Configure additional options</span></span>

<span data-ttu-id="72ad9-294">Další možnosti lze `WithUrl` konfigurovat`withUrl` v metodě `HubConnectionBuilder` (v Jazyce JavaScript) `HttpHubConnectionBuilder` na různých konfiguračních API v klientovi java:</span><span class="sxs-lookup"><span data-stu-id="72ad9-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="72ad9-295">.NET</span><span class="sxs-lookup"><span data-stu-id="72ad9-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="72ad9-296">Možnost .NET</span><span class="sxs-lookup"><span data-stu-id="72ad9-296">.NET Option</span></span> |  <span data-ttu-id="72ad9-297">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="72ad9-297">Default value</span></span> | <span data-ttu-id="72ad9-298">Popis</span><span class="sxs-lookup"><span data-stu-id="72ad9-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="72ad9-299">Funkce vracející řetězec, který je k dispozici jako ověřovací token Nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="72ad9-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="72ad9-300">Nastavte tuto `true` na přeskočení kroku vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="72ad9-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="72ad9-301">**Podporováno pouze v případě, že přenos WebSockets je pouze povolený přenos**.</span><span class="sxs-lookup"><span data-stu-id="72ad9-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="72ad9-302">Toto nastavení nelze povolit při použití služby Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="72ad9-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="72ad9-303">Prázdné</span><span class="sxs-lookup"><span data-stu-id="72ad9-303">Empty</span></span> | <span data-ttu-id="72ad9-304">Kolekce certifikátů TLS k odeslání k ověření požadavků.</span><span class="sxs-lookup"><span data-stu-id="72ad9-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="72ad9-305">Prázdné</span><span class="sxs-lookup"><span data-stu-id="72ad9-305">Empty</span></span> | <span data-ttu-id="72ad9-306">Kolekce souborů cookie HTTP, které se mají odesílat s každým požadavkem HTTP.</span><span class="sxs-lookup"><span data-stu-id="72ad9-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="72ad9-307">Prázdné</span><span class="sxs-lookup"><span data-stu-id="72ad9-307">Empty</span></span> | <span data-ttu-id="72ad9-308">Pověření k odeslání s každým požadavkem HTTP.</span><span class="sxs-lookup"><span data-stu-id="72ad9-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="72ad9-309">5 sekund</span><span class="sxs-lookup"><span data-stu-id="72ad9-309">5 seconds</span></span> | <span data-ttu-id="72ad9-310">Pouze websockets.</span><span class="sxs-lookup"><span data-stu-id="72ad9-310">WebSockets only.</span></span> <span data-ttu-id="72ad9-311">Maximální doba, po kterou klient čeká po zavření, aby server potvrdil požadavek na uzavření.</span><span class="sxs-lookup"><span data-stu-id="72ad9-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="72ad9-312">Pokud server nepotvrdí uzavření během této doby, klient se odpojí.</span><span class="sxs-lookup"><span data-stu-id="72ad9-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="72ad9-313">Prázdné</span><span class="sxs-lookup"><span data-stu-id="72ad9-313">Empty</span></span> | <span data-ttu-id="72ad9-314">Mapa dalších hlaviček PROTOKOLU HTTP, které se mají odesílat s každým požadavkem HTTP.</span><span class="sxs-lookup"><span data-stu-id="72ad9-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="72ad9-315">Delegát, který lze použít ke `HttpMessageHandler` konfiguraci nebo nahrazení použitých k odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="72ad9-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="72ad9-316">Nepoužívá se pro připojení WebSocket.</span><span class="sxs-lookup"><span data-stu-id="72ad9-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="72ad9-317">Tento delegát musí vrátit hodnotu bez hodnoty null a obdrží výchozí hodnotu jako parametr.</span><span class="sxs-lookup"><span data-stu-id="72ad9-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="72ad9-318">Buď upravte nastavení této výchozí hodnoty a `HttpMessageHandler` vraťte ji, nebo vraťte novou instanci.</span><span class="sxs-lookup"><span data-stu-id="72ad9-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="72ad9-319">**Při výměně obslužné rutiny nezapomeňte zkopírovat nastavení, které chcete zachovat z poskytnuté obslužné rutiny, jinak nakonfigurované možnosti (například soubory cookie a záhlaví) se na novou obslužnou rutinu nevztahují.**</span><span class="sxs-lookup"><span data-stu-id="72ad9-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="72ad9-320">Proxy HTTP, který se má použít při odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="72ad9-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="72ad9-321">Nastavte tuto logickou hodnotu tak, aby odesílala výchozí pověření pro požadavky HTTP a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="72ad9-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="72ad9-322">To umožňuje použití ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="72ad9-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="72ad9-323">Delegát, který lze použít ke konfiguraci dalších možností WebSocket.</span><span class="sxs-lookup"><span data-stu-id="72ad9-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="72ad9-324">Přijímá instanci [ClientWebSocketOptions,](/dotnet/api/system.net.websockets.clientwebsocketoptions) která může být použita ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="72ad9-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="72ad9-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="72ad9-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="72ad9-326">Možnost JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="72ad9-326">JavaScript Option</span></span> | <span data-ttu-id="72ad9-327">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="72ad9-327">Default Value</span></span> | <span data-ttu-id="72ad9-328">Popis</span><span class="sxs-lookup"><span data-stu-id="72ad9-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="72ad9-329">Funkce vracející řetězec, který je k dispozici jako ověřovací token Nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="72ad9-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="72ad9-330">Nastavte tuto `true` na přeskočení kroku vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="72ad9-330">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="72ad9-331">**Podporováno pouze v případě, že přenos WebSockets je pouze povolený přenos**.</span><span class="sxs-lookup"><span data-stu-id="72ad9-331">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="72ad9-332">Toto nastavení nelze povolit při použití služby Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="72ad9-332">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="72ad9-333">Java</span><span class="sxs-lookup"><span data-stu-id="72ad9-333">Java</span></span>](#tab/java)

| <span data-ttu-id="72ad9-334">Java možnost</span><span class="sxs-lookup"><span data-stu-id="72ad9-334">Java Option</span></span> | <span data-ttu-id="72ad9-335">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="72ad9-335">Default Value</span></span> | <span data-ttu-id="72ad9-336">Popis</span><span class="sxs-lookup"><span data-stu-id="72ad9-336">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="72ad9-337">Funkce vracející řetězec, který je k dispozici jako ověřovací token Nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="72ad9-337">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="72ad9-338">Nastavte tuto `true` na přeskočení kroku vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="72ad9-338">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="72ad9-339">**Podporováno pouze v případě, že přenos WebSockets je pouze povolený přenos**.</span><span class="sxs-lookup"><span data-stu-id="72ad9-339">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="72ad9-340">Toto nastavení nelze povolit při použití služby Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="72ad9-340">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="72ad9-341">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="72ad9-341">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="72ad9-342">Prázdné</span><span class="sxs-lookup"><span data-stu-id="72ad9-342">Empty</span></span> | <span data-ttu-id="72ad9-343">Mapa dalších hlaviček PROTOKOLU HTTP, které se mají odesílat s každým požadavkem HTTP.</span><span class="sxs-lookup"><span data-stu-id="72ad9-343">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="72ad9-344">V klientovi .NET mohou být tyto možnosti změněny možnostmi, které delegát poskytuje `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="72ad9-344">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="72ad9-345">V klientovi JavaScript mohou být tyto možnosti poskytnuty `withUrl`v objektu JavaScript u :</span><span class="sxs-lookup"><span data-stu-id="72ad9-345">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="72ad9-346">V klientovi Java lze tyto možnosti nakonfigurovat pomocí metod na vráceném `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="72ad9-346">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="72ad9-347">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="72ad9-347">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="72ad9-348">Možnosti serializace balíčku JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="72ad9-348">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="72ad9-349">ASP.NET Core SignalR podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="72ad9-349">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="72ad9-350">Každý protokol má možnosti konfigurace serializace.</span><span class="sxs-lookup"><span data-stu-id="72ad9-350">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="72ad9-351">Serializaci JSON lze na serveru nakonfigurovat pomocí metody rozšíření [AddJsonProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)</span><span class="sxs-lookup"><span data-stu-id="72ad9-351">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="72ad9-352">`AddJsonProtocol`lze přidat po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="72ad9-352">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="72ad9-353">Metoda `AddJsonProtocol` trvá delegáta, který `options` obdrží objekt.</span><span class="sxs-lookup"><span data-stu-id="72ad9-353">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="72ad9-354">[Vlastnost PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) u tohoto `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objektu je objekt, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="72ad9-354">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="72ad9-355">Další informace naleznete v [dokumentaci System.Text.Json](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="72ad9-355">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="72ad9-356">Chcete-li například nakonfigurovat serializátor tak, aby neměnil názvy vlastností, namísto výchozích názvů camelCase použijte následující kód v aplikaci `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="72ad9-356">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="72ad9-357">V klientovi .NET `AddJsonProtocol` existuje stejná metoda rozšíření v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="72ad9-357">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="72ad9-358">Obor `Microsoft.Extensions.DependencyInjection` názvů musí být importován, aby se vyřešila metoda rozšíření:</span><span class="sxs-lookup"><span data-stu-id="72ad9-358">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="72ad9-359">V tuto chvíli není možné konfigurovat serializaci JSON v klientovi JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-359">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="72ad9-360">Přejít na Newtonsoft.Json</span><span class="sxs-lookup"><span data-stu-id="72ad9-360">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="72ad9-361">Pokud potřebujete `Newtonsoft.Json` funkce, které nejsou `System.Text.Json`podporovány v , viz [Přepnout na Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="72ad9-361">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="72ad9-362">Možnosti serializace messagepacku</span><span class="sxs-lookup"><span data-stu-id="72ad9-362">MessagePack serialization options</span></span>

<span data-ttu-id="72ad9-363">Serializace messagepack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)</span><span class="sxs-lookup"><span data-stu-id="72ad9-363">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="72ad9-364">Další podrobnosti najdete [v tématu MessagePack v SignalR.](xref:signalr/messagepackhubprotocol)</span><span class="sxs-lookup"><span data-stu-id="72ad9-364">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="72ad9-365">V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-365">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="72ad9-366">Konfigurace možností serveru</span><span class="sxs-lookup"><span data-stu-id="72ad9-366">Configure server options</span></span>

<span data-ttu-id="72ad9-367">Následující tabulka popisuje možnosti konfigurace rozbočovačů SignalR:</span><span class="sxs-lookup"><span data-stu-id="72ad9-367">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="72ad9-368">Možnost</span><span class="sxs-lookup"><span data-stu-id="72ad9-368">Option</span></span> | <span data-ttu-id="72ad9-369">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="72ad9-369">Default Value</span></span> | <span data-ttu-id="72ad9-370">Popis</span><span class="sxs-lookup"><span data-stu-id="72ad9-370">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="72ad9-371">30 sekund</span><span class="sxs-lookup"><span data-stu-id="72ad9-371">30 seconds</span></span> | <span data-ttu-id="72ad9-372">Server bude považovat klientodpojený, pokud neobdržel zprávu (včetně keep-alive) v tomto intervalu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-372">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="72ad9-373">Může trvat déle než tento časový interval pro klienta skutečně označeny odpojen, z důvodu, jak je implementována.</span><span class="sxs-lookup"><span data-stu-id="72ad9-373">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="72ad9-374">Doporučená hodnota je `KeepAliveInterval` dvojnásobek hodnoty.</span><span class="sxs-lookup"><span data-stu-id="72ad9-374">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="72ad9-375">15 sekund</span><span class="sxs-lookup"><span data-stu-id="72ad9-375">15 seconds</span></span> | <span data-ttu-id="72ad9-376">Pokud klient neodešle počáteční zprávu handshake v tomto časovém intervalu, připojení je ukončeno.</span><span class="sxs-lookup"><span data-stu-id="72ad9-376">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="72ad9-377">Toto je rozšířené nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake z důvodu závažné latence sítě.</span><span class="sxs-lookup"><span data-stu-id="72ad9-377">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="72ad9-378">Další podrobnosti o procesu handshake naleznete v tématu [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="72ad9-378">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="72ad9-379">15 sekund</span><span class="sxs-lookup"><span data-stu-id="72ad9-379">15 seconds</span></span> | <span data-ttu-id="72ad9-380">Pokud server neodeslal zprávu v tomto intervalu, je automaticky odeslána zpráva ping, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="72ad9-380">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="72ad9-381">Při `KeepAliveInterval`změně změňte `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení klienta.</span><span class="sxs-lookup"><span data-stu-id="72ad9-381">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="72ad9-382">Doporučená `ServerTimeout` / `serverTimeoutInMilliseconds` hodnota je `KeepAliveInterval` dvojnásobek hodnoty.</span><span class="sxs-lookup"><span data-stu-id="72ad9-382">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="72ad9-383">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="72ad9-383">All installed protocols</span></span> | <span data-ttu-id="72ad9-384">Protokoly podporované tímto centrem.</span><span class="sxs-lookup"><span data-stu-id="72ad9-384">Protocols supported by this hub.</span></span> <span data-ttu-id="72ad9-385">Ve výchozím nastavení jsou povoleny všechny protokoly registrované na serveru, ale protokoly mohou být odebrány z tohoto seznamu zakázat konkrétní protokoly pro jednotlivé rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="72ad9-385">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="72ad9-386">Pokud `true`, podrobné zprávy o výjimce jsou vráceny klientům při vyvolání výjimky v Hub metody.</span><span class="sxs-lookup"><span data-stu-id="72ad9-386">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="72ad9-387">Výchozí hodnota `false`je , protože tyto zprávy o výjimce mohou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="72ad9-387">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="72ad9-388">Maximální počet položek, které mohou být uloženy do vyrovnávací paměti pro datové proudy pro odesílání klienta.</span><span class="sxs-lookup"><span data-stu-id="72ad9-388">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="72ad9-389">Pokud je dosaženo tohoto limitu, zpracování vyvolání je blokován, dokud server zpracovává položky datového proudu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-389">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="72ad9-390">32 KB</span><span class="sxs-lookup"><span data-stu-id="72ad9-390">32 KB</span></span> | <span data-ttu-id="72ad9-391">Maximální velikost jedné příchozí zprávy rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="72ad9-391">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="72ad9-392">Možnosti lze nakonfigurovat pro všechny rozbočovače `Startup.ConfigureServices`poskytnutím možnosti delegáta volání v aplikaci `AddSignalR` .</span><span class="sxs-lookup"><span data-stu-id="72ad9-392">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="72ad9-393">Možnosti pro jeden rozbočovač přepíší globální možnosti uvedené v `AddSignalR` aplikaci a lze je konfigurovat pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span><span class="sxs-lookup"><span data-stu-id="72ad9-393">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="72ad9-394">Rozšířené možnosti konfigurace protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="72ad9-394">Advanced HTTP configuration options</span></span>

<span data-ttu-id="72ad9-395">Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="72ad9-395">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="72ad9-396">Tyto možnosti jsou konfigurovány předáním delegáta [maphub\<t>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="72ad9-396">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="72ad9-397">Následující tabulka popisuje možnosti konfigurace ASP.NET pokročilých možností protokolu HTTP nástroje Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="72ad9-397">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="72ad9-398">Možnost</span><span class="sxs-lookup"><span data-stu-id="72ad9-398">Option</span></span> | <span data-ttu-id="72ad9-399">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="72ad9-399">Default Value</span></span> | <span data-ttu-id="72ad9-400">Popis</span><span class="sxs-lookup"><span data-stu-id="72ad9-400">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="72ad9-401">32 KB</span><span class="sxs-lookup"><span data-stu-id="72ad9-401">32 KB</span></span> | <span data-ttu-id="72ad9-402">Maximální počet bajtů přijatých od klienta, které server vyrovnávací paměti před použitím protitlaku.</span><span class="sxs-lookup"><span data-stu-id="72ad9-402">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="72ad9-403">Zvýšení této hodnoty umožňuje serveru přijímat větší zprávy rychleji bez použití protitlaku, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="72ad9-403">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="72ad9-404">Data automaticky získaná z `Authorize` atributů použitých pro třídu Hub.</span><span class="sxs-lookup"><span data-stu-id="72ad9-404">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="72ad9-405">Seznam objektů [IAuthorizeData,](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) které slouží k určení, zda je klient oprávněn připojit k rozbočovači.</span><span class="sxs-lookup"><span data-stu-id="72ad9-405">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="72ad9-406">32 KB</span><span class="sxs-lookup"><span data-stu-id="72ad9-406">32 KB</span></span> | <span data-ttu-id="72ad9-407">Maximální počet bajtů odeslaných aplikací, které server vyrovnávací paměti před sledováním protitlaku.</span><span class="sxs-lookup"><span data-stu-id="72ad9-407">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="72ad9-408">Zvýšení této hodnoty umožňuje serveru do vyrovnávací paměti větší zprávy rychleji bez čekání na protitlak, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="72ad9-408">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="72ad9-409">Všechny přenosy jsou povoleny.</span><span class="sxs-lookup"><span data-stu-id="72ad9-409">All Transports are enabled.</span></span> | <span data-ttu-id="72ad9-410">Bit příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenosy klienta můžete použít k připojení.</span><span class="sxs-lookup"><span data-stu-id="72ad9-410">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="72ad9-411">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="72ad9-411">See below.</span></span> | <span data-ttu-id="72ad9-412">Další možnosti specifické pro přenos dlouhé dotazování.</span><span class="sxs-lookup"><span data-stu-id="72ad9-412">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="72ad9-413">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="72ad9-413">See below.</span></span> | <span data-ttu-id="72ad9-414">Další možnosti specifické pro přenos WebSockets.</span><span class="sxs-lookup"><span data-stu-id="72ad9-414">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="72ad9-415">Přenos dlouhé dotazování má další možnosti, které lze nakonfigurovat pomocí vlastnosti: `LongPolling`</span><span class="sxs-lookup"><span data-stu-id="72ad9-415">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="72ad9-416">Možnost</span><span class="sxs-lookup"><span data-stu-id="72ad9-416">Option</span></span> | <span data-ttu-id="72ad9-417">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="72ad9-417">Default Value</span></span> | <span data-ttu-id="72ad9-418">Popis</span><span class="sxs-lookup"><span data-stu-id="72ad9-418">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="72ad9-419">90 sekund</span><span class="sxs-lookup"><span data-stu-id="72ad9-419">90 seconds</span></span> | <span data-ttu-id="72ad9-420">Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jednoho požadavku na dotazování.</span><span class="sxs-lookup"><span data-stu-id="72ad9-420">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="72ad9-421">Snížení této hodnoty způsobí, že klient vydávat nové požadavky na dotazování častěji.</span><span class="sxs-lookup"><span data-stu-id="72ad9-421">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="72ad9-422">Přenos WebSocket má další možnosti, `WebSockets` které lze nakonfigurovat pomocí vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="72ad9-422">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="72ad9-423">Možnost</span><span class="sxs-lookup"><span data-stu-id="72ad9-423">Option</span></span> | <span data-ttu-id="72ad9-424">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="72ad9-424">Default Value</span></span> | <span data-ttu-id="72ad9-425">Popis</span><span class="sxs-lookup"><span data-stu-id="72ad9-425">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="72ad9-426">5 sekund</span><span class="sxs-lookup"><span data-stu-id="72ad9-426">5 seconds</span></span> | <span data-ttu-id="72ad9-427">Po ukončení serveru, pokud se klientovi nepodaří zavřít v tomto časovém intervalu, je připojení ukončeno.</span><span class="sxs-lookup"><span data-stu-id="72ad9-427">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="72ad9-428">Delegát, který lze použít `Sec-WebSocket-Protocol` k nastavení záhlaví na vlastní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-428">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="72ad9-429">Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že vrátí požadovanou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-429">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="72ad9-430">Konfigurace možností klienta</span><span class="sxs-lookup"><span data-stu-id="72ad9-430">Configure client options</span></span>

<span data-ttu-id="72ad9-431">Možnosti klienta lze `HubConnectionBuilder` konfigurovat na typu (k dispozici v klientech .NET a JavaScript).</span><span class="sxs-lookup"><span data-stu-id="72ad9-431">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="72ad9-432">Je také k dispozici v klientovi `HttpHubConnectionBuilder` Java, ale podtřída je to, co `HubConnection` obsahuje možnosti konfigurace tvůrce, stejně jako na sobě.</span><span class="sxs-lookup"><span data-stu-id="72ad9-432">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="72ad9-433">Konfigurace protokolování</span><span class="sxs-lookup"><span data-stu-id="72ad9-433">Configure logging</span></span>

<span data-ttu-id="72ad9-434">Protokolování je konfigurováno v `ConfigureLogging` klientovi .NET pomocí metody.</span><span class="sxs-lookup"><span data-stu-id="72ad9-434">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="72ad9-435">Zprostředkovatelé protokolování a filtry mohou být registrovány stejným způsobem jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="72ad9-435">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="72ad9-436">Další informace naleznete v dokumentaci [protokolování ASP.NET jádra.](xref:fundamentals/logging/index)</span><span class="sxs-lookup"><span data-stu-id="72ad9-436">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="72ad9-437">Chcete-li zaregistrovat zprostředkovatele protokolování, je nutné nainstalovat potřebné balíčky.</span><span class="sxs-lookup"><span data-stu-id="72ad9-437">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="72ad9-438">Úplný seznam najdete v části [Vestavěné zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentech.</span><span class="sxs-lookup"><span data-stu-id="72ad9-438">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="72ad9-439">Chcete-li například povolit `Microsoft.Extensions.Logging.Console` protokolování konzoly, nainstalujte balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="72ad9-439">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="72ad9-440">Volání `AddConsole` metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="72ad9-440">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="72ad9-441">V klientovi JavaScript `configureLogging` existuje podobná metoda.</span><span class="sxs-lookup"><span data-stu-id="72ad9-441">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="72ad9-442">Zadejte `LogLevel` hodnotu označující minimální úroveň zpráv protokolu k vytvoření.</span><span class="sxs-lookup"><span data-stu-id="72ad9-442">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="72ad9-443">Protokoly jsou zapsány do okna konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="72ad9-443">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="72ad9-444">Namísto `LogLevel` hodnoty můžete také zadat `string` hodnotu představující název na úrovni protokolu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-444">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="72ad9-445">To je užitečné při konfiguraci protokolování SignalR v prostředích, kde nemáte přístup k `LogLevel` konstantám.</span><span class="sxs-lookup"><span data-stu-id="72ad9-445">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="72ad9-446">V následující tabulce jsou uvedeny dostupné úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-446">The following table lists the available log levels.</span></span> <span data-ttu-id="72ad9-447">Hodnota, kterou `configureLogging` zadáte pro nastavení **minimální** úroveň protokolu, která bude zaznamenána.</span><span class="sxs-lookup"><span data-stu-id="72ad9-447">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="72ad9-448">Zprávy zaznamenané na této úrovni **nebo úrovně uvedené za ní v tabulce**budou zaznamenány.</span><span class="sxs-lookup"><span data-stu-id="72ad9-448">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="72ad9-449">Řetězec</span><span class="sxs-lookup"><span data-stu-id="72ad9-449">String</span></span>                      | <span data-ttu-id="72ad9-450">LogLevel</span><span class="sxs-lookup"><span data-stu-id="72ad9-450">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="72ad9-451">`info`**nebo**`information`</span><span class="sxs-lookup"><span data-stu-id="72ad9-451">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="72ad9-452">`warn`**nebo**`warning`</span><span class="sxs-lookup"><span data-stu-id="72ad9-452">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="72ad9-453">Chcete-li protokolování `signalR.LogLevel.None` zcela `configureLogging` zakázat, zadejte v metodě.</span><span class="sxs-lookup"><span data-stu-id="72ad9-453">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="72ad9-454">Další informace o protokolování naleznete v [dokumentaci k diagnostice signalr .](xref:signalr/diagnostics)</span><span class="sxs-lookup"><span data-stu-id="72ad9-454">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="72ad9-455">Klient Java SignalR používá pro protokolování knihovnu [SLF4J.](https://www.slf4j.org/)</span><span class="sxs-lookup"><span data-stu-id="72ad9-455">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="72ad9-456">Jedná se o rozhraní API pro protokolování vysoké úrovně, které umožňuje uživatelům knihovny zvolit vlastní implementaci protokolování tím, že přivedou konkrétní závislost protokolování.</span><span class="sxs-lookup"><span data-stu-id="72ad9-456">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="72ad9-457">Následující fragment kódu ukazuje, jak `java.util.logging` se používat s klientem Java SignalR.</span><span class="sxs-lookup"><span data-stu-id="72ad9-457">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="72ad9-458">Pokud nenakonfigurujete protokolování v závislostech, SLF4J načte výchozí protokolovací nástroj bez operace s následující varovnou zprávou:</span><span class="sxs-lookup"><span data-stu-id="72ad9-458">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="72ad9-459">To lze bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="72ad9-459">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="72ad9-460">Konfigurace povolených přenosů</span><span class="sxs-lookup"><span data-stu-id="72ad9-460">Configure allowed transports</span></span>

<span data-ttu-id="72ad9-461">Přenosy používané SignalR lze konfigurovat `WithUrl` ve`withUrl` volání (v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="72ad9-461">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="72ad9-462">Bitové nebo hodnoty `HttpTransportType` lze omezit klienta používat pouze zadané přenosy.</span><span class="sxs-lookup"><span data-stu-id="72ad9-462">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="72ad9-463">Všechny přenosy jsou ve výchozím nastavení povoleny.</span><span class="sxs-lookup"><span data-stu-id="72ad9-463">All transports are enabled by default.</span></span>

<span data-ttu-id="72ad9-464">Chcete-li například zakázat přenos událostí odeslaných serverem, ale povolit připojení WebSockets a Long Polling:</span><span class="sxs-lookup"><span data-stu-id="72ad9-464">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="72ad9-465">V klientovi JavaScript jsou přenosy `transport` konfigurovány nastavením pole `withUrl`na objektu options, který je k dispozici :</span><span class="sxs-lookup"><span data-stu-id="72ad9-465">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="72ad9-466">V této verzi webových zásuvek klienta Java je pouze dostupný přenos.</span><span class="sxs-lookup"><span data-stu-id="72ad9-466">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="72ad9-467">V klientovi Java je přenos `withTransport` vybrán s `HttpHubConnectionBuilder`metodou na .</span><span class="sxs-lookup"><span data-stu-id="72ad9-467">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="72ad9-468">Klient Java ve výchozím nastavení používá přenos WebSockets.</span><span class="sxs-lookup"><span data-stu-id="72ad9-468">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="72ad9-469">Klient Java SignalR ještě nepodporuje záložní přenos.</span><span class="sxs-lookup"><span data-stu-id="72ad9-469">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="72ad9-470">Konfigurace ověřování nosiče</span><span class="sxs-lookup"><span data-stu-id="72ad9-470">Configure bearer authentication</span></span>

<span data-ttu-id="72ad9-471">Chcete-li poskytnout ověřovací data spolu s `AccessTokenProvider` požadavky`accessTokenFactory` SignalR, použijte možnost ( v Jazyce JavaScript a určete funkci, která vrátí požadovaný přístupový token.</span><span class="sxs-lookup"><span data-stu-id="72ad9-471">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="72ad9-472">V klientovi .NET je tento přístupový token předán jako http "Bearer Authentication" token (Pomocí `Authorization` hlavičky `Bearer`s typem ).</span><span class="sxs-lookup"><span data-stu-id="72ad9-472">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="72ad9-473">V klientovi JavaScript se přístupový token používá jako token Nosiče, **s výjimkou** několika případů, kdy rozhraní API prohlížeče omezují možnost použití záhlaví (konkrétně v požadavcích Události odeslané serverem a WebSockets).</span><span class="sxs-lookup"><span data-stu-id="72ad9-473">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="72ad9-474">V těchto případech je přístupový token k `access_token`dispozici jako hodnota řetězce dotazu .</span><span class="sxs-lookup"><span data-stu-id="72ad9-474">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="72ad9-475">V klientovi .NET `AccessTokenProvider` lze tuto možnost zadat `WithUrl`pomocí možností delegáta v aplikaci :</span><span class="sxs-lookup"><span data-stu-id="72ad9-475">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="72ad9-476">V klientovi JavaScript je přístupový token `accessTokenFactory` konfigurován nastavením `withUrl`pole na objektu options v aplikaci :</span><span class="sxs-lookup"><span data-stu-id="72ad9-476">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="72ad9-477">V klientovi SignalR Java můžete nakonfigurovat token nosiče pro ověřování poskytnutím objektu [HttpHubConnectionBuilder.](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)</span><span class="sxs-lookup"><span data-stu-id="72ad9-477">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="72ad9-478">Použijte [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) poskytnout [RxJava](https://github.com/ReactiveX/RxJava) [single\<string>](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="72ad9-478">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="72ad9-479">S [volánísingle.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), můžete napsat logiku k vytvoření přístupové tokeny pro vašeho klienta.</span><span class="sxs-lookup"><span data-stu-id="72ad9-479">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="72ad9-480">Konfigurace možností časového limitu a udržování v životě</span><span class="sxs-lookup"><span data-stu-id="72ad9-480">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="72ad9-481">Další možnosti pro konfiguraci časového limitu a `HubConnection` chování udržování naživu jsou k dispozici na samotném objektu:</span><span class="sxs-lookup"><span data-stu-id="72ad9-481">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="72ad9-482">.NET</span><span class="sxs-lookup"><span data-stu-id="72ad9-482">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="72ad9-483">Možnost</span><span class="sxs-lookup"><span data-stu-id="72ad9-483">Option</span></span> | <span data-ttu-id="72ad9-484">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="72ad9-484">Default value</span></span> | <span data-ttu-id="72ad9-485">Popis</span><span class="sxs-lookup"><span data-stu-id="72ad9-485">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="72ad9-486">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="72ad9-486">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="72ad9-487">Časový čas pro aktivitu serveru.</span><span class="sxs-lookup"><span data-stu-id="72ad9-487">Timeout for server activity.</span></span> <span data-ttu-id="72ad9-488">Pokud server v tomto intervalu neodeslal zprávu, klient uváží server `Closed` odpojený`onclose` a spustí událost (v Jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="72ad9-488">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="72ad9-489">Tato hodnota musí být dostatečně velká, aby zpráva ping byla odeslána ze serveru **a** přijata klientem v časovém intervalu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-489">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="72ad9-490">Doporučená hodnota je číslo alespoň dvojnásobek `KeepAliveInterval` hodnoty serveru povolit čas pro příkaz ping k doručení.</span><span class="sxs-lookup"><span data-stu-id="72ad9-490">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="72ad9-491">15 sekund</span><span class="sxs-lookup"><span data-stu-id="72ad9-491">15 seconds</span></span> | <span data-ttu-id="72ad9-492">Časový limit pro počáteční server handshake.</span><span class="sxs-lookup"><span data-stu-id="72ad9-492">Timeout for initial server handshake.</span></span> <span data-ttu-id="72ad9-493">Pokud server neodešle odpověď handshake v tomto intervalu, klient zruší `Closed` handshake`onclose` a spustí událost (v Jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="72ad9-493">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="72ad9-494">Toto je rozšířené nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake z důvodu závažné latence sítě.</span><span class="sxs-lookup"><span data-stu-id="72ad9-494">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="72ad9-495">Další podrobnosti o procesu handshake naleznete v tématu [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="72ad9-495">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="72ad9-496">15 sekund</span><span class="sxs-lookup"><span data-stu-id="72ad9-496">15 seconds</span></span> | <span data-ttu-id="72ad9-497">Určuje interval, ve kterém klient odesílá zprávy ping.</span><span class="sxs-lookup"><span data-stu-id="72ad9-497">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="72ad9-498">Odesláním libovolné zprávy z klienta obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-498">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="72ad9-499">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, server považuje klienta za odpojeného.</span><span class="sxs-lookup"><span data-stu-id="72ad9-499">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="72ad9-500">V klientovi .NET jsou hodnoty `TimeSpan` časového času určeny jako hodnoty.</span><span class="sxs-lookup"><span data-stu-id="72ad9-500">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="72ad9-501">JavaScript</span><span class="sxs-lookup"><span data-stu-id="72ad9-501">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="72ad9-502">Možnost</span><span class="sxs-lookup"><span data-stu-id="72ad9-502">Option</span></span> | <span data-ttu-id="72ad9-503">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="72ad9-503">Default value</span></span> | <span data-ttu-id="72ad9-504">Popis</span><span class="sxs-lookup"><span data-stu-id="72ad9-504">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="72ad9-505">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="72ad9-505">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="72ad9-506">Časový čas pro aktivitu serveru.</span><span class="sxs-lookup"><span data-stu-id="72ad9-506">Timeout for server activity.</span></span> <span data-ttu-id="72ad9-507">Pokud server neodeslal zprávu v tomto intervalu, klient považuje server odpojena a spustí `onclose` událost.</span><span class="sxs-lookup"><span data-stu-id="72ad9-507">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="72ad9-508">Tato hodnota musí být dostatečně velká, aby zpráva ping byla odeslána ze serveru **a** přijata klientem v časovém intervalu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-508">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="72ad9-509">Doporučená hodnota je číslo alespoň dvojnásobek `KeepAliveInterval` hodnoty serveru povolit čas pro příkaz ping k doručení.</span><span class="sxs-lookup"><span data-stu-id="72ad9-509">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="72ad9-510">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="72ad9-510">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="72ad9-511">Určuje interval, ve kterém klient odesílá zprávy ping.</span><span class="sxs-lookup"><span data-stu-id="72ad9-511">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="72ad9-512">Odesláním libovolné zprávy z klienta obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-512">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="72ad9-513">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, server považuje klienta za odpojeného.</span><span class="sxs-lookup"><span data-stu-id="72ad9-513">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="72ad9-514">Java</span><span class="sxs-lookup"><span data-stu-id="72ad9-514">Java</span></span>](#tab/java)

| <span data-ttu-id="72ad9-515">Možnost</span><span class="sxs-lookup"><span data-stu-id="72ad9-515">Option</span></span> | <span data-ttu-id="72ad9-516">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="72ad9-516">Default value</span></span> | <span data-ttu-id="72ad9-517">Popis</span><span class="sxs-lookup"><span data-stu-id="72ad9-517">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="72ad9-518">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="72ad9-518">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="72ad9-519">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="72ad9-519">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="72ad9-520">Časový čas pro aktivitu serveru.</span><span class="sxs-lookup"><span data-stu-id="72ad9-520">Timeout for server activity.</span></span> <span data-ttu-id="72ad9-521">Pokud server neodeslal zprávu v tomto intervalu, klient považuje server odpojena a spustí `onClose` událost.</span><span class="sxs-lookup"><span data-stu-id="72ad9-521">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="72ad9-522">Tato hodnota musí být dostatečně velká, aby zpráva ping byla odeslána ze serveru **a** přijata klientem v časovém intervalu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-522">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="72ad9-523">Doporučená hodnota je číslo alespoň dvojnásobek `KeepAliveInterval` hodnoty serveru povolit čas pro příkaz ping k doručení.</span><span class="sxs-lookup"><span data-stu-id="72ad9-523">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="72ad9-524">15 sekund</span><span class="sxs-lookup"><span data-stu-id="72ad9-524">15 seconds</span></span> | <span data-ttu-id="72ad9-525">Časový limit pro počáteční server handshake.</span><span class="sxs-lookup"><span data-stu-id="72ad9-525">Timeout for initial server handshake.</span></span> <span data-ttu-id="72ad9-526">Pokud server neodešle odpověď handshake v tomto intervalu, klient zruší `onClose` handshake a spustí událost.</span><span class="sxs-lookup"><span data-stu-id="72ad9-526">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="72ad9-527">Toto je rozšířené nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake z důvodu závažné latence sítě.</span><span class="sxs-lookup"><span data-stu-id="72ad9-527">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="72ad9-528">Další podrobnosti o procesu handshake naleznete v tématu [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="72ad9-528">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="72ad9-529">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="72ad9-529">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="72ad9-530">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="72ad9-530">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="72ad9-531">Určuje interval, ve kterém klient odesílá zprávy ping.</span><span class="sxs-lookup"><span data-stu-id="72ad9-531">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="72ad9-532">Odesláním libovolné zprávy z klienta obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-532">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="72ad9-533">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, server považuje klienta za odpojeného.</span><span class="sxs-lookup"><span data-stu-id="72ad9-533">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="72ad9-534">Konfigurace dalších možností</span><span class="sxs-lookup"><span data-stu-id="72ad9-534">Configure additional options</span></span>

<span data-ttu-id="72ad9-535">Další možnosti lze `WithUrl` konfigurovat`withUrl` v metodě `HubConnectionBuilder` (v Jazyce JavaScript) `HttpHubConnectionBuilder` na různých konfiguračních API v klientovi java:</span><span class="sxs-lookup"><span data-stu-id="72ad9-535">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="72ad9-536">.NET</span><span class="sxs-lookup"><span data-stu-id="72ad9-536">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="72ad9-537">Možnost .NET</span><span class="sxs-lookup"><span data-stu-id="72ad9-537">.NET Option</span></span> |  <span data-ttu-id="72ad9-538">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="72ad9-538">Default value</span></span> | <span data-ttu-id="72ad9-539">Popis</span><span class="sxs-lookup"><span data-stu-id="72ad9-539">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="72ad9-540">Funkce vracející řetězec, který je k dispozici jako ověřovací token Nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="72ad9-540">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="72ad9-541">Nastavte tuto `true` na přeskočení kroku vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="72ad9-541">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="72ad9-542">**Podporováno pouze v případě, že přenos WebSockets je pouze povolený přenos**.</span><span class="sxs-lookup"><span data-stu-id="72ad9-542">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="72ad9-543">Toto nastavení nelze povolit při použití služby Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="72ad9-543">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="72ad9-544">Prázdné</span><span class="sxs-lookup"><span data-stu-id="72ad9-544">Empty</span></span> | <span data-ttu-id="72ad9-545">Kolekce certifikátů TLS k odeslání k ověření požadavků.</span><span class="sxs-lookup"><span data-stu-id="72ad9-545">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="72ad9-546">Prázdné</span><span class="sxs-lookup"><span data-stu-id="72ad9-546">Empty</span></span> | <span data-ttu-id="72ad9-547">Kolekce souborů cookie HTTP, které se mají odesílat s každým požadavkem HTTP.</span><span class="sxs-lookup"><span data-stu-id="72ad9-547">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="72ad9-548">Prázdné</span><span class="sxs-lookup"><span data-stu-id="72ad9-548">Empty</span></span> | <span data-ttu-id="72ad9-549">Pověření k odeslání s každým požadavkem HTTP.</span><span class="sxs-lookup"><span data-stu-id="72ad9-549">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="72ad9-550">5 sekund</span><span class="sxs-lookup"><span data-stu-id="72ad9-550">5 seconds</span></span> | <span data-ttu-id="72ad9-551">Pouze websockets.</span><span class="sxs-lookup"><span data-stu-id="72ad9-551">WebSockets only.</span></span> <span data-ttu-id="72ad9-552">Maximální doba, po kterou klient čeká po zavření, aby server potvrdil požadavek na uzavření.</span><span class="sxs-lookup"><span data-stu-id="72ad9-552">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="72ad9-553">Pokud server nepotvrdí uzavření během této doby, klient se odpojí.</span><span class="sxs-lookup"><span data-stu-id="72ad9-553">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="72ad9-554">Prázdné</span><span class="sxs-lookup"><span data-stu-id="72ad9-554">Empty</span></span> | <span data-ttu-id="72ad9-555">Mapa dalších hlaviček PROTOKOLU HTTP, které se mají odesílat s každým požadavkem HTTP.</span><span class="sxs-lookup"><span data-stu-id="72ad9-555">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="72ad9-556">Delegát, který lze použít ke `HttpMessageHandler` konfiguraci nebo nahrazení použitých k odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="72ad9-556">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="72ad9-557">Nepoužívá se pro připojení WebSocket.</span><span class="sxs-lookup"><span data-stu-id="72ad9-557">Not used for WebSocket connections.</span></span> <span data-ttu-id="72ad9-558">Tento delegát musí vrátit hodnotu bez hodnoty null a obdrží výchozí hodnotu jako parametr.</span><span class="sxs-lookup"><span data-stu-id="72ad9-558">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="72ad9-559">Buď upravte nastavení této výchozí hodnoty a `HttpMessageHandler` vraťte ji, nebo vraťte novou instanci.</span><span class="sxs-lookup"><span data-stu-id="72ad9-559">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="72ad9-560">**Při výměně obslužné rutiny nezapomeňte zkopírovat nastavení, které chcete zachovat z poskytnuté obslužné rutiny, jinak nakonfigurované možnosti (například soubory cookie a záhlaví) se na novou obslužnou rutinu nevztahují.**</span><span class="sxs-lookup"><span data-stu-id="72ad9-560">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="72ad9-561">Proxy HTTP, který se má použít při odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="72ad9-561">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="72ad9-562">Nastavte tuto logickou hodnotu tak, aby odesílala výchozí pověření pro požadavky HTTP a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="72ad9-562">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="72ad9-563">To umožňuje použití ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="72ad9-563">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="72ad9-564">Delegát, který lze použít ke konfiguraci dalších možností WebSocket.</span><span class="sxs-lookup"><span data-stu-id="72ad9-564">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="72ad9-565">Přijímá instanci [ClientWebSocketOptions,](/dotnet/api/system.net.websockets.clientwebsocketoptions) která může být použita ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="72ad9-565">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="72ad9-566">JavaScript</span><span class="sxs-lookup"><span data-stu-id="72ad9-566">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="72ad9-567">Možnost JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="72ad9-567">JavaScript Option</span></span> | <span data-ttu-id="72ad9-568">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="72ad9-568">Default Value</span></span> | <span data-ttu-id="72ad9-569">Popis</span><span class="sxs-lookup"><span data-stu-id="72ad9-569">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="72ad9-570">Funkce vracející řetězec, který je k dispozici jako ověřovací token Nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="72ad9-570">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="72ad9-571">Nastavte tuto `true` na přeskočení kroku vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="72ad9-571">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="72ad9-572">**Podporováno pouze v případě, že přenos WebSockets je pouze povolený přenos**.</span><span class="sxs-lookup"><span data-stu-id="72ad9-572">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="72ad9-573">Toto nastavení nelze povolit při použití služby Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="72ad9-573">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="72ad9-574">Java</span><span class="sxs-lookup"><span data-stu-id="72ad9-574">Java</span></span>](#tab/java)

| <span data-ttu-id="72ad9-575">Java možnost</span><span class="sxs-lookup"><span data-stu-id="72ad9-575">Java Option</span></span> | <span data-ttu-id="72ad9-576">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="72ad9-576">Default Value</span></span> | <span data-ttu-id="72ad9-577">Popis</span><span class="sxs-lookup"><span data-stu-id="72ad9-577">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="72ad9-578">Funkce vracející řetězec, který je k dispozici jako ověřovací token Nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="72ad9-578">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="72ad9-579">Nastavte tuto `true` na přeskočení kroku vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="72ad9-579">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="72ad9-580">**Podporováno pouze v případě, že přenos WebSockets je pouze povolený přenos**.</span><span class="sxs-lookup"><span data-stu-id="72ad9-580">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="72ad9-581">Toto nastavení nelze povolit při použití služby Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="72ad9-581">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="72ad9-582">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="72ad9-582">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="72ad9-583">Prázdné</span><span class="sxs-lookup"><span data-stu-id="72ad9-583">Empty</span></span> | <span data-ttu-id="72ad9-584">Mapa dalších hlaviček PROTOKOLU HTTP, které se mají odesílat s každým požadavkem HTTP.</span><span class="sxs-lookup"><span data-stu-id="72ad9-584">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="72ad9-585">V klientovi .NET mohou být tyto možnosti změněny možnostmi, které delegát poskytuje `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="72ad9-585">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="72ad9-586">V klientovi JavaScript mohou být tyto možnosti poskytnuty `withUrl`v objektu JavaScript u :</span><span class="sxs-lookup"><span data-stu-id="72ad9-586">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="72ad9-587">V klientovi Java lze tyto možnosti nakonfigurovat pomocí metod na vráceném `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="72ad9-587">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="72ad9-588">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="72ad9-588">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="72ad9-589">Možnosti serializace balíčku JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="72ad9-589">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="72ad9-590">ASP.NET Core SignalR podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="72ad9-590">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="72ad9-591">Každý protokol má možnosti konfigurace serializace.</span><span class="sxs-lookup"><span data-stu-id="72ad9-591">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="72ad9-592">Serializaci JSON lze na serveru nakonfigurovat pomocí metody rozšíření [AddJsonProtocol,](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) kterou `Startup.ConfigureServices` lze přidat za [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) ve vaší metodě.</span><span class="sxs-lookup"><span data-stu-id="72ad9-592">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="72ad9-593">Metoda `AddJsonProtocol` trvá delegáta, který `options` obdrží objekt.</span><span class="sxs-lookup"><span data-stu-id="72ad9-593">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="72ad9-594">[Vlastnost PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) u tohoto objektu je JSON.NET `JsonSerializerSettings` objekt, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="72ad9-594">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="72ad9-595">Další informace naleznete v [dokumentaci k JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="72ad9-595">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="72ad9-596">Jako příklad nakonfigurujte serializátor tak, aby používal názvy vlastností "PascalCase", `Startup.ConfigureServices`namísto výchozích názvů camelCase, použijte následující kód v aplikaci :</span><span class="sxs-lookup"><span data-stu-id="72ad9-596">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="72ad9-597">V klientovi .NET `AddJsonProtocol` existuje stejná metoda rozšíření v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="72ad9-597">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="72ad9-598">Obor `Microsoft.Extensions.DependencyInjection` názvů musí být importován, aby se vyřešila metoda rozšíření:</span><span class="sxs-lookup"><span data-stu-id="72ad9-598">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="72ad9-599">V tuto chvíli není možné konfigurovat serializaci JSON v klientovi JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-599">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="72ad9-600">Možnosti serializace messagepacku</span><span class="sxs-lookup"><span data-stu-id="72ad9-600">MessagePack serialization options</span></span>

<span data-ttu-id="72ad9-601">Serializace messagepack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)</span><span class="sxs-lookup"><span data-stu-id="72ad9-601">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="72ad9-602">Další podrobnosti najdete [v tématu MessagePack v SignalR.](xref:signalr/messagepackhubprotocol)</span><span class="sxs-lookup"><span data-stu-id="72ad9-602">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="72ad9-603">V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-603">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="72ad9-604">Konfigurace možností serveru</span><span class="sxs-lookup"><span data-stu-id="72ad9-604">Configure server options</span></span>

<span data-ttu-id="72ad9-605">Následující tabulka popisuje možnosti konfigurace rozbočovačů SignalR:</span><span class="sxs-lookup"><span data-stu-id="72ad9-605">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="72ad9-606">Možnost</span><span class="sxs-lookup"><span data-stu-id="72ad9-606">Option</span></span> | <span data-ttu-id="72ad9-607">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="72ad9-607">Default Value</span></span> | <span data-ttu-id="72ad9-608">Popis</span><span class="sxs-lookup"><span data-stu-id="72ad9-608">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="72ad9-609">30 sekund</span><span class="sxs-lookup"><span data-stu-id="72ad9-609">30 seconds</span></span> | <span data-ttu-id="72ad9-610">Server bude považovat klientodpojený, pokud neobdržel zprávu (včetně keep-alive) v tomto intervalu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-610">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="72ad9-611">Může trvat déle než tento časový interval pro klienta skutečně označeny odpojen, z důvodu, jak je implementována.</span><span class="sxs-lookup"><span data-stu-id="72ad9-611">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="72ad9-612">Doporučená hodnota je `KeepAliveInterval` dvojnásobek hodnoty.</span><span class="sxs-lookup"><span data-stu-id="72ad9-612">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="72ad9-613">15 sekund</span><span class="sxs-lookup"><span data-stu-id="72ad9-613">15 seconds</span></span> | <span data-ttu-id="72ad9-614">Pokud klient neodešle počáteční zprávu handshake v tomto časovém intervalu, připojení je ukončeno.</span><span class="sxs-lookup"><span data-stu-id="72ad9-614">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="72ad9-615">Toto je rozšířené nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake z důvodu závažné latence sítě.</span><span class="sxs-lookup"><span data-stu-id="72ad9-615">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="72ad9-616">Další podrobnosti o procesu handshake naleznete v tématu [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="72ad9-616">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="72ad9-617">15 sekund</span><span class="sxs-lookup"><span data-stu-id="72ad9-617">15 seconds</span></span> | <span data-ttu-id="72ad9-618">Pokud server neodeslal zprávu v tomto intervalu, je automaticky odeslána zpráva ping, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="72ad9-618">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="72ad9-619">Při `KeepAliveInterval`změně změňte `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení klienta.</span><span class="sxs-lookup"><span data-stu-id="72ad9-619">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="72ad9-620">Doporučená `ServerTimeout` / `serverTimeoutInMilliseconds` hodnota je `KeepAliveInterval` dvojnásobek hodnoty.</span><span class="sxs-lookup"><span data-stu-id="72ad9-620">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="72ad9-621">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="72ad9-621">All installed protocols</span></span> | <span data-ttu-id="72ad9-622">Protokoly podporované tímto centrem.</span><span class="sxs-lookup"><span data-stu-id="72ad9-622">Protocols supported by this hub.</span></span> <span data-ttu-id="72ad9-623">Ve výchozím nastavení jsou povoleny všechny protokoly registrované na serveru, ale protokoly mohou být odebrány z tohoto seznamu zakázat konkrétní protokoly pro jednotlivé rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="72ad9-623">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="72ad9-624">Pokud `true`, podrobné zprávy o výjimce jsou vráceny klientům při vyvolání výjimky v Hub metody.</span><span class="sxs-lookup"><span data-stu-id="72ad9-624">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="72ad9-625">Výchozí hodnota `false`je , protože tyto zprávy o výjimce mohou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="72ad9-625">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="72ad9-626">Možnosti lze nakonfigurovat pro všechny rozbočovače `Startup.ConfigureServices`poskytnutím možnosti delegáta volání v aplikaci `AddSignalR` .</span><span class="sxs-lookup"><span data-stu-id="72ad9-626">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="72ad9-627">Možnosti pro jeden rozbočovač přepíší globální možnosti uvedené v `AddSignalR` aplikaci a lze je konfigurovat pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span><span class="sxs-lookup"><span data-stu-id="72ad9-627">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="72ad9-628">Rozšířené možnosti konfigurace protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="72ad9-628">Advanced HTTP configuration options</span></span>

<span data-ttu-id="72ad9-629">Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="72ad9-629">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="72ad9-630">Tyto možnosti jsou konfigurovány předáním delegáta [maphub\<t>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="72ad9-630">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="72ad9-631">Následující tabulka popisuje možnosti konfigurace ASP.NET pokročilých možností protokolu HTTP nástroje Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="72ad9-631">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="72ad9-632">Možnost</span><span class="sxs-lookup"><span data-stu-id="72ad9-632">Option</span></span> | <span data-ttu-id="72ad9-633">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="72ad9-633">Default Value</span></span> | <span data-ttu-id="72ad9-634">Popis</span><span class="sxs-lookup"><span data-stu-id="72ad9-634">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="72ad9-635">32 KB</span><span class="sxs-lookup"><span data-stu-id="72ad9-635">32 KB</span></span> | <span data-ttu-id="72ad9-636">Maximální počet bajtů přijatých od klienta, které server vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="72ad9-636">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="72ad9-637">Zvýšení této hodnoty umožňuje serveru přijímat větší zprávy, ale může negativně ovlivnit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="72ad9-637">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="72ad9-638">Data automaticky získaná z `Authorize` atributů použitých pro třídu Hub.</span><span class="sxs-lookup"><span data-stu-id="72ad9-638">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="72ad9-639">Seznam objektů [IAuthorizeData,](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) které slouží k určení, zda je klient oprávněn připojit k rozbočovači.</span><span class="sxs-lookup"><span data-stu-id="72ad9-639">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="72ad9-640">32 KB</span><span class="sxs-lookup"><span data-stu-id="72ad9-640">32 KB</span></span> | <span data-ttu-id="72ad9-641">Maximální počet bajtů odeslaných aplikací, které server vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="72ad9-641">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="72ad9-642">Zvýšení této hodnoty umožňuje serveru odesílat větší zprávy, ale může negativně ovlivnit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="72ad9-642">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="72ad9-643">Všechny přenosy jsou povoleny.</span><span class="sxs-lookup"><span data-stu-id="72ad9-643">All Transports are enabled.</span></span> | <span data-ttu-id="72ad9-644">Bit příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenosy klienta můžete použít k připojení.</span><span class="sxs-lookup"><span data-stu-id="72ad9-644">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="72ad9-645">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="72ad9-645">See below.</span></span> | <span data-ttu-id="72ad9-646">Další možnosti specifické pro přenos dlouhé dotazování.</span><span class="sxs-lookup"><span data-stu-id="72ad9-646">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="72ad9-647">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="72ad9-647">See below.</span></span> | <span data-ttu-id="72ad9-648">Další možnosti specifické pro přenos WebSockets.</span><span class="sxs-lookup"><span data-stu-id="72ad9-648">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="72ad9-649">Přenos dlouhé dotazování má další možnosti, které lze nakonfigurovat pomocí vlastnosti: `LongPolling`</span><span class="sxs-lookup"><span data-stu-id="72ad9-649">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="72ad9-650">Možnost</span><span class="sxs-lookup"><span data-stu-id="72ad9-650">Option</span></span> | <span data-ttu-id="72ad9-651">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="72ad9-651">Default Value</span></span> | <span data-ttu-id="72ad9-652">Popis</span><span class="sxs-lookup"><span data-stu-id="72ad9-652">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="72ad9-653">90 sekund</span><span class="sxs-lookup"><span data-stu-id="72ad9-653">90 seconds</span></span> | <span data-ttu-id="72ad9-654">Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jednoho požadavku na dotazování.</span><span class="sxs-lookup"><span data-stu-id="72ad9-654">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="72ad9-655">Snížení této hodnoty způsobí, že klient vydávat nové požadavky na dotazování častěji.</span><span class="sxs-lookup"><span data-stu-id="72ad9-655">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="72ad9-656">Přenos WebSocket má další možnosti, `WebSockets` které lze nakonfigurovat pomocí vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="72ad9-656">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="72ad9-657">Možnost</span><span class="sxs-lookup"><span data-stu-id="72ad9-657">Option</span></span> | <span data-ttu-id="72ad9-658">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="72ad9-658">Default Value</span></span> | <span data-ttu-id="72ad9-659">Popis</span><span class="sxs-lookup"><span data-stu-id="72ad9-659">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="72ad9-660">5 sekund</span><span class="sxs-lookup"><span data-stu-id="72ad9-660">5 seconds</span></span> | <span data-ttu-id="72ad9-661">Po ukončení serveru, pokud se klientovi nepodaří zavřít v tomto časovém intervalu, je připojení ukončeno.</span><span class="sxs-lookup"><span data-stu-id="72ad9-661">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="72ad9-662">Delegát, který lze použít `Sec-WebSocket-Protocol` k nastavení záhlaví na vlastní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-662">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="72ad9-663">Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že vrátí požadovanou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-663">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="72ad9-664">Konfigurace možností klienta</span><span class="sxs-lookup"><span data-stu-id="72ad9-664">Configure client options</span></span>

<span data-ttu-id="72ad9-665">Možnosti klienta lze `HubConnectionBuilder` konfigurovat na typu (k dispozici v klientech .NET a JavaScript).</span><span class="sxs-lookup"><span data-stu-id="72ad9-665">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="72ad9-666">Je také k dispozici v klientovi `HttpHubConnectionBuilder` Java, ale podtřída je to, co `HubConnection` obsahuje možnosti konfigurace tvůrce, stejně jako na sobě.</span><span class="sxs-lookup"><span data-stu-id="72ad9-666">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="72ad9-667">Konfigurace protokolování</span><span class="sxs-lookup"><span data-stu-id="72ad9-667">Configure logging</span></span>

<span data-ttu-id="72ad9-668">Protokolování je konfigurováno v `ConfigureLogging` klientovi .NET pomocí metody.</span><span class="sxs-lookup"><span data-stu-id="72ad9-668">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="72ad9-669">Zprostředkovatelé protokolování a filtry mohou být registrovány stejným způsobem jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="72ad9-669">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="72ad9-670">Další informace naleznete v dokumentaci [protokolování ASP.NET jádra.](xref:fundamentals/logging/index)</span><span class="sxs-lookup"><span data-stu-id="72ad9-670">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="72ad9-671">Chcete-li zaregistrovat zprostředkovatele protokolování, je nutné nainstalovat potřebné balíčky.</span><span class="sxs-lookup"><span data-stu-id="72ad9-671">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="72ad9-672">Úplný seznam najdete v části [Vestavěné zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentech.</span><span class="sxs-lookup"><span data-stu-id="72ad9-672">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="72ad9-673">Chcete-li například povolit `Microsoft.Extensions.Logging.Console` protokolování konzoly, nainstalujte balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="72ad9-673">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="72ad9-674">Volání `AddConsole` metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="72ad9-674">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="72ad9-675">V klientovi JavaScript `configureLogging` existuje podobná metoda.</span><span class="sxs-lookup"><span data-stu-id="72ad9-675">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="72ad9-676">Zadejte `LogLevel` hodnotu označující minimální úroveň zpráv protokolu k vytvoření.</span><span class="sxs-lookup"><span data-stu-id="72ad9-676">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="72ad9-677">Protokoly jsou zapsány do okna konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="72ad9-677">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="72ad9-678">Chcete-li protokolování `signalR.LogLevel.None` zcela `configureLogging` zakázat, zadejte v metodě.</span><span class="sxs-lookup"><span data-stu-id="72ad9-678">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="72ad9-679">Další informace o protokolování naleznete v [dokumentaci k diagnostice signalr .](xref:signalr/diagnostics)</span><span class="sxs-lookup"><span data-stu-id="72ad9-679">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="72ad9-680">Klient Java SignalR používá pro protokolování knihovnu [SLF4J.](https://www.slf4j.org/)</span><span class="sxs-lookup"><span data-stu-id="72ad9-680">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="72ad9-681">Jedná se o rozhraní API pro protokolování vysoké úrovně, které umožňuje uživatelům knihovny zvolit vlastní implementaci protokolování tím, že přivedou konkrétní závislost protokolování.</span><span class="sxs-lookup"><span data-stu-id="72ad9-681">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="72ad9-682">Následující fragment kódu ukazuje, jak `java.util.logging` se používat s klientem Java SignalR.</span><span class="sxs-lookup"><span data-stu-id="72ad9-682">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="72ad9-683">Pokud nenakonfigurujete protokolování v závislostech, SLF4J načte výchozí protokolovací nástroj bez operace s následující varovnou zprávou:</span><span class="sxs-lookup"><span data-stu-id="72ad9-683">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="72ad9-684">To lze bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="72ad9-684">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="72ad9-685">Konfigurace povolených přenosů</span><span class="sxs-lookup"><span data-stu-id="72ad9-685">Configure allowed transports</span></span>

<span data-ttu-id="72ad9-686">Přenosy používané SignalR lze konfigurovat `WithUrl` ve`withUrl` volání (v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="72ad9-686">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="72ad9-687">Bitové nebo hodnoty `HttpTransportType` lze omezit klienta používat pouze zadané přenosy.</span><span class="sxs-lookup"><span data-stu-id="72ad9-687">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="72ad9-688">Všechny přenosy jsou ve výchozím nastavení povoleny.</span><span class="sxs-lookup"><span data-stu-id="72ad9-688">All transports are enabled by default.</span></span>

<span data-ttu-id="72ad9-689">Chcete-li například zakázat přenos událostí odeslaných serverem, ale povolit připojení WebSockets a Long Polling:</span><span class="sxs-lookup"><span data-stu-id="72ad9-689">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="72ad9-690">V klientovi JavaScript jsou přenosy `transport` konfigurovány nastavením pole `withUrl`na objektu options, který je k dispozici :</span><span class="sxs-lookup"><span data-stu-id="72ad9-690">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="72ad9-691">V této verzi webových zásuvek klienta Java je pouze dostupný přenos.</span><span class="sxs-lookup"><span data-stu-id="72ad9-691">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="72ad9-692">Konfigurace ověřování nosiče</span><span class="sxs-lookup"><span data-stu-id="72ad9-692">Configure bearer authentication</span></span>

<span data-ttu-id="72ad9-693">Chcete-li poskytnout ověřovací data spolu s `AccessTokenProvider` požadavky`accessTokenFactory` SignalR, použijte možnost ( v Jazyce JavaScript a určete funkci, která vrátí požadovaný přístupový token.</span><span class="sxs-lookup"><span data-stu-id="72ad9-693">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="72ad9-694">V klientovi .NET je tento přístupový token předán jako http "Bearer Authentication" token (Pomocí `Authorization` hlavičky `Bearer`s typem ).</span><span class="sxs-lookup"><span data-stu-id="72ad9-694">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="72ad9-695">V klientovi JavaScript se přístupový token používá jako token Nosiče, **s výjimkou** několika případů, kdy rozhraní API prohlížeče omezují možnost použití záhlaví (konkrétně v požadavcích Události odeslané serverem a WebSockets).</span><span class="sxs-lookup"><span data-stu-id="72ad9-695">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="72ad9-696">V těchto případech je přístupový token k `access_token`dispozici jako hodnota řetězce dotazu .</span><span class="sxs-lookup"><span data-stu-id="72ad9-696">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="72ad9-697">V klientovi .NET `AccessTokenProvider` lze tuto možnost zadat `WithUrl`pomocí možností delegáta v aplikaci :</span><span class="sxs-lookup"><span data-stu-id="72ad9-697">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="72ad9-698">V klientovi JavaScript je přístupový token `accessTokenFactory` konfigurován nastavením `withUrl`pole na objektu options v aplikaci :</span><span class="sxs-lookup"><span data-stu-id="72ad9-698">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="72ad9-699">V klientovi SignalR Java můžete nakonfigurovat token nosiče pro ověřování poskytnutím objektu [HttpHubConnectionBuilder.](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)</span><span class="sxs-lookup"><span data-stu-id="72ad9-699">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="72ad9-700">Použijte [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) poskytnout [RxJava](https://github.com/ReactiveX/RxJava) [single\<string>](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="72ad9-700">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="72ad9-701">S [volánísingle.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), můžete napsat logiku k vytvoření přístupové tokeny pro vašeho klienta.</span><span class="sxs-lookup"><span data-stu-id="72ad9-701">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="72ad9-702">Konfigurace možností časového limitu a udržování v životě</span><span class="sxs-lookup"><span data-stu-id="72ad9-702">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="72ad9-703">Další možnosti pro konfiguraci časového limitu a `HubConnection` chování udržování naživu jsou k dispozici na samotném objektu:</span><span class="sxs-lookup"><span data-stu-id="72ad9-703">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="72ad9-704">.NET</span><span class="sxs-lookup"><span data-stu-id="72ad9-704">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="72ad9-705">Možnost</span><span class="sxs-lookup"><span data-stu-id="72ad9-705">Option</span></span> | <span data-ttu-id="72ad9-706">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="72ad9-706">Default value</span></span> | <span data-ttu-id="72ad9-707">Popis</span><span class="sxs-lookup"><span data-stu-id="72ad9-707">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="72ad9-708">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="72ad9-708">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="72ad9-709">Časový čas pro aktivitu serveru.</span><span class="sxs-lookup"><span data-stu-id="72ad9-709">Timeout for server activity.</span></span> <span data-ttu-id="72ad9-710">Pokud server v tomto intervalu neodeslal zprávu, klient uváží server `Closed` odpojený`onclose` a spustí událost (v Jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="72ad9-710">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="72ad9-711">Tato hodnota musí být dostatečně velká, aby zpráva ping byla odeslána ze serveru **a** přijata klientem v časovém intervalu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-711">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="72ad9-712">Doporučená hodnota je číslo alespoň dvojnásobek `KeepAliveInterval` hodnoty serveru povolit čas pro příkaz ping k doručení.</span><span class="sxs-lookup"><span data-stu-id="72ad9-712">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="72ad9-713">15 sekund</span><span class="sxs-lookup"><span data-stu-id="72ad9-713">15 seconds</span></span> | <span data-ttu-id="72ad9-714">Časový limit pro počáteční server handshake.</span><span class="sxs-lookup"><span data-stu-id="72ad9-714">Timeout for initial server handshake.</span></span> <span data-ttu-id="72ad9-715">Pokud server neodešle odpověď handshake v tomto intervalu, klient zruší `Closed` handshake`onclose` a spustí událost (v Jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="72ad9-715">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="72ad9-716">Toto je rozšířené nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake z důvodu závažné latence sítě.</span><span class="sxs-lookup"><span data-stu-id="72ad9-716">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="72ad9-717">Další podrobnosti o procesu handshake naleznete v tématu [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="72ad9-717">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="72ad9-718">15 sekund</span><span class="sxs-lookup"><span data-stu-id="72ad9-718">15 seconds</span></span> | <span data-ttu-id="72ad9-719">Určuje interval, ve kterém klient odesílá zprávy ping.</span><span class="sxs-lookup"><span data-stu-id="72ad9-719">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="72ad9-720">Odesláním libovolné zprávy z klienta obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-720">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="72ad9-721">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, server považuje klienta za odpojeného.</span><span class="sxs-lookup"><span data-stu-id="72ad9-721">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="72ad9-722">V klientovi .NET jsou hodnoty `TimeSpan` časového času určeny jako hodnoty.</span><span class="sxs-lookup"><span data-stu-id="72ad9-722">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="72ad9-723">JavaScript</span><span class="sxs-lookup"><span data-stu-id="72ad9-723">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="72ad9-724">Možnost</span><span class="sxs-lookup"><span data-stu-id="72ad9-724">Option</span></span> | <span data-ttu-id="72ad9-725">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="72ad9-725">Default value</span></span> | <span data-ttu-id="72ad9-726">Popis</span><span class="sxs-lookup"><span data-stu-id="72ad9-726">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="72ad9-727">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="72ad9-727">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="72ad9-728">Časový čas pro aktivitu serveru.</span><span class="sxs-lookup"><span data-stu-id="72ad9-728">Timeout for server activity.</span></span> <span data-ttu-id="72ad9-729">Pokud server neodeslal zprávu v tomto intervalu, klient považuje server odpojena a spustí `onclose` událost.</span><span class="sxs-lookup"><span data-stu-id="72ad9-729">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="72ad9-730">Tato hodnota musí být dostatečně velká, aby zpráva ping byla odeslána ze serveru **a** přijata klientem v časovém intervalu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-730">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="72ad9-731">Doporučená hodnota je číslo alespoň dvojnásobek `KeepAliveInterval` hodnoty serveru povolit čas pro příkaz ping k doručení.</span><span class="sxs-lookup"><span data-stu-id="72ad9-731">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="72ad9-732">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="72ad9-732">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="72ad9-733">Určuje interval, ve kterém klient odesílá zprávy ping.</span><span class="sxs-lookup"><span data-stu-id="72ad9-733">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="72ad9-734">Odesláním libovolné zprávy z klienta obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-734">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="72ad9-735">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, server považuje klienta za odpojeného.</span><span class="sxs-lookup"><span data-stu-id="72ad9-735">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="72ad9-736">Java</span><span class="sxs-lookup"><span data-stu-id="72ad9-736">Java</span></span>](#tab/java)

| <span data-ttu-id="72ad9-737">Možnost</span><span class="sxs-lookup"><span data-stu-id="72ad9-737">Option</span></span> | <span data-ttu-id="72ad9-738">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="72ad9-738">Default value</span></span> | <span data-ttu-id="72ad9-739">Popis</span><span class="sxs-lookup"><span data-stu-id="72ad9-739">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="72ad9-740">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="72ad9-740">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="72ad9-741">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="72ad9-741">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="72ad9-742">Časový čas pro aktivitu serveru.</span><span class="sxs-lookup"><span data-stu-id="72ad9-742">Timeout for server activity.</span></span> <span data-ttu-id="72ad9-743">Pokud server neodeslal zprávu v tomto intervalu, klient považuje server odpojena a spustí `onClose` událost.</span><span class="sxs-lookup"><span data-stu-id="72ad9-743">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="72ad9-744">Tato hodnota musí být dostatečně velká, aby zpráva ping byla odeslána ze serveru **a** přijata klientem v časovém intervalu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-744">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="72ad9-745">Doporučená hodnota je číslo alespoň dvojnásobek `KeepAliveInterval` hodnoty serveru povolit čas pro příkaz ping k doručení.</span><span class="sxs-lookup"><span data-stu-id="72ad9-745">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="72ad9-746">15 sekund</span><span class="sxs-lookup"><span data-stu-id="72ad9-746">15 seconds</span></span> | <span data-ttu-id="72ad9-747">Časový limit pro počáteční server handshake.</span><span class="sxs-lookup"><span data-stu-id="72ad9-747">Timeout for initial server handshake.</span></span> <span data-ttu-id="72ad9-748">Pokud server neodešle odpověď handshake v tomto intervalu, klient zruší `onClose` handshake a spustí událost.</span><span class="sxs-lookup"><span data-stu-id="72ad9-748">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="72ad9-749">Toto je rozšířené nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake z důvodu závažné latence sítě.</span><span class="sxs-lookup"><span data-stu-id="72ad9-749">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="72ad9-750">Další podrobnosti o procesu handshake naleznete v tématu [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="72ad9-750">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="72ad9-751">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="72ad9-751">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="72ad9-752">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="72ad9-752">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="72ad9-753">Určuje interval, ve kterém klient odesílá zprávy ping.</span><span class="sxs-lookup"><span data-stu-id="72ad9-753">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="72ad9-754">Odesláním libovolné zprávy z klienta obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-754">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="72ad9-755">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, server považuje klienta za odpojeného.</span><span class="sxs-lookup"><span data-stu-id="72ad9-755">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="72ad9-756">Konfigurace dalších možností</span><span class="sxs-lookup"><span data-stu-id="72ad9-756">Configure additional options</span></span>

<span data-ttu-id="72ad9-757">Další možnosti lze `WithUrl` konfigurovat`withUrl` v metodě `HubConnectionBuilder` (v Jazyce JavaScript) `HttpHubConnectionBuilder` na různých konfiguračních API v klientovi java:</span><span class="sxs-lookup"><span data-stu-id="72ad9-757">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="72ad9-758">.NET</span><span class="sxs-lookup"><span data-stu-id="72ad9-758">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="72ad9-759">Možnost .NET</span><span class="sxs-lookup"><span data-stu-id="72ad9-759">.NET Option</span></span> |  <span data-ttu-id="72ad9-760">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="72ad9-760">Default value</span></span> | <span data-ttu-id="72ad9-761">Popis</span><span class="sxs-lookup"><span data-stu-id="72ad9-761">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="72ad9-762">Funkce vracející řetězec, který je k dispozici jako ověřovací token Nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="72ad9-762">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="72ad9-763">Nastavte tuto `true` na přeskočení kroku vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="72ad9-763">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="72ad9-764">**Podporováno pouze v případě, že přenos WebSockets je pouze povolený přenos**.</span><span class="sxs-lookup"><span data-stu-id="72ad9-764">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="72ad9-765">Toto nastavení nelze povolit při použití služby Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="72ad9-765">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="72ad9-766">Prázdné</span><span class="sxs-lookup"><span data-stu-id="72ad9-766">Empty</span></span> | <span data-ttu-id="72ad9-767">Kolekce certifikátů TLS k odeslání k ověření požadavků.</span><span class="sxs-lookup"><span data-stu-id="72ad9-767">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="72ad9-768">Prázdné</span><span class="sxs-lookup"><span data-stu-id="72ad9-768">Empty</span></span> | <span data-ttu-id="72ad9-769">Kolekce souborů cookie HTTP, které se mají odesílat s každým požadavkem HTTP.</span><span class="sxs-lookup"><span data-stu-id="72ad9-769">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="72ad9-770">Prázdné</span><span class="sxs-lookup"><span data-stu-id="72ad9-770">Empty</span></span> | <span data-ttu-id="72ad9-771">Pověření k odeslání s každým požadavkem HTTP.</span><span class="sxs-lookup"><span data-stu-id="72ad9-771">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="72ad9-772">5 sekund</span><span class="sxs-lookup"><span data-stu-id="72ad9-772">5 seconds</span></span> | <span data-ttu-id="72ad9-773">Pouze websockets.</span><span class="sxs-lookup"><span data-stu-id="72ad9-773">WebSockets only.</span></span> <span data-ttu-id="72ad9-774">Maximální doba, po kterou klient čeká po zavření, aby server potvrdil požadavek na uzavření.</span><span class="sxs-lookup"><span data-stu-id="72ad9-774">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="72ad9-775">Pokud server nepotvrdí uzavření během této doby, klient se odpojí.</span><span class="sxs-lookup"><span data-stu-id="72ad9-775">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="72ad9-776">Prázdné</span><span class="sxs-lookup"><span data-stu-id="72ad9-776">Empty</span></span> | <span data-ttu-id="72ad9-777">Mapa dalších hlaviček PROTOKOLU HTTP, které se mají odesílat s každým požadavkem HTTP.</span><span class="sxs-lookup"><span data-stu-id="72ad9-777">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="72ad9-778">Delegát, který lze použít ke `HttpMessageHandler` konfiguraci nebo nahrazení použitých k odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="72ad9-778">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="72ad9-779">Nepoužívá se pro připojení WebSocket.</span><span class="sxs-lookup"><span data-stu-id="72ad9-779">Not used for WebSocket connections.</span></span> <span data-ttu-id="72ad9-780">Tento delegát musí vrátit hodnotu bez hodnoty null a obdrží výchozí hodnotu jako parametr.</span><span class="sxs-lookup"><span data-stu-id="72ad9-780">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="72ad9-781">Buď upravte nastavení této výchozí hodnoty a `HttpMessageHandler` vraťte ji, nebo vraťte novou instanci.</span><span class="sxs-lookup"><span data-stu-id="72ad9-781">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="72ad9-782">**Při výměně obslužné rutiny nezapomeňte zkopírovat nastavení, které chcete zachovat z poskytnuté obslužné rutiny, jinak nakonfigurované možnosti (například soubory cookie a záhlaví) se na novou obslužnou rutinu nevztahují.**</span><span class="sxs-lookup"><span data-stu-id="72ad9-782">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="72ad9-783">Proxy HTTP, který se má použít při odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="72ad9-783">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="72ad9-784">Nastavte tuto logickou hodnotu tak, aby odesílala výchozí pověření pro požadavky HTTP a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="72ad9-784">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="72ad9-785">To umožňuje použití ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="72ad9-785">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="72ad9-786">Delegát, který lze použít ke konfiguraci dalších možností WebSocket.</span><span class="sxs-lookup"><span data-stu-id="72ad9-786">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="72ad9-787">Přijímá instanci [ClientWebSocketOptions,](/dotnet/api/system.net.websockets.clientwebsocketoptions) která může být použita ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="72ad9-787">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="72ad9-788">JavaScript</span><span class="sxs-lookup"><span data-stu-id="72ad9-788">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="72ad9-789">Možnost JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="72ad9-789">JavaScript Option</span></span> | <span data-ttu-id="72ad9-790">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="72ad9-790">Default Value</span></span> | <span data-ttu-id="72ad9-791">Popis</span><span class="sxs-lookup"><span data-stu-id="72ad9-791">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="72ad9-792">Funkce vracející řetězec, který je k dispozici jako ověřovací token Nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="72ad9-792">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="72ad9-793">Nastavte tuto `true` na přeskočení kroku vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="72ad9-793">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="72ad9-794">**Podporováno pouze v případě, že přenos WebSockets je pouze povolený přenos**.</span><span class="sxs-lookup"><span data-stu-id="72ad9-794">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="72ad9-795">Toto nastavení nelze povolit při použití služby Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="72ad9-795">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="72ad9-796">Java</span><span class="sxs-lookup"><span data-stu-id="72ad9-796">Java</span></span>](#tab/java)

| <span data-ttu-id="72ad9-797">Java možnost</span><span class="sxs-lookup"><span data-stu-id="72ad9-797">Java Option</span></span> | <span data-ttu-id="72ad9-798">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="72ad9-798">Default Value</span></span> | <span data-ttu-id="72ad9-799">Popis</span><span class="sxs-lookup"><span data-stu-id="72ad9-799">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="72ad9-800">Funkce vracející řetězec, který je k dispozici jako ověřovací token Nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="72ad9-800">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="72ad9-801">Nastavte tuto `true` na přeskočení kroku vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="72ad9-801">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="72ad9-802">**Podporováno pouze v případě, že přenos WebSockets je pouze povolený přenos**.</span><span class="sxs-lookup"><span data-stu-id="72ad9-802">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="72ad9-803">Toto nastavení nelze povolit při použití služby Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="72ad9-803">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="72ad9-804">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="72ad9-804">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="72ad9-805">Prázdné</span><span class="sxs-lookup"><span data-stu-id="72ad9-805">Empty</span></span> | <span data-ttu-id="72ad9-806">Mapa dalších hlaviček PROTOKOLU HTTP, které se mají odesílat s každým požadavkem HTTP.</span><span class="sxs-lookup"><span data-stu-id="72ad9-806">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="72ad9-807">V klientovi .NET mohou být tyto možnosti změněny možnostmi, které delegát poskytuje `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="72ad9-807">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="72ad9-808">V klientovi JavaScript mohou být tyto možnosti poskytnuty `withUrl`v objektu JavaScript u :</span><span class="sxs-lookup"><span data-stu-id="72ad9-808">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="72ad9-809">V klientovi Java lze tyto možnosti nakonfigurovat pomocí metod na vráceném `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="72ad9-809">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="72ad9-810">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="72ad9-810">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="72ad9-811">Možnosti serializace balíčku JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="72ad9-811">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="72ad9-812">ASP.NET Core SignalR podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="72ad9-812">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="72ad9-813">Každý protokol má možnosti konfigurace serializace.</span><span class="sxs-lookup"><span data-stu-id="72ad9-813">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="72ad9-814">Serializaci JSON lze na serveru nakonfigurovat pomocí metody rozšíření [AddJsonProtocol,](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) kterou `Startup.ConfigureServices` lze přidat za [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) ve vaší metodě.</span><span class="sxs-lookup"><span data-stu-id="72ad9-814">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="72ad9-815">Metoda `AddJsonProtocol` trvá delegáta, který `options` obdrží objekt.</span><span class="sxs-lookup"><span data-stu-id="72ad9-815">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="72ad9-816">[Vlastnost PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) u tohoto objektu je JSON.NET `JsonSerializerSettings` objekt, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="72ad9-816">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="72ad9-817">Další informace naleznete v [dokumentaci k JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="72ad9-817">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="72ad9-818">Jako příklad nakonfigurujte serializátor tak, aby používal názvy vlastností "PascalCase", `Startup.ConfigureServices`namísto výchozích názvů camelCase, použijte následující kód v aplikaci :</span><span class="sxs-lookup"><span data-stu-id="72ad9-818">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="72ad9-819">V klientovi .NET `AddJsonProtocol` existuje stejná metoda rozšíření v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="72ad9-819">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="72ad9-820">Obor `Microsoft.Extensions.DependencyInjection` názvů musí být importován, aby se vyřešila metoda rozšíření:</span><span class="sxs-lookup"><span data-stu-id="72ad9-820">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="72ad9-821">V tuto chvíli není možné konfigurovat serializaci JSON v klientovi JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-821">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="72ad9-822">Možnosti serializace messagepacku</span><span class="sxs-lookup"><span data-stu-id="72ad9-822">MessagePack serialization options</span></span>

<span data-ttu-id="72ad9-823">Serializace messagepack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)</span><span class="sxs-lookup"><span data-stu-id="72ad9-823">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="72ad9-824">Další podrobnosti najdete [v tématu MessagePack v SignalR.](xref:signalr/messagepackhubprotocol)</span><span class="sxs-lookup"><span data-stu-id="72ad9-824">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="72ad9-825">V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-825">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="72ad9-826">Konfigurace možností serveru</span><span class="sxs-lookup"><span data-stu-id="72ad9-826">Configure server options</span></span>

<span data-ttu-id="72ad9-827">Následující tabulka popisuje možnosti konfigurace rozbočovačů SignalR:</span><span class="sxs-lookup"><span data-stu-id="72ad9-827">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="72ad9-828">Možnost</span><span class="sxs-lookup"><span data-stu-id="72ad9-828">Option</span></span> | <span data-ttu-id="72ad9-829">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="72ad9-829">Default Value</span></span> | <span data-ttu-id="72ad9-830">Popis</span><span class="sxs-lookup"><span data-stu-id="72ad9-830">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="72ad9-831">15 sekund</span><span class="sxs-lookup"><span data-stu-id="72ad9-831">15 seconds</span></span> | <span data-ttu-id="72ad9-832">Pokud klient neodešle počáteční zprávu handshake v tomto časovém intervalu, připojení je ukončeno.</span><span class="sxs-lookup"><span data-stu-id="72ad9-832">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="72ad9-833">Toto je rozšířené nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake z důvodu závažné latence sítě.</span><span class="sxs-lookup"><span data-stu-id="72ad9-833">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="72ad9-834">Další podrobnosti o procesu handshake naleznete v tématu [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="72ad9-834">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="72ad9-835">15 sekund</span><span class="sxs-lookup"><span data-stu-id="72ad9-835">15 seconds</span></span> | <span data-ttu-id="72ad9-836">Pokud server neodeslal zprávu v tomto intervalu, je automaticky odeslána zpráva ping, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="72ad9-836">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="72ad9-837">Při `KeepAliveInterval`změně změňte `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení klienta.</span><span class="sxs-lookup"><span data-stu-id="72ad9-837">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="72ad9-838">Doporučená `ServerTimeout` / `serverTimeoutInMilliseconds` hodnota je `KeepAliveInterval` dvojnásobek hodnoty.</span><span class="sxs-lookup"><span data-stu-id="72ad9-838">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="72ad9-839">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="72ad9-839">All installed protocols</span></span> | <span data-ttu-id="72ad9-840">Protokoly podporované tímto centrem.</span><span class="sxs-lookup"><span data-stu-id="72ad9-840">Protocols supported by this hub.</span></span> <span data-ttu-id="72ad9-841">Ve výchozím nastavení jsou povoleny všechny protokoly registrované na serveru, ale protokoly mohou být odebrány z tohoto seznamu zakázat konkrétní protokoly pro jednotlivé rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="72ad9-841">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="72ad9-842">Pokud `true`, podrobné zprávy o výjimce jsou vráceny klientům při vyvolání výjimky v Hub metody.</span><span class="sxs-lookup"><span data-stu-id="72ad9-842">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="72ad9-843">Výchozí hodnota `false`je , protože tyto zprávy o výjimce mohou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="72ad9-843">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="72ad9-844">Možnosti lze nakonfigurovat pro všechny rozbočovače `Startup.ConfigureServices`poskytnutím možnosti delegáta volání v aplikaci `AddSignalR` .</span><span class="sxs-lookup"><span data-stu-id="72ad9-844">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="72ad9-845">Možnosti pro jeden rozbočovač přepíší globální možnosti uvedené v `AddSignalR` aplikaci a lze je konfigurovat pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span><span class="sxs-lookup"><span data-stu-id="72ad9-845">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="72ad9-846">Rozšířené možnosti konfigurace protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="72ad9-846">Advanced HTTP configuration options</span></span>

<span data-ttu-id="72ad9-847">Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="72ad9-847">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="72ad9-848">Tyto možnosti jsou konfigurovány předáním delegáta [maphub\<t>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="72ad9-848">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="72ad9-849">Následující tabulka popisuje možnosti konfigurace ASP.NET pokročilých možností protokolu HTTP nástroje Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="72ad9-849">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="72ad9-850">Možnost</span><span class="sxs-lookup"><span data-stu-id="72ad9-850">Option</span></span> | <span data-ttu-id="72ad9-851">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="72ad9-851">Default Value</span></span> | <span data-ttu-id="72ad9-852">Popis</span><span class="sxs-lookup"><span data-stu-id="72ad9-852">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="72ad9-853">32 KB</span><span class="sxs-lookup"><span data-stu-id="72ad9-853">32 KB</span></span> | <span data-ttu-id="72ad9-854">Maximální počet bajtů přijatých od klienta, které server vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="72ad9-854">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="72ad9-855">Zvýšení této hodnoty umožňuje serveru přijímat větší zprávy, ale může negativně ovlivnit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="72ad9-855">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="72ad9-856">Data automaticky získaná z `Authorize` atributů použitých pro třídu Hub.</span><span class="sxs-lookup"><span data-stu-id="72ad9-856">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="72ad9-857">Seznam objektů [IAuthorizeData,](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) které slouží k určení, zda je klient oprávněn připojit k rozbočovači.</span><span class="sxs-lookup"><span data-stu-id="72ad9-857">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="72ad9-858">32 KB</span><span class="sxs-lookup"><span data-stu-id="72ad9-858">32 KB</span></span> | <span data-ttu-id="72ad9-859">Maximální počet bajtů odeslaných aplikací, které server vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="72ad9-859">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="72ad9-860">Zvýšení této hodnoty umožňuje serveru odesílat větší zprávy, ale může negativně ovlivnit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="72ad9-860">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="72ad9-861">Všechny přenosy jsou povoleny.</span><span class="sxs-lookup"><span data-stu-id="72ad9-861">All Transports are enabled.</span></span> | <span data-ttu-id="72ad9-862">Bit příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenosy klienta můžete použít k připojení.</span><span class="sxs-lookup"><span data-stu-id="72ad9-862">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="72ad9-863">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="72ad9-863">See below.</span></span> | <span data-ttu-id="72ad9-864">Další možnosti specifické pro přenos dlouhé dotazování.</span><span class="sxs-lookup"><span data-stu-id="72ad9-864">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="72ad9-865">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="72ad9-865">See below.</span></span> | <span data-ttu-id="72ad9-866">Další možnosti specifické pro přenos WebSockets.</span><span class="sxs-lookup"><span data-stu-id="72ad9-866">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="72ad9-867">Přenos dlouhé dotazování má další možnosti, které lze nakonfigurovat pomocí vlastnosti: `LongPolling`</span><span class="sxs-lookup"><span data-stu-id="72ad9-867">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="72ad9-868">Možnost</span><span class="sxs-lookup"><span data-stu-id="72ad9-868">Option</span></span> | <span data-ttu-id="72ad9-869">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="72ad9-869">Default Value</span></span> | <span data-ttu-id="72ad9-870">Popis</span><span class="sxs-lookup"><span data-stu-id="72ad9-870">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="72ad9-871">90 sekund</span><span class="sxs-lookup"><span data-stu-id="72ad9-871">90 seconds</span></span> | <span data-ttu-id="72ad9-872">Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jednoho požadavku na dotazování.</span><span class="sxs-lookup"><span data-stu-id="72ad9-872">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="72ad9-873">Snížení této hodnoty způsobí, že klient vydávat nové požadavky na dotazování častěji.</span><span class="sxs-lookup"><span data-stu-id="72ad9-873">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="72ad9-874">Přenos WebSocket má další možnosti, `WebSockets` které lze nakonfigurovat pomocí vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="72ad9-874">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="72ad9-875">Možnost</span><span class="sxs-lookup"><span data-stu-id="72ad9-875">Option</span></span> | <span data-ttu-id="72ad9-876">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="72ad9-876">Default Value</span></span> | <span data-ttu-id="72ad9-877">Popis</span><span class="sxs-lookup"><span data-stu-id="72ad9-877">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="72ad9-878">5 sekund</span><span class="sxs-lookup"><span data-stu-id="72ad9-878">5 seconds</span></span> | <span data-ttu-id="72ad9-879">Po ukončení serveru, pokud se klientovi nepodaří zavřít v tomto časovém intervalu, je připojení ukončeno.</span><span class="sxs-lookup"><span data-stu-id="72ad9-879">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="72ad9-880">Delegát, který lze použít `Sec-WebSocket-Protocol` k nastavení záhlaví na vlastní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-880">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="72ad9-881">Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že vrátí požadovanou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-881">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="72ad9-882">Konfigurace možností klienta</span><span class="sxs-lookup"><span data-stu-id="72ad9-882">Configure client options</span></span>

<span data-ttu-id="72ad9-883">Možnosti klienta lze `HubConnectionBuilder` konfigurovat na typu (k dispozici v klientech .NET a JavaScript).</span><span class="sxs-lookup"><span data-stu-id="72ad9-883">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="72ad9-884">Je také k dispozici v klientovi `HttpHubConnectionBuilder` Java, ale podtřída je to, co `HubConnection` obsahuje možnosti konfigurace tvůrce, stejně jako na sobě.</span><span class="sxs-lookup"><span data-stu-id="72ad9-884">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="72ad9-885">Konfigurace protokolování</span><span class="sxs-lookup"><span data-stu-id="72ad9-885">Configure logging</span></span>

<span data-ttu-id="72ad9-886">Protokolování je konfigurováno v `ConfigureLogging` klientovi .NET pomocí metody.</span><span class="sxs-lookup"><span data-stu-id="72ad9-886">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="72ad9-887">Zprostředkovatelé protokolování a filtry mohou být registrovány stejným způsobem jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="72ad9-887">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="72ad9-888">Další informace naleznete v dokumentaci [protokolování ASP.NET jádra.](xref:fundamentals/logging/index)</span><span class="sxs-lookup"><span data-stu-id="72ad9-888">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="72ad9-889">Chcete-li zaregistrovat zprostředkovatele protokolování, je nutné nainstalovat potřebné balíčky.</span><span class="sxs-lookup"><span data-stu-id="72ad9-889">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="72ad9-890">Úplný seznam najdete v části [Vestavěné zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentech.</span><span class="sxs-lookup"><span data-stu-id="72ad9-890">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="72ad9-891">Chcete-li například povolit `Microsoft.Extensions.Logging.Console` protokolování konzoly, nainstalujte balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="72ad9-891">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="72ad9-892">Volání `AddConsole` metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="72ad9-892">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="72ad9-893">V klientovi JavaScript `configureLogging` existuje podobná metoda.</span><span class="sxs-lookup"><span data-stu-id="72ad9-893">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="72ad9-894">Zadejte `LogLevel` hodnotu označující minimální úroveň zpráv protokolu k vytvoření.</span><span class="sxs-lookup"><span data-stu-id="72ad9-894">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="72ad9-895">Protokoly jsou zapsány do okna konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="72ad9-895">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="72ad9-896">Chcete-li protokolování `signalR.LogLevel.None` zcela `configureLogging` zakázat, zadejte v metodě.</span><span class="sxs-lookup"><span data-stu-id="72ad9-896">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="72ad9-897">Další informace o protokolování naleznete v [dokumentaci k diagnostice signalr .](xref:signalr/diagnostics)</span><span class="sxs-lookup"><span data-stu-id="72ad9-897">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="72ad9-898">Klient Java SignalR používá pro protokolování knihovnu [SLF4J.](https://www.slf4j.org/)</span><span class="sxs-lookup"><span data-stu-id="72ad9-898">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="72ad9-899">Jedná se o rozhraní API pro protokolování vysoké úrovně, které umožňuje uživatelům knihovny zvolit vlastní implementaci protokolování tím, že přivedou konkrétní závislost protokolování.</span><span class="sxs-lookup"><span data-stu-id="72ad9-899">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="72ad9-900">Následující fragment kódu ukazuje, jak `java.util.logging` se používat s klientem Java SignalR.</span><span class="sxs-lookup"><span data-stu-id="72ad9-900">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="72ad9-901">Pokud nenakonfigurujete protokolování v závislostech, SLF4J načte výchozí protokolovací nástroj bez operace s následující varovnou zprávou:</span><span class="sxs-lookup"><span data-stu-id="72ad9-901">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="72ad9-902">To lze bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="72ad9-902">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="72ad9-903">Konfigurace povolených přenosů</span><span class="sxs-lookup"><span data-stu-id="72ad9-903">Configure allowed transports</span></span>

<span data-ttu-id="72ad9-904">Přenosy používané SignalR lze konfigurovat `WithUrl` ve`withUrl` volání (v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="72ad9-904">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="72ad9-905">Bitové nebo hodnoty `HttpTransportType` lze omezit klienta používat pouze zadané přenosy.</span><span class="sxs-lookup"><span data-stu-id="72ad9-905">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="72ad9-906">Všechny přenosy jsou ve výchozím nastavení povoleny.</span><span class="sxs-lookup"><span data-stu-id="72ad9-906">All transports are enabled by default.</span></span>

<span data-ttu-id="72ad9-907">Chcete-li například zakázat přenos událostí odeslaných serverem, ale povolit připojení WebSockets a Long Polling:</span><span class="sxs-lookup"><span data-stu-id="72ad9-907">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="72ad9-908">V klientovi JavaScript jsou přenosy `transport` konfigurovány nastavením pole `withUrl`na objektu options, který je k dispozici :</span><span class="sxs-lookup"><span data-stu-id="72ad9-908">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="72ad9-909">Konfigurace ověřování nosiče</span><span class="sxs-lookup"><span data-stu-id="72ad9-909">Configure bearer authentication</span></span>

<span data-ttu-id="72ad9-910">Chcete-li poskytnout ověřovací data spolu s `AccessTokenProvider` požadavky`accessTokenFactory` SignalR, použijte možnost ( v Jazyce JavaScript a určete funkci, která vrátí požadovaný přístupový token.</span><span class="sxs-lookup"><span data-stu-id="72ad9-910">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="72ad9-911">V klientovi .NET je tento přístupový token předán jako http "Bearer Authentication" token (Pomocí `Authorization` hlavičky `Bearer`s typem ).</span><span class="sxs-lookup"><span data-stu-id="72ad9-911">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="72ad9-912">V klientovi JavaScript se přístupový token používá jako token Nosiče, **s výjimkou** několika případů, kdy rozhraní API prohlížeče omezují možnost použití záhlaví (konkrétně v požadavcích Události odeslané serverem a WebSockets).</span><span class="sxs-lookup"><span data-stu-id="72ad9-912">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="72ad9-913">V těchto případech je přístupový token k `access_token`dispozici jako hodnota řetězce dotazu .</span><span class="sxs-lookup"><span data-stu-id="72ad9-913">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="72ad9-914">V klientovi .NET `AccessTokenProvider` lze tuto možnost zadat `WithUrl`pomocí možností delegáta v aplikaci :</span><span class="sxs-lookup"><span data-stu-id="72ad9-914">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="72ad9-915">V klientovi JavaScript je přístupový token `accessTokenFactory` konfigurován nastavením `withUrl`pole na objektu options v aplikaci :</span><span class="sxs-lookup"><span data-stu-id="72ad9-915">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="72ad9-916">V klientovi SignalR Java můžete nakonfigurovat token nosiče pro ověřování poskytnutím objektu [HttpHubConnectionBuilder.](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)</span><span class="sxs-lookup"><span data-stu-id="72ad9-916">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="72ad9-917">Použijte [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) poskytnout [RxJava](https://github.com/ReactiveX/RxJava) [single\<string>](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="72ad9-917">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="72ad9-918">S [volánísingle.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), můžete napsat logiku k vytvoření přístupové tokeny pro vašeho klienta.</span><span class="sxs-lookup"><span data-stu-id="72ad9-918">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="72ad9-919">Konfigurace možností časového limitu a udržování v životě</span><span class="sxs-lookup"><span data-stu-id="72ad9-919">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="72ad9-920">Další možnosti pro konfiguraci časového limitu a `HubConnection` chování udržování naživu jsou k dispozici na samotném objektu:</span><span class="sxs-lookup"><span data-stu-id="72ad9-920">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="72ad9-921">.NET</span><span class="sxs-lookup"><span data-stu-id="72ad9-921">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="72ad9-922">Možnost</span><span class="sxs-lookup"><span data-stu-id="72ad9-922">Option</span></span> | <span data-ttu-id="72ad9-923">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="72ad9-923">Default value</span></span> | <span data-ttu-id="72ad9-924">Popis</span><span class="sxs-lookup"><span data-stu-id="72ad9-924">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="72ad9-925">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="72ad9-925">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="72ad9-926">Časový čas pro aktivitu serveru.</span><span class="sxs-lookup"><span data-stu-id="72ad9-926">Timeout for server activity.</span></span> <span data-ttu-id="72ad9-927">Pokud server v tomto intervalu neodeslal zprávu, klient uváží server `Closed` odpojený`onclose` a spustí událost (v Jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="72ad9-927">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="72ad9-928">Tato hodnota musí být dostatečně velká, aby zpráva ping byla odeslána ze serveru **a** přijata klientem v časovém intervalu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-928">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="72ad9-929">Doporučená hodnota je číslo alespoň dvojnásobek `KeepAliveInterval` hodnoty serveru povolit čas pro příkaz ping k doručení.</span><span class="sxs-lookup"><span data-stu-id="72ad9-929">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="72ad9-930">15 sekund</span><span class="sxs-lookup"><span data-stu-id="72ad9-930">15 seconds</span></span> | <span data-ttu-id="72ad9-931">Časový limit pro počáteční server handshake.</span><span class="sxs-lookup"><span data-stu-id="72ad9-931">Timeout for initial server handshake.</span></span> <span data-ttu-id="72ad9-932">Pokud server neodešle odpověď handshake v tomto intervalu, klient zruší `Closed` handshake`onclose` a spustí událost (v Jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="72ad9-932">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="72ad9-933">Toto je rozšířené nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake z důvodu závažné latence sítě.</span><span class="sxs-lookup"><span data-stu-id="72ad9-933">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="72ad9-934">Další podrobnosti o procesu handshake naleznete v tématu [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="72ad9-934">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="72ad9-935">V klientovi .NET jsou hodnoty `TimeSpan` časového času určeny jako hodnoty.</span><span class="sxs-lookup"><span data-stu-id="72ad9-935">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="72ad9-936">JavaScript</span><span class="sxs-lookup"><span data-stu-id="72ad9-936">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="72ad9-937">Možnost</span><span class="sxs-lookup"><span data-stu-id="72ad9-937">Option</span></span> | <span data-ttu-id="72ad9-938">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="72ad9-938">Default value</span></span> | <span data-ttu-id="72ad9-939">Popis</span><span class="sxs-lookup"><span data-stu-id="72ad9-939">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="72ad9-940">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="72ad9-940">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="72ad9-941">Časový čas pro aktivitu serveru.</span><span class="sxs-lookup"><span data-stu-id="72ad9-941">Timeout for server activity.</span></span> <span data-ttu-id="72ad9-942">Pokud server neodeslal zprávu v tomto intervalu, klient považuje server odpojena a spustí `onclose` událost.</span><span class="sxs-lookup"><span data-stu-id="72ad9-942">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="72ad9-943">Tato hodnota musí být dostatečně velká, aby zpráva ping byla odeslána ze serveru **a** přijata klientem v časovém intervalu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-943">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="72ad9-944">Doporučená hodnota je číslo alespoň dvojnásobek `KeepAliveInterval` hodnoty serveru povolit čas pro příkaz ping k doručení.</span><span class="sxs-lookup"><span data-stu-id="72ad9-944">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="72ad9-945">Java</span><span class="sxs-lookup"><span data-stu-id="72ad9-945">Java</span></span>](#tab/java)

| <span data-ttu-id="72ad9-946">Možnost</span><span class="sxs-lookup"><span data-stu-id="72ad9-946">Option</span></span> | <span data-ttu-id="72ad9-947">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="72ad9-947">Default value</span></span> | <span data-ttu-id="72ad9-948">Popis</span><span class="sxs-lookup"><span data-stu-id="72ad9-948">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="72ad9-949">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="72ad9-949">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="72ad9-950">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="72ad9-950">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="72ad9-951">Časový čas pro aktivitu serveru.</span><span class="sxs-lookup"><span data-stu-id="72ad9-951">Timeout for server activity.</span></span> <span data-ttu-id="72ad9-952">Pokud server neodeslal zprávu v tomto intervalu, klient považuje server odpojena a spustí `onClose` událost.</span><span class="sxs-lookup"><span data-stu-id="72ad9-952">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="72ad9-953">Tato hodnota musí být dostatečně velká, aby zpráva ping byla odeslána ze serveru **a** přijata klientem v časovém intervalu.</span><span class="sxs-lookup"><span data-stu-id="72ad9-953">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="72ad9-954">Doporučená hodnota je číslo alespoň dvojnásobek `KeepAliveInterval` hodnoty serveru, aby čas pro příkaz ping k doručení.</span><span class="sxs-lookup"><span data-stu-id="72ad9-954">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="72ad9-955">15 sekund</span><span class="sxs-lookup"><span data-stu-id="72ad9-955">15 seconds</span></span> | <span data-ttu-id="72ad9-956">Časový limit pro počáteční server handshake.</span><span class="sxs-lookup"><span data-stu-id="72ad9-956">Timeout for initial server handshake.</span></span> <span data-ttu-id="72ad9-957">Pokud server neodešle odpověď handshake v tomto intervalu, klient zruší `onClose` handshake a spustí událost.</span><span class="sxs-lookup"><span data-stu-id="72ad9-957">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="72ad9-958">Toto je rozšířené nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake z důvodu závažné latence sítě.</span><span class="sxs-lookup"><span data-stu-id="72ad9-958">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="72ad9-959">Další podrobnosti o procesu handshake naleznete v tématu [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="72ad9-959">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="72ad9-960">Konfigurace dalších možností</span><span class="sxs-lookup"><span data-stu-id="72ad9-960">Configure additional options</span></span>

<span data-ttu-id="72ad9-961">Další možnosti lze `WithUrl` konfigurovat`withUrl` v metodě `HubConnectionBuilder` (v Jazyce JavaScript) `HttpHubConnectionBuilder` na různých konfiguračních API v klientovi java:</span><span class="sxs-lookup"><span data-stu-id="72ad9-961">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="72ad9-962">.NET</span><span class="sxs-lookup"><span data-stu-id="72ad9-962">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="72ad9-963">Možnost .NET</span><span class="sxs-lookup"><span data-stu-id="72ad9-963">.NET Option</span></span> |  <span data-ttu-id="72ad9-964">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="72ad9-964">Default value</span></span> | <span data-ttu-id="72ad9-965">Popis</span><span class="sxs-lookup"><span data-stu-id="72ad9-965">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="72ad9-966">Funkce vracející řetězec, který je k dispozici jako ověřovací token Nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="72ad9-966">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="72ad9-967">Nastavte tuto `true` na přeskočení kroku vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="72ad9-967">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="72ad9-968">**Podporováno pouze v případě, že přenos WebSockets je pouze povolený přenos**.</span><span class="sxs-lookup"><span data-stu-id="72ad9-968">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="72ad9-969">Toto nastavení nelze povolit při použití služby Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="72ad9-969">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="72ad9-970">Prázdné</span><span class="sxs-lookup"><span data-stu-id="72ad9-970">Empty</span></span> | <span data-ttu-id="72ad9-971">Kolekce certifikátů TLS k odeslání k ověření požadavků.</span><span class="sxs-lookup"><span data-stu-id="72ad9-971">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="72ad9-972">Prázdné</span><span class="sxs-lookup"><span data-stu-id="72ad9-972">Empty</span></span> | <span data-ttu-id="72ad9-973">Kolekce souborů cookie HTTP, které se mají odesílat s každým požadavkem HTTP.</span><span class="sxs-lookup"><span data-stu-id="72ad9-973">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="72ad9-974">Prázdné</span><span class="sxs-lookup"><span data-stu-id="72ad9-974">Empty</span></span> | <span data-ttu-id="72ad9-975">Pověření k odeslání s každým požadavkem HTTP.</span><span class="sxs-lookup"><span data-stu-id="72ad9-975">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="72ad9-976">5 sekund</span><span class="sxs-lookup"><span data-stu-id="72ad9-976">5 seconds</span></span> | <span data-ttu-id="72ad9-977">Pouze websockets.</span><span class="sxs-lookup"><span data-stu-id="72ad9-977">WebSockets only.</span></span> <span data-ttu-id="72ad9-978">Maximální doba, po kterou klient čeká po zavření, aby server potvrdil požadavek na uzavření.</span><span class="sxs-lookup"><span data-stu-id="72ad9-978">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="72ad9-979">Pokud server nepotvrdí uzavření během této doby, klient se odpojí.</span><span class="sxs-lookup"><span data-stu-id="72ad9-979">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="72ad9-980">Prázdné</span><span class="sxs-lookup"><span data-stu-id="72ad9-980">Empty</span></span> | <span data-ttu-id="72ad9-981">Mapa dalších hlaviček PROTOKOLU HTTP, které se mají odesílat s každým požadavkem HTTP.</span><span class="sxs-lookup"><span data-stu-id="72ad9-981">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="72ad9-982">Delegát, který lze použít ke `HttpMessageHandler` konfiguraci nebo nahrazení použitých k odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="72ad9-982">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="72ad9-983">Nepoužívá se pro připojení WebSocket.</span><span class="sxs-lookup"><span data-stu-id="72ad9-983">Not used for WebSocket connections.</span></span> <span data-ttu-id="72ad9-984">Tento delegát musí vrátit hodnotu bez hodnoty null a obdrží výchozí hodnotu jako parametr.</span><span class="sxs-lookup"><span data-stu-id="72ad9-984">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="72ad9-985">Buď upravte nastavení této výchozí hodnoty a `HttpMessageHandler` vraťte ji, nebo vraťte novou instanci.</span><span class="sxs-lookup"><span data-stu-id="72ad9-985">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="72ad9-986">**Při výměně obslužné rutiny nezapomeňte zkopírovat nastavení, které chcete zachovat z poskytnuté obslužné rutiny, jinak nakonfigurované možnosti (například soubory cookie a záhlaví) se na novou obslužnou rutinu nevztahují.**</span><span class="sxs-lookup"><span data-stu-id="72ad9-986">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="72ad9-987">Proxy HTTP, který se má použít při odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="72ad9-987">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="72ad9-988">Nastavte tuto logickou hodnotu tak, aby odesílala výchozí pověření pro požadavky HTTP a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="72ad9-988">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="72ad9-989">To umožňuje použití ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="72ad9-989">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="72ad9-990">Delegát, který lze použít ke konfiguraci dalších možností WebSocket.</span><span class="sxs-lookup"><span data-stu-id="72ad9-990">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="72ad9-991">Přijímá instanci [ClientWebSocketOptions,](/dotnet/api/system.net.websockets.clientwebsocketoptions) která může být použita ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="72ad9-991">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="72ad9-992">JavaScript</span><span class="sxs-lookup"><span data-stu-id="72ad9-992">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="72ad9-993">Možnost JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="72ad9-993">JavaScript Option</span></span> | <span data-ttu-id="72ad9-994">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="72ad9-994">Default Value</span></span> | <span data-ttu-id="72ad9-995">Popis</span><span class="sxs-lookup"><span data-stu-id="72ad9-995">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="72ad9-996">Funkce vracející řetězec, který je k dispozici jako ověřovací token Nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="72ad9-996">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="72ad9-997">Nastavte tuto `true` na přeskočení kroku vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="72ad9-997">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="72ad9-998">**Podporováno pouze v případě, že přenos WebSockets je pouze povolený přenos**.</span><span class="sxs-lookup"><span data-stu-id="72ad9-998">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="72ad9-999">Toto nastavení nelze povolit při použití služby Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="72ad9-999">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="72ad9-1000">Java</span><span class="sxs-lookup"><span data-stu-id="72ad9-1000">Java</span></span>](#tab/java)

| <span data-ttu-id="72ad9-1001">Java možnost</span><span class="sxs-lookup"><span data-stu-id="72ad9-1001">Java Option</span></span> | <span data-ttu-id="72ad9-1002">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="72ad9-1002">Default Value</span></span> | <span data-ttu-id="72ad9-1003">Popis</span><span class="sxs-lookup"><span data-stu-id="72ad9-1003">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="72ad9-1004">Funkce vracející řetězec, který je k dispozici jako ověřovací token Nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="72ad9-1004">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="72ad9-1005">Nastavte tuto `true` na přeskočení kroku vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="72ad9-1005">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="72ad9-1006">**Podporováno pouze v případě, že přenos WebSockets je pouze povolený přenos**.</span><span class="sxs-lookup"><span data-stu-id="72ad9-1006">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="72ad9-1007">Toto nastavení nelze povolit při použití služby Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="72ad9-1007">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="72ad9-1008">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="72ad9-1008">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="72ad9-1009">Prázdné</span><span class="sxs-lookup"><span data-stu-id="72ad9-1009">Empty</span></span> | <span data-ttu-id="72ad9-1010">Mapa dalších hlaviček PROTOKOLU HTTP, které se mají odesílat s každým požadavkem HTTP.</span><span class="sxs-lookup"><span data-stu-id="72ad9-1010">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="72ad9-1011">V klientovi .NET mohou být tyto možnosti změněny možnostmi, které delegát poskytuje `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="72ad9-1011">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="72ad9-1012">V klientovi JavaScript mohou být tyto možnosti poskytnuty `withUrl`v objektu JavaScript u :</span><span class="sxs-lookup"><span data-stu-id="72ad9-1012">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="72ad9-1013">V klientovi Java lze tyto možnosti nakonfigurovat pomocí metod na vráceném `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="72ad9-1013">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="72ad9-1014">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="72ad9-1014">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
