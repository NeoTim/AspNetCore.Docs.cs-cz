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
ms.openlocfilehash: 2e9fda6d57986171fc375a2e0fdebf9e111218e0
ms.sourcegitcommit: 6f1b516e0c899a49afe9a29044a2383ce2ada3c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81223982"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="def09-103">ASP.NET konfigurace Core SignalR</span><span class="sxs-lookup"><span data-stu-id="def09-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="def09-104">Možnosti serializace balíčku JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="def09-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="def09-105">ASP.NET Core SignalR podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="def09-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="def09-106">Každý protokol má možnosti konfigurace serializace.</span><span class="sxs-lookup"><span data-stu-id="def09-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="def09-107">Serializaci JSON lze na serveru nakonfigurovat pomocí metody rozšíření [AddJsonProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol)</span><span class="sxs-lookup"><span data-stu-id="def09-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="def09-108">`AddJsonProtocol`lze přidat po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="def09-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="def09-109">Metoda `AddJsonProtocol` trvá delegáta, který `options` obdrží objekt.</span><span class="sxs-lookup"><span data-stu-id="def09-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="def09-110">[Vlastnost PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) u tohoto `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objektu je objekt, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="def09-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="def09-111">Další informace naleznete v [dokumentaci System.Text.Json](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="def09-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="def09-112">Chcete-li například nakonfigurovat serializátor tak, aby neměnil názvy vlastností, namísto výchozích názvů camelCase použijte následující kód v aplikaci `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="def09-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="def09-113">V klientovi .NET `AddJsonProtocol` existuje stejná metoda rozšíření v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="def09-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="def09-114">Obor `Microsoft.Extensions.DependencyInjection` názvů musí být importován, aby se vyřešila metoda rozšíření:</span><span class="sxs-lookup"><span data-stu-id="def09-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="def09-115">V tuto chvíli není možné konfigurovat serializaci JSON v klientovi JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="def09-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="def09-116">Přejít na Newtonsoft.Json</span><span class="sxs-lookup"><span data-stu-id="def09-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="def09-117">Pokud potřebujete `Newtonsoft.Json` funkce, které nejsou `System.Text.Json`podporovány v , viz [Přepnout na Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="def09-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="def09-118">Možnosti serializace messagepacku</span><span class="sxs-lookup"><span data-stu-id="def09-118">MessagePack serialization options</span></span>

<span data-ttu-id="def09-119">Serializace messagepack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)</span><span class="sxs-lookup"><span data-stu-id="def09-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="def09-120">Další podrobnosti najdete [v tématu MessagePack v SignalR.](xref:signalr/messagepackhubprotocol)</span><span class="sxs-lookup"><span data-stu-id="def09-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="def09-121">V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="def09-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="def09-122">Konfigurace možností serveru</span><span class="sxs-lookup"><span data-stu-id="def09-122">Configure server options</span></span>

<span data-ttu-id="def09-123">Následující tabulka popisuje možnosti konfigurace rozbočovačů SignalR:</span><span class="sxs-lookup"><span data-stu-id="def09-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="def09-124">Možnost</span><span class="sxs-lookup"><span data-stu-id="def09-124">Option</span></span> | <span data-ttu-id="def09-125">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="def09-125">Default Value</span></span> | <span data-ttu-id="def09-126">Popis</span><span class="sxs-lookup"><span data-stu-id="def09-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="def09-127">30 sekund</span><span class="sxs-lookup"><span data-stu-id="def09-127">30 seconds</span></span> | <span data-ttu-id="def09-128">Server bude považovat klientodpojený, pokud neobdržel zprávu (včetně keep-alive) v tomto intervalu.</span><span class="sxs-lookup"><span data-stu-id="def09-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="def09-129">Může trvat déle než tento časový interval pro klienta skutečně označeny odpojen, z důvodu, jak je implementována.</span><span class="sxs-lookup"><span data-stu-id="def09-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="def09-130">Doporučená hodnota je `KeepAliveInterval` dvojnásobek hodnoty.</span><span class="sxs-lookup"><span data-stu-id="def09-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="def09-131">15 sekund</span><span class="sxs-lookup"><span data-stu-id="def09-131">15 seconds</span></span> | <span data-ttu-id="def09-132">Pokud klient neodešle počáteční zprávu handshake v tomto časovém intervalu, připojení je ukončeno.</span><span class="sxs-lookup"><span data-stu-id="def09-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="def09-133">Toto je rozšířené nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake z důvodu závažné latence sítě.</span><span class="sxs-lookup"><span data-stu-id="def09-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="def09-134">Další podrobnosti o procesu handshake naleznete v tématu [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="def09-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="def09-135">15 sekund</span><span class="sxs-lookup"><span data-stu-id="def09-135">15 seconds</span></span> | <span data-ttu-id="def09-136">Pokud server neodeslal zprávu v tomto intervalu, je automaticky odeslána zpráva ping, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="def09-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="def09-137">Při `KeepAliveInterval`změně změňte `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení klienta.</span><span class="sxs-lookup"><span data-stu-id="def09-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="def09-138">Doporučená `ServerTimeout` / `serverTimeoutInMilliseconds` hodnota je `KeepAliveInterval` dvojnásobek hodnoty.</span><span class="sxs-lookup"><span data-stu-id="def09-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="def09-139">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="def09-139">All installed protocols</span></span> | <span data-ttu-id="def09-140">Protokoly podporované tímto centrem.</span><span class="sxs-lookup"><span data-stu-id="def09-140">Protocols supported by this hub.</span></span> <span data-ttu-id="def09-141">Ve výchozím nastavení jsou povoleny všechny protokoly registrované na serveru, ale protokoly mohou být odebrány z tohoto seznamu zakázat konkrétní protokoly pro jednotlivé rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="def09-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="def09-142">Pokud `true`, podrobné zprávy o výjimce jsou vráceny klientům při vyvolání výjimky v Hub metody.</span><span class="sxs-lookup"><span data-stu-id="def09-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="def09-143">Výchozí hodnota `false`je , protože tyto zprávy o výjimce mohou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="def09-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="def09-144">Maximální počet položek, které mohou být uloženy do vyrovnávací paměti pro datové proudy pro odesílání klienta.</span><span class="sxs-lookup"><span data-stu-id="def09-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="def09-145">Pokud je dosaženo tohoto limitu, zpracování vyvolání je blokován, dokud server zpracovává položky datového proudu.</span><span class="sxs-lookup"><span data-stu-id="def09-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="def09-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="def09-146">32 KB</span></span> | <span data-ttu-id="def09-147">Maximální velikost jedné příchozí zprávy rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="def09-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="def09-148">Možnosti lze nakonfigurovat pro všechny rozbočovače `Startup.ConfigureServices`poskytnutím možnosti delegáta volání v aplikaci `AddSignalR` .</span><span class="sxs-lookup"><span data-stu-id="def09-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="def09-149">Možnosti pro jeden rozbočovač přepíší globální možnosti uvedené v `AddSignalR` aplikaci a lze je konfigurovat pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span><span class="sxs-lookup"><span data-stu-id="def09-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="def09-150">Rozšířené možnosti konfigurace protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="def09-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="def09-151">Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="def09-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="def09-152">Tyto možnosti jsou konfigurovány předáním delegáta [maphub\<t>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="def09-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="def09-153">Následující tabulka popisuje možnosti konfigurace ASP.NET pokročilých možností protokolu HTTP nástroje Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="def09-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="def09-154">Možnost</span><span class="sxs-lookup"><span data-stu-id="def09-154">Option</span></span> | <span data-ttu-id="def09-155">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="def09-155">Default Value</span></span> | <span data-ttu-id="def09-156">Popis</span><span class="sxs-lookup"><span data-stu-id="def09-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="def09-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="def09-157">32 KB</span></span> | <span data-ttu-id="def09-158">Maximální počet bajtů přijatých od klienta, které server vyrovnávací paměti před použitím protitlaku.</span><span class="sxs-lookup"><span data-stu-id="def09-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="def09-159">Zvýšení této hodnoty umožňuje serveru přijímat větší zprávy rychleji bez použití protitlaku, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="def09-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="def09-160">Data automaticky získaná z `Authorize` atributů použitých pro třídu Hub.</span><span class="sxs-lookup"><span data-stu-id="def09-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="def09-161">Seznam objektů [IAuthorizeData,](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) které slouží k určení, zda je klient oprávněn připojit k rozbočovači.</span><span class="sxs-lookup"><span data-stu-id="def09-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="def09-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="def09-162">32 KB</span></span> | <span data-ttu-id="def09-163">Maximální počet bajtů odeslaných aplikací, které server vyrovnávací paměti před sledováním protitlaku.</span><span class="sxs-lookup"><span data-stu-id="def09-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="def09-164">Zvýšení této hodnoty umožňuje serveru do vyrovnávací paměti větší zprávy rychleji bez čekání na protitlak, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="def09-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="def09-165">Všechny přenosy jsou povoleny.</span><span class="sxs-lookup"><span data-stu-id="def09-165">All Transports are enabled.</span></span> | <span data-ttu-id="def09-166">Bit příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenosy klienta můžete použít k připojení.</span><span class="sxs-lookup"><span data-stu-id="def09-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="def09-167">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="def09-167">See below.</span></span> | <span data-ttu-id="def09-168">Další možnosti specifické pro přenos dlouhé dotazování.</span><span class="sxs-lookup"><span data-stu-id="def09-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="def09-169">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="def09-169">See below.</span></span> | <span data-ttu-id="def09-170">Další možnosti specifické pro přenos WebSockets.</span><span class="sxs-lookup"><span data-stu-id="def09-170">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="def09-171">Přenos dlouhé dotazování má další možnosti, které lze nakonfigurovat pomocí vlastnosti: `LongPolling`</span><span class="sxs-lookup"><span data-stu-id="def09-171">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="def09-172">Možnost</span><span class="sxs-lookup"><span data-stu-id="def09-172">Option</span></span> | <span data-ttu-id="def09-173">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="def09-173">Default Value</span></span> | <span data-ttu-id="def09-174">Popis</span><span class="sxs-lookup"><span data-stu-id="def09-174">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="def09-175">90 sekund</span><span class="sxs-lookup"><span data-stu-id="def09-175">90 seconds</span></span> | <span data-ttu-id="def09-176">Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jednoho požadavku na dotazování.</span><span class="sxs-lookup"><span data-stu-id="def09-176">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="def09-177">Snížení této hodnoty způsobí, že klient vydávat nové požadavky na dotazování častěji.</span><span class="sxs-lookup"><span data-stu-id="def09-177">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="def09-178">Přenos WebSocket má další možnosti, `WebSockets` které lze nakonfigurovat pomocí vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="def09-178">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="def09-179">Možnost</span><span class="sxs-lookup"><span data-stu-id="def09-179">Option</span></span> | <span data-ttu-id="def09-180">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="def09-180">Default Value</span></span> | <span data-ttu-id="def09-181">Popis</span><span class="sxs-lookup"><span data-stu-id="def09-181">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="def09-182">5 sekund</span><span class="sxs-lookup"><span data-stu-id="def09-182">5 seconds</span></span> | <span data-ttu-id="def09-183">Po ukončení serveru, pokud se klientovi nepodaří zavřít v tomto časovém intervalu, je připojení ukončeno.</span><span class="sxs-lookup"><span data-stu-id="def09-183">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="def09-184">Delegát, který lze použít `Sec-WebSocket-Protocol` k nastavení záhlaví na vlastní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="def09-184">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="def09-185">Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že vrátí požadovanou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="def09-185">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="def09-186">Konfigurace možností klienta</span><span class="sxs-lookup"><span data-stu-id="def09-186">Configure client options</span></span>

<span data-ttu-id="def09-187">Možnosti klienta lze `HubConnectionBuilder` konfigurovat na typu (k dispozici v klientech .NET a JavaScript).</span><span class="sxs-lookup"><span data-stu-id="def09-187">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="def09-188">Je také k dispozici v klientovi `HttpHubConnectionBuilder` Java, ale podtřída je to, co `HubConnection` obsahuje možnosti konfigurace tvůrce, stejně jako na sobě.</span><span class="sxs-lookup"><span data-stu-id="def09-188">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="def09-189">Konfigurace protokolování</span><span class="sxs-lookup"><span data-stu-id="def09-189">Configure logging</span></span>

<span data-ttu-id="def09-190">Protokolování je konfigurováno v `ConfigureLogging` klientovi .NET pomocí metody.</span><span class="sxs-lookup"><span data-stu-id="def09-190">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="def09-191">Zprostředkovatelé protokolování a filtry mohou být registrovány stejným způsobem jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="def09-191">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="def09-192">Další informace naleznete v dokumentaci [protokolování ASP.NET jádra.](xref:fundamentals/logging/index)</span><span class="sxs-lookup"><span data-stu-id="def09-192">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="def09-193">Chcete-li zaregistrovat zprostředkovatele protokolování, je nutné nainstalovat potřebné balíčky.</span><span class="sxs-lookup"><span data-stu-id="def09-193">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="def09-194">Úplný seznam najdete v části [Vestavěné zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentech.</span><span class="sxs-lookup"><span data-stu-id="def09-194">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="def09-195">Chcete-li například povolit `Microsoft.Extensions.Logging.Console` protokolování konzoly, nainstalujte balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="def09-195">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="def09-196">Volání `AddConsole` metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="def09-196">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="def09-197">V klientovi JavaScript `configureLogging` existuje podobná metoda.</span><span class="sxs-lookup"><span data-stu-id="def09-197">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="def09-198">Zadejte `LogLevel` hodnotu označující minimální úroveň zpráv protokolu k vytvoření.</span><span class="sxs-lookup"><span data-stu-id="def09-198">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="def09-199">Protokoly jsou zapsány do okna konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="def09-199">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="def09-200">Namísto `LogLevel` hodnoty můžete také zadat `string` hodnotu představující název na úrovni protokolu.</span><span class="sxs-lookup"><span data-stu-id="def09-200">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="def09-201">To je užitečné při konfiguraci protokolování SignalR v prostředích, kde nemáte přístup k `LogLevel` konstantám.</span><span class="sxs-lookup"><span data-stu-id="def09-201">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="def09-202">V následující tabulce jsou uvedeny dostupné úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="def09-202">The following table lists the available log levels.</span></span> <span data-ttu-id="def09-203">Hodnota, kterou `configureLogging` zadáte pro nastavení **minimální** úroveň protokolu, která bude zaznamenána.</span><span class="sxs-lookup"><span data-stu-id="def09-203">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="def09-204">Zprávy zaznamenané na této úrovni **nebo úrovně uvedené za ní v tabulce**budou zaznamenány.</span><span class="sxs-lookup"><span data-stu-id="def09-204">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="def09-205">Řetězec</span><span class="sxs-lookup"><span data-stu-id="def09-205">String</span></span>                      | <span data-ttu-id="def09-206">LogLevel</span><span class="sxs-lookup"><span data-stu-id="def09-206">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="def09-207">`info`**nebo**`information`</span><span class="sxs-lookup"><span data-stu-id="def09-207">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="def09-208">`warn`**nebo**`warning`</span><span class="sxs-lookup"><span data-stu-id="def09-208">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="def09-209">Chcete-li protokolování `signalR.LogLevel.None` zcela `configureLogging` zakázat, zadejte v metodě.</span><span class="sxs-lookup"><span data-stu-id="def09-209">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="def09-210">Další informace o protokolování naleznete v [dokumentaci k diagnostice signalr .](xref:signalr/diagnostics)</span><span class="sxs-lookup"><span data-stu-id="def09-210">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="def09-211">Klient Java SignalR používá pro protokolování knihovnu [SLF4J.](https://www.slf4j.org/)</span><span class="sxs-lookup"><span data-stu-id="def09-211">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="def09-212">Jedná se o rozhraní API pro protokolování vysoké úrovně, které umožňuje uživatelům knihovny zvolit vlastní implementaci protokolování tím, že přivedou konkrétní závislost protokolování.</span><span class="sxs-lookup"><span data-stu-id="def09-212">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="def09-213">Následující fragment kódu ukazuje, jak `java.util.logging` se používat s klientem Java SignalR.</span><span class="sxs-lookup"><span data-stu-id="def09-213">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="def09-214">Pokud nenakonfigurujete protokolování v závislostech, SLF4J načte výchozí protokolovací nástroj bez operace s následující varovnou zprávou:</span><span class="sxs-lookup"><span data-stu-id="def09-214">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="def09-215">To lze bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="def09-215">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="def09-216">Konfigurace povolených přenosů</span><span class="sxs-lookup"><span data-stu-id="def09-216">Configure allowed transports</span></span>

<span data-ttu-id="def09-217">Přenosy používané SignalR lze konfigurovat `WithUrl` ve`withUrl` volání (v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="def09-217">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="def09-218">Bitové nebo hodnoty `HttpTransportType` lze omezit klienta používat pouze zadané přenosy.</span><span class="sxs-lookup"><span data-stu-id="def09-218">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="def09-219">Všechny přenosy jsou ve výchozím nastavení povoleny.</span><span class="sxs-lookup"><span data-stu-id="def09-219">All transports are enabled by default.</span></span>

<span data-ttu-id="def09-220">Chcete-li například zakázat přenos událostí odeslaných serverem, ale povolit připojení WebSockets a Long Polling:</span><span class="sxs-lookup"><span data-stu-id="def09-220">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="def09-221">V klientovi JavaScript jsou přenosy `transport` konfigurovány nastavením pole `withUrl`na objektu options, který je k dispozici :</span><span class="sxs-lookup"><span data-stu-id="def09-221">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="def09-222">V této verzi webových zásuvek klienta Java je pouze dostupný přenos.</span><span class="sxs-lookup"><span data-stu-id="def09-222">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="def09-223">V klientovi Java je přenos `withTransport` vybrán s `HttpHubConnectionBuilder`metodou na .</span><span class="sxs-lookup"><span data-stu-id="def09-223">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="def09-224">Klient Java ve výchozím nastavení používá přenos WebSockets.</span><span class="sxs-lookup"><span data-stu-id="def09-224">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="def09-225">Klient Java SignalR ještě nepodporuje záložní přenos.</span><span class="sxs-lookup"><span data-stu-id="def09-225">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="def09-226">Konfigurace ověřování nosiče</span><span class="sxs-lookup"><span data-stu-id="def09-226">Configure bearer authentication</span></span>

<span data-ttu-id="def09-227">Chcete-li poskytnout ověřovací data spolu s `AccessTokenProvider` požadavky`accessTokenFactory` SignalR, použijte možnost ( v Jazyce JavaScript a určete funkci, která vrátí požadovaný přístupový token.</span><span class="sxs-lookup"><span data-stu-id="def09-227">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="def09-228">V klientovi .NET je tento přístupový token předán jako http "Bearer Authentication" token (Pomocí `Authorization` hlavičky `Bearer`s typem ).</span><span class="sxs-lookup"><span data-stu-id="def09-228">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="def09-229">V klientovi JavaScript se přístupový token používá jako token Nosiče, **s výjimkou** několika případů, kdy rozhraní API prohlížeče omezují možnost použití záhlaví (konkrétně v požadavcích Události odeslané serverem a WebSockets).</span><span class="sxs-lookup"><span data-stu-id="def09-229">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="def09-230">V těchto případech je přístupový token k `access_token`dispozici jako hodnota řetězce dotazu .</span><span class="sxs-lookup"><span data-stu-id="def09-230">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="def09-231">V klientovi .NET `AccessTokenProvider` lze tuto možnost zadat `WithUrl`pomocí možností delegáta v aplikaci :</span><span class="sxs-lookup"><span data-stu-id="def09-231">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="def09-232">V klientovi JavaScript je přístupový token `accessTokenFactory` konfigurován nastavením `withUrl`pole na objektu options v aplikaci :</span><span class="sxs-lookup"><span data-stu-id="def09-232">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="def09-233">V klientovi SignalR Java můžete nakonfigurovat token nosiče pro ověřování poskytnutím objektu [HttpHubConnectionBuilder.](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)</span><span class="sxs-lookup"><span data-stu-id="def09-233">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="def09-234">Použijte [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) poskytnout [RxJava](https://github.com/ReactiveX/RxJava) [single\<string>](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="def09-234">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="def09-235">S [volánísingle.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), můžete napsat logiku k vytvoření přístupové tokeny pro vašeho klienta.</span><span class="sxs-lookup"><span data-stu-id="def09-235">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="def09-236">Konfigurace možností časového limitu a udržování v životě</span><span class="sxs-lookup"><span data-stu-id="def09-236">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="def09-237">Další možnosti pro konfiguraci časového limitu a `HubConnection` chování udržování naživu jsou k dispozici na samotném objektu:</span><span class="sxs-lookup"><span data-stu-id="def09-237">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="def09-238">.NET</span><span class="sxs-lookup"><span data-stu-id="def09-238">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="def09-239">Možnost</span><span class="sxs-lookup"><span data-stu-id="def09-239">Option</span></span> | <span data-ttu-id="def09-240">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="def09-240">Default value</span></span> | <span data-ttu-id="def09-241">Popis</span><span class="sxs-lookup"><span data-stu-id="def09-241">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="def09-242">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="def09-242">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="def09-243">Časový čas pro aktivitu serveru.</span><span class="sxs-lookup"><span data-stu-id="def09-243">Timeout for server activity.</span></span> <span data-ttu-id="def09-244">Pokud server v tomto intervalu neodeslal zprávu, klient uváží server `Closed` odpojený`onclose` a spustí událost (v Jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="def09-244">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="def09-245">Tato hodnota musí být dostatečně velká, aby zpráva ping byla odeslána ze serveru **a** přijata klientem v časovém intervalu.</span><span class="sxs-lookup"><span data-stu-id="def09-245">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="def09-246">Doporučená hodnota je číslo alespoň dvojnásobek `KeepAliveInterval` hodnoty serveru povolit čas pro příkaz ping k doručení.</span><span class="sxs-lookup"><span data-stu-id="def09-246">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="def09-247">15 sekund</span><span class="sxs-lookup"><span data-stu-id="def09-247">15 seconds</span></span> | <span data-ttu-id="def09-248">Časový limit pro počáteční server handshake.</span><span class="sxs-lookup"><span data-stu-id="def09-248">Timeout for initial server handshake.</span></span> <span data-ttu-id="def09-249">Pokud server neodešle odpověď handshake v tomto intervalu, klient zruší `Closed` handshake`onclose` a spustí událost (v Jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="def09-249">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="def09-250">Toto je rozšířené nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake z důvodu závažné latence sítě.</span><span class="sxs-lookup"><span data-stu-id="def09-250">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="def09-251">Další podrobnosti o procesu handshake naleznete v tématu [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="def09-251">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="def09-252">15 sekund</span><span class="sxs-lookup"><span data-stu-id="def09-252">15 seconds</span></span> | <span data-ttu-id="def09-253">Určuje interval, ve kterém klient odesílá zprávy ping.</span><span class="sxs-lookup"><span data-stu-id="def09-253">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="def09-254">Odesláním libovolné zprávy z klienta obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="def09-254">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="def09-255">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, server považuje klienta za odpojeného.</span><span class="sxs-lookup"><span data-stu-id="def09-255">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="def09-256">V klientovi .NET jsou hodnoty `TimeSpan` časového času určeny jako hodnoty.</span><span class="sxs-lookup"><span data-stu-id="def09-256">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="def09-257">JavaScript</span><span class="sxs-lookup"><span data-stu-id="def09-257">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="def09-258">Možnost</span><span class="sxs-lookup"><span data-stu-id="def09-258">Option</span></span> | <span data-ttu-id="def09-259">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="def09-259">Default value</span></span> | <span data-ttu-id="def09-260">Popis</span><span class="sxs-lookup"><span data-stu-id="def09-260">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="def09-261">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="def09-261">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="def09-262">Časový čas pro aktivitu serveru.</span><span class="sxs-lookup"><span data-stu-id="def09-262">Timeout for server activity.</span></span> <span data-ttu-id="def09-263">Pokud server neodeslal zprávu v tomto intervalu, klient považuje server odpojena a spustí `onclose` událost.</span><span class="sxs-lookup"><span data-stu-id="def09-263">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="def09-264">Tato hodnota musí být dostatečně velká, aby zpráva ping byla odeslána ze serveru **a** přijata klientem v časovém intervalu.</span><span class="sxs-lookup"><span data-stu-id="def09-264">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="def09-265">Doporučená hodnota je číslo alespoň dvojnásobek `KeepAliveInterval` hodnoty serveru povolit čas pro příkaz ping k doručení.</span><span class="sxs-lookup"><span data-stu-id="def09-265">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="def09-266">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="def09-266">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="def09-267">Určuje interval, ve kterém klient odesílá zprávy ping.</span><span class="sxs-lookup"><span data-stu-id="def09-267">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="def09-268">Odesláním libovolné zprávy z klienta obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="def09-268">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="def09-269">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, server považuje klienta za odpojeného.</span><span class="sxs-lookup"><span data-stu-id="def09-269">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="def09-270">Java</span><span class="sxs-lookup"><span data-stu-id="def09-270">Java</span></span>](#tab/java)

| <span data-ttu-id="def09-271">Možnost</span><span class="sxs-lookup"><span data-stu-id="def09-271">Option</span></span> | <span data-ttu-id="def09-272">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="def09-272">Default value</span></span> | <span data-ttu-id="def09-273">Popis</span><span class="sxs-lookup"><span data-stu-id="def09-273">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="def09-274">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="def09-274">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="def09-275">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="def09-275">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="def09-276">Časový čas pro aktivitu serveru.</span><span class="sxs-lookup"><span data-stu-id="def09-276">Timeout for server activity.</span></span> <span data-ttu-id="def09-277">Pokud server neodeslal zprávu v tomto intervalu, klient považuje server odpojena a spustí `onClose` událost.</span><span class="sxs-lookup"><span data-stu-id="def09-277">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="def09-278">Tato hodnota musí být dostatečně velká, aby zpráva ping byla odeslána ze serveru **a** přijata klientem v časovém intervalu.</span><span class="sxs-lookup"><span data-stu-id="def09-278">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="def09-279">Doporučená hodnota je číslo alespoň dvojnásobek `KeepAliveInterval` hodnoty serveru povolit čas pro příkaz ping k doručení.</span><span class="sxs-lookup"><span data-stu-id="def09-279">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="def09-280">15 sekund</span><span class="sxs-lookup"><span data-stu-id="def09-280">15 seconds</span></span> | <span data-ttu-id="def09-281">Časový limit pro počáteční server handshake.</span><span class="sxs-lookup"><span data-stu-id="def09-281">Timeout for initial server handshake.</span></span> <span data-ttu-id="def09-282">Pokud server neodešle odpověď handshake v tomto intervalu, klient zruší `onClose` handshake a spustí událost.</span><span class="sxs-lookup"><span data-stu-id="def09-282">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="def09-283">Toto je rozšířené nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake z důvodu závažné latence sítě.</span><span class="sxs-lookup"><span data-stu-id="def09-283">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="def09-284">Další podrobnosti o procesu handshake naleznete v tématu [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="def09-284">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="def09-285">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="def09-285">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="def09-286">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="def09-286">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="def09-287">Určuje interval, ve kterém klient odesílá zprávy ping.</span><span class="sxs-lookup"><span data-stu-id="def09-287">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="def09-288">Odesláním libovolné zprávy z klienta obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="def09-288">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="def09-289">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, server považuje klienta za odpojeného.</span><span class="sxs-lookup"><span data-stu-id="def09-289">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="def09-290">Konfigurace dalších možností</span><span class="sxs-lookup"><span data-stu-id="def09-290">Configure additional options</span></span>

<span data-ttu-id="def09-291">Další možnosti lze `WithUrl` konfigurovat`withUrl` v metodě `HubConnectionBuilder` (v Jazyce JavaScript) `HttpHubConnectionBuilder` na různých konfiguračních API v klientovi java:</span><span class="sxs-lookup"><span data-stu-id="def09-291">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="def09-292">.NET</span><span class="sxs-lookup"><span data-stu-id="def09-292">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="def09-293">Možnost .NET</span><span class="sxs-lookup"><span data-stu-id="def09-293">.NET Option</span></span> |  <span data-ttu-id="def09-294">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="def09-294">Default value</span></span> | <span data-ttu-id="def09-295">Popis</span><span class="sxs-lookup"><span data-stu-id="def09-295">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="def09-296">Funkce vracející řetězec, který je k dispozici jako ověřovací token Nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="def09-296">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="def09-297">Nastavte tuto `true` na přeskočení kroku vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="def09-297">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="def09-298">**Podporováno pouze v případě, že přenos WebSockets je pouze povolený přenos**.</span><span class="sxs-lookup"><span data-stu-id="def09-298">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="def09-299">Toto nastavení nelze povolit při použití služby Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="def09-299">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="def09-300">Prázdné</span><span class="sxs-lookup"><span data-stu-id="def09-300">Empty</span></span> | <span data-ttu-id="def09-301">Kolekce certifikátů TLS k odeslání k ověření požadavků.</span><span class="sxs-lookup"><span data-stu-id="def09-301">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="def09-302">Prázdné</span><span class="sxs-lookup"><span data-stu-id="def09-302">Empty</span></span> | <span data-ttu-id="def09-303">Kolekce souborů cookie HTTP, které se mají odesílat s každým požadavkem HTTP.</span><span class="sxs-lookup"><span data-stu-id="def09-303">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="def09-304">Prázdné</span><span class="sxs-lookup"><span data-stu-id="def09-304">Empty</span></span> | <span data-ttu-id="def09-305">Pověření k odeslání s každým požadavkem HTTP.</span><span class="sxs-lookup"><span data-stu-id="def09-305">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="def09-306">5 sekund</span><span class="sxs-lookup"><span data-stu-id="def09-306">5 seconds</span></span> | <span data-ttu-id="def09-307">Pouze websockets.</span><span class="sxs-lookup"><span data-stu-id="def09-307">WebSockets only.</span></span> <span data-ttu-id="def09-308">Maximální doba, po kterou klient čeká po zavření, aby server potvrdil požadavek na uzavření.</span><span class="sxs-lookup"><span data-stu-id="def09-308">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="def09-309">Pokud server nepotvrdí uzavření během této doby, klient se odpojí.</span><span class="sxs-lookup"><span data-stu-id="def09-309">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="def09-310">Prázdné</span><span class="sxs-lookup"><span data-stu-id="def09-310">Empty</span></span> | <span data-ttu-id="def09-311">Mapa dalších hlaviček PROTOKOLU HTTP, které se mají odesílat s každým požadavkem HTTP.</span><span class="sxs-lookup"><span data-stu-id="def09-311">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="def09-312">Delegát, který lze použít ke `HttpMessageHandler` konfiguraci nebo nahrazení použitých k odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="def09-312">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="def09-313">Nepoužívá se pro připojení WebSocket.</span><span class="sxs-lookup"><span data-stu-id="def09-313">Not used for WebSocket connections.</span></span> <span data-ttu-id="def09-314">Tento delegát musí vrátit hodnotu bez hodnoty null a obdrží výchozí hodnotu jako parametr.</span><span class="sxs-lookup"><span data-stu-id="def09-314">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="def09-315">Buď upravte nastavení této výchozí hodnoty a `HttpMessageHandler` vraťte ji, nebo vraťte novou instanci.</span><span class="sxs-lookup"><span data-stu-id="def09-315">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="def09-316">**Při výměně obslužné rutiny nezapomeňte zkopírovat nastavení, které chcete zachovat z poskytnuté obslužné rutiny, jinak nakonfigurované možnosti (například soubory cookie a záhlaví) se na novou obslužnou rutinu nevztahují.**</span><span class="sxs-lookup"><span data-stu-id="def09-316">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="def09-317">Proxy HTTP, který se má použít při odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="def09-317">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="def09-318">Nastavte tuto logickou hodnotu tak, aby odesílala výchozí pověření pro požadavky HTTP a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="def09-318">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="def09-319">To umožňuje použití ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="def09-319">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="def09-320">Delegát, který lze použít ke konfiguraci dalších možností WebSocket.</span><span class="sxs-lookup"><span data-stu-id="def09-320">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="def09-321">Přijímá instanci [ClientWebSocketOptions,](/dotnet/api/system.net.websockets.clientwebsocketoptions) která může být použita ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="def09-321">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="def09-322">JavaScript</span><span class="sxs-lookup"><span data-stu-id="def09-322">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="def09-323">Možnost JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="def09-323">JavaScript Option</span></span> | <span data-ttu-id="def09-324">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="def09-324">Default Value</span></span> | <span data-ttu-id="def09-325">Popis</span><span class="sxs-lookup"><span data-stu-id="def09-325">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="def09-326">Funkce vracející řetězec, který je k dispozici jako ověřovací token Nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="def09-326">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="def09-327">Nastavte tuto `true` na přeskočení kroku vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="def09-327">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="def09-328">**Podporováno pouze v případě, že přenos WebSockets je pouze povolený přenos**.</span><span class="sxs-lookup"><span data-stu-id="def09-328">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="def09-329">Toto nastavení nelze povolit při použití služby Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="def09-329">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="def09-330">Java</span><span class="sxs-lookup"><span data-stu-id="def09-330">Java</span></span>](#tab/java)

| <span data-ttu-id="def09-331">Java možnost</span><span class="sxs-lookup"><span data-stu-id="def09-331">Java Option</span></span> | <span data-ttu-id="def09-332">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="def09-332">Default Value</span></span> | <span data-ttu-id="def09-333">Popis</span><span class="sxs-lookup"><span data-stu-id="def09-333">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="def09-334">Funkce vracející řetězec, který je k dispozici jako ověřovací token Nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="def09-334">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="def09-335">Nastavte tuto `true` na přeskočení kroku vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="def09-335">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="def09-336">**Podporováno pouze v případě, že přenos WebSockets je pouze povolený přenos**.</span><span class="sxs-lookup"><span data-stu-id="def09-336">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="def09-337">Toto nastavení nelze povolit při použití služby Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="def09-337">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="def09-338">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="def09-338">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="def09-339">Prázdné</span><span class="sxs-lookup"><span data-stu-id="def09-339">Empty</span></span> | <span data-ttu-id="def09-340">Mapa dalších hlaviček PROTOKOLU HTTP, které se mají odesílat s každým požadavkem HTTP.</span><span class="sxs-lookup"><span data-stu-id="def09-340">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="def09-341">V klientovi .NET mohou být tyto možnosti změněny možnostmi, které delegát poskytuje `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="def09-341">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="def09-342">V klientovi JavaScript mohou být tyto možnosti poskytnuty `withUrl`v objektu JavaScript u :</span><span class="sxs-lookup"><span data-stu-id="def09-342">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="def09-343">V klientovi Java lze tyto možnosti nakonfigurovat pomocí metod na vráceném `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="def09-343">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="def09-344">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="def09-344">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="def09-345">Možnosti serializace balíčku JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="def09-345">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="def09-346">ASP.NET Core SignalR podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="def09-346">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="def09-347">Každý protokol má možnosti konfigurace serializace.</span><span class="sxs-lookup"><span data-stu-id="def09-347">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="def09-348">Serializaci JSON lze na serveru nakonfigurovat pomocí metody rozšíření [AddJsonProtocol,](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) kterou `Startup.ConfigureServices` lze přidat za [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) ve vaší metodě.</span><span class="sxs-lookup"><span data-stu-id="def09-348">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="def09-349">Metoda `AddJsonProtocol` trvá delegáta, který `options` obdrží objekt.</span><span class="sxs-lookup"><span data-stu-id="def09-349">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="def09-350">[Vlastnost PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) u tohoto objektu je JSON.NET `JsonSerializerSettings` objekt, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="def09-350">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="def09-351">Další informace naleznete v [dokumentaci k JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="def09-351">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="def09-352">Jako příklad nakonfigurujte serializátor tak, aby používal názvy vlastností "PascalCase", `Startup.ConfigureServices`namísto výchozích názvů camelCase, použijte následující kód v aplikaci :</span><span class="sxs-lookup"><span data-stu-id="def09-352">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="def09-353">V klientovi .NET `AddJsonProtocol` existuje stejná metoda rozšíření v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="def09-353">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="def09-354">Obor `Microsoft.Extensions.DependencyInjection` názvů musí být importován, aby se vyřešila metoda rozšíření:</span><span class="sxs-lookup"><span data-stu-id="def09-354">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="def09-355">V tuto chvíli není možné konfigurovat serializaci JSON v klientovi JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="def09-355">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="def09-356">Možnosti serializace messagepacku</span><span class="sxs-lookup"><span data-stu-id="def09-356">MessagePack serialization options</span></span>

<span data-ttu-id="def09-357">Serializace messagepack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)</span><span class="sxs-lookup"><span data-stu-id="def09-357">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="def09-358">Další podrobnosti najdete [v tématu MessagePack v SignalR.](xref:signalr/messagepackhubprotocol)</span><span class="sxs-lookup"><span data-stu-id="def09-358">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="def09-359">V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="def09-359">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="def09-360">Konfigurace možností serveru</span><span class="sxs-lookup"><span data-stu-id="def09-360">Configure server options</span></span>

<span data-ttu-id="def09-361">Následující tabulka popisuje možnosti konfigurace rozbočovačů SignalR:</span><span class="sxs-lookup"><span data-stu-id="def09-361">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="def09-362">Možnost</span><span class="sxs-lookup"><span data-stu-id="def09-362">Option</span></span> | <span data-ttu-id="def09-363">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="def09-363">Default Value</span></span> | <span data-ttu-id="def09-364">Popis</span><span class="sxs-lookup"><span data-stu-id="def09-364">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="def09-365">30 sekund</span><span class="sxs-lookup"><span data-stu-id="def09-365">30 seconds</span></span> | <span data-ttu-id="def09-366">Server bude považovat klientodpojený, pokud neobdržel zprávu (včetně keep-alive) v tomto intervalu.</span><span class="sxs-lookup"><span data-stu-id="def09-366">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="def09-367">Může trvat déle než tento časový interval pro klienta skutečně označeny odpojen, z důvodu, jak je implementována.</span><span class="sxs-lookup"><span data-stu-id="def09-367">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="def09-368">Doporučená hodnota je `KeepAliveInterval` dvojnásobek hodnoty.</span><span class="sxs-lookup"><span data-stu-id="def09-368">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="def09-369">15 sekund</span><span class="sxs-lookup"><span data-stu-id="def09-369">15 seconds</span></span> | <span data-ttu-id="def09-370">Pokud klient neodešle počáteční zprávu handshake v tomto časovém intervalu, připojení je ukončeno.</span><span class="sxs-lookup"><span data-stu-id="def09-370">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="def09-371">Toto je rozšířené nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake z důvodu závažné latence sítě.</span><span class="sxs-lookup"><span data-stu-id="def09-371">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="def09-372">Další podrobnosti o procesu handshake naleznete v tématu [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="def09-372">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="def09-373">15 sekund</span><span class="sxs-lookup"><span data-stu-id="def09-373">15 seconds</span></span> | <span data-ttu-id="def09-374">Pokud server neodeslal zprávu v tomto intervalu, je automaticky odeslána zpráva ping, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="def09-374">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="def09-375">Při `KeepAliveInterval`změně změňte `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení klienta.</span><span class="sxs-lookup"><span data-stu-id="def09-375">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="def09-376">Doporučená `ServerTimeout` / `serverTimeoutInMilliseconds` hodnota je `KeepAliveInterval` dvojnásobek hodnoty.</span><span class="sxs-lookup"><span data-stu-id="def09-376">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="def09-377">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="def09-377">All installed protocols</span></span> | <span data-ttu-id="def09-378">Protokoly podporované tímto centrem.</span><span class="sxs-lookup"><span data-stu-id="def09-378">Protocols supported by this hub.</span></span> <span data-ttu-id="def09-379">Ve výchozím nastavení jsou povoleny všechny protokoly registrované na serveru, ale protokoly mohou být odebrány z tohoto seznamu zakázat konkrétní protokoly pro jednotlivé rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="def09-379">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="def09-380">Pokud `true`, podrobné zprávy o výjimce jsou vráceny klientům při vyvolání výjimky v Hub metody.</span><span class="sxs-lookup"><span data-stu-id="def09-380">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="def09-381">Výchozí hodnota `false`je , protože tyto zprávy o výjimce mohou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="def09-381">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="def09-382">Možnosti lze nakonfigurovat pro všechny rozbočovače `Startup.ConfigureServices`poskytnutím možnosti delegáta volání v aplikaci `AddSignalR` .</span><span class="sxs-lookup"><span data-stu-id="def09-382">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="def09-383">Možnosti pro jeden rozbočovač přepíší globální možnosti uvedené v `AddSignalR` aplikaci a lze je konfigurovat pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span><span class="sxs-lookup"><span data-stu-id="def09-383">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="def09-384">Rozšířené možnosti konfigurace protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="def09-384">Advanced HTTP configuration options</span></span>

<span data-ttu-id="def09-385">Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="def09-385">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="def09-386">Tyto možnosti jsou konfigurovány předáním delegáta [maphub\<t>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="def09-386">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="def09-387">Následující tabulka popisuje možnosti konfigurace ASP.NET pokročilých možností protokolu HTTP nástroje Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="def09-387">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="def09-388">Možnost</span><span class="sxs-lookup"><span data-stu-id="def09-388">Option</span></span> | <span data-ttu-id="def09-389">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="def09-389">Default Value</span></span> | <span data-ttu-id="def09-390">Popis</span><span class="sxs-lookup"><span data-stu-id="def09-390">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="def09-391">32 KB</span><span class="sxs-lookup"><span data-stu-id="def09-391">32 KB</span></span> | <span data-ttu-id="def09-392">Maximální počet bajtů přijatých od klienta, které server vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="def09-392">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="def09-393">Zvýšení této hodnoty umožňuje serveru přijímat větší zprávy, ale může negativně ovlivnit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="def09-393">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="def09-394">Data automaticky získaná z `Authorize` atributů použitých pro třídu Hub.</span><span class="sxs-lookup"><span data-stu-id="def09-394">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="def09-395">Seznam objektů [IAuthorizeData,](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) které slouží k určení, zda je klient oprávněn připojit k rozbočovači.</span><span class="sxs-lookup"><span data-stu-id="def09-395">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="def09-396">32 KB</span><span class="sxs-lookup"><span data-stu-id="def09-396">32 KB</span></span> | <span data-ttu-id="def09-397">Maximální počet bajtů odeslaných aplikací, které server vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="def09-397">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="def09-398">Zvýšení této hodnoty umožňuje serveru odesílat větší zprávy, ale může negativně ovlivnit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="def09-398">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="def09-399">Všechny přenosy jsou povoleny.</span><span class="sxs-lookup"><span data-stu-id="def09-399">All Transports are enabled.</span></span> | <span data-ttu-id="def09-400">Bit příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenosy klienta můžete použít k připojení.</span><span class="sxs-lookup"><span data-stu-id="def09-400">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="def09-401">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="def09-401">See below.</span></span> | <span data-ttu-id="def09-402">Další možnosti specifické pro přenos dlouhé dotazování.</span><span class="sxs-lookup"><span data-stu-id="def09-402">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="def09-403">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="def09-403">See below.</span></span> | <span data-ttu-id="def09-404">Další možnosti specifické pro přenos WebSockets.</span><span class="sxs-lookup"><span data-stu-id="def09-404">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="def09-405">Přenos dlouhé dotazování má další možnosti, které lze nakonfigurovat pomocí vlastnosti: `LongPolling`</span><span class="sxs-lookup"><span data-stu-id="def09-405">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="def09-406">Možnost</span><span class="sxs-lookup"><span data-stu-id="def09-406">Option</span></span> | <span data-ttu-id="def09-407">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="def09-407">Default Value</span></span> | <span data-ttu-id="def09-408">Popis</span><span class="sxs-lookup"><span data-stu-id="def09-408">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="def09-409">90 sekund</span><span class="sxs-lookup"><span data-stu-id="def09-409">90 seconds</span></span> | <span data-ttu-id="def09-410">Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jednoho požadavku na dotazování.</span><span class="sxs-lookup"><span data-stu-id="def09-410">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="def09-411">Snížení této hodnoty způsobí, že klient vydávat nové požadavky na dotazování častěji.</span><span class="sxs-lookup"><span data-stu-id="def09-411">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="def09-412">Přenos WebSocket má další možnosti, `WebSockets` které lze nakonfigurovat pomocí vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="def09-412">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="def09-413">Možnost</span><span class="sxs-lookup"><span data-stu-id="def09-413">Option</span></span> | <span data-ttu-id="def09-414">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="def09-414">Default Value</span></span> | <span data-ttu-id="def09-415">Popis</span><span class="sxs-lookup"><span data-stu-id="def09-415">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="def09-416">5 sekund</span><span class="sxs-lookup"><span data-stu-id="def09-416">5 seconds</span></span> | <span data-ttu-id="def09-417">Po ukončení serveru, pokud se klientovi nepodaří zavřít v tomto časovém intervalu, je připojení ukončeno.</span><span class="sxs-lookup"><span data-stu-id="def09-417">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="def09-418">Delegát, který lze použít `Sec-WebSocket-Protocol` k nastavení záhlaví na vlastní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="def09-418">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="def09-419">Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že vrátí požadovanou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="def09-419">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="def09-420">Konfigurace možností klienta</span><span class="sxs-lookup"><span data-stu-id="def09-420">Configure client options</span></span>

<span data-ttu-id="def09-421">Možnosti klienta lze `HubConnectionBuilder` konfigurovat na typu (k dispozici v klientech .NET a JavaScript).</span><span class="sxs-lookup"><span data-stu-id="def09-421">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="def09-422">Je také k dispozici v klientovi `HttpHubConnectionBuilder` Java, ale podtřída je to, co `HubConnection` obsahuje možnosti konfigurace tvůrce, stejně jako na sobě.</span><span class="sxs-lookup"><span data-stu-id="def09-422">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="def09-423">Konfigurace protokolování</span><span class="sxs-lookup"><span data-stu-id="def09-423">Configure logging</span></span>

<span data-ttu-id="def09-424">Protokolování je konfigurováno v `ConfigureLogging` klientovi .NET pomocí metody.</span><span class="sxs-lookup"><span data-stu-id="def09-424">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="def09-425">Zprostředkovatelé protokolování a filtry mohou být registrovány stejným způsobem jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="def09-425">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="def09-426">Další informace naleznete v dokumentaci [protokolování ASP.NET jádra.](xref:fundamentals/logging/index)</span><span class="sxs-lookup"><span data-stu-id="def09-426">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="def09-427">Chcete-li zaregistrovat zprostředkovatele protokolování, je nutné nainstalovat potřebné balíčky.</span><span class="sxs-lookup"><span data-stu-id="def09-427">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="def09-428">Úplný seznam najdete v části [Vestavěné zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentech.</span><span class="sxs-lookup"><span data-stu-id="def09-428">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="def09-429">Chcete-li například povolit `Microsoft.Extensions.Logging.Console` protokolování konzoly, nainstalujte balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="def09-429">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="def09-430">Volání `AddConsole` metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="def09-430">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="def09-431">V klientovi JavaScript `configureLogging` existuje podobná metoda.</span><span class="sxs-lookup"><span data-stu-id="def09-431">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="def09-432">Zadejte `LogLevel` hodnotu označující minimální úroveň zpráv protokolu k vytvoření.</span><span class="sxs-lookup"><span data-stu-id="def09-432">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="def09-433">Protokoly jsou zapsány do okna konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="def09-433">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="def09-434">Chcete-li protokolování `signalR.LogLevel.None` zcela `configureLogging` zakázat, zadejte v metodě.</span><span class="sxs-lookup"><span data-stu-id="def09-434">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="def09-435">Další informace o protokolování naleznete v [dokumentaci k diagnostice signalr .](xref:signalr/diagnostics)</span><span class="sxs-lookup"><span data-stu-id="def09-435">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="def09-436">Klient Java SignalR používá pro protokolování knihovnu [SLF4J.](https://www.slf4j.org/)</span><span class="sxs-lookup"><span data-stu-id="def09-436">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="def09-437">Jedná se o rozhraní API pro protokolování vysoké úrovně, které umožňuje uživatelům knihovny zvolit vlastní implementaci protokolování tím, že přivedou konkrétní závislost protokolování.</span><span class="sxs-lookup"><span data-stu-id="def09-437">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="def09-438">Následující fragment kódu ukazuje, jak `java.util.logging` se používat s klientem Java SignalR.</span><span class="sxs-lookup"><span data-stu-id="def09-438">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="def09-439">Pokud nenakonfigurujete protokolování v závislostech, SLF4J načte výchozí protokolovací nástroj bez operace s následující varovnou zprávou:</span><span class="sxs-lookup"><span data-stu-id="def09-439">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="def09-440">To lze bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="def09-440">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="def09-441">Konfigurace povolených přenosů</span><span class="sxs-lookup"><span data-stu-id="def09-441">Configure allowed transports</span></span>

<span data-ttu-id="def09-442">Přenosy používané SignalR lze konfigurovat `WithUrl` ve`withUrl` volání (v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="def09-442">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="def09-443">Bitové nebo hodnoty `HttpTransportType` lze omezit klienta používat pouze zadané přenosy.</span><span class="sxs-lookup"><span data-stu-id="def09-443">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="def09-444">Všechny přenosy jsou ve výchozím nastavení povoleny.</span><span class="sxs-lookup"><span data-stu-id="def09-444">All transports are enabled by default.</span></span>

<span data-ttu-id="def09-445">Chcete-li například zakázat přenos událostí odeslaných serverem, ale povolit připojení WebSockets a Long Polling:</span><span class="sxs-lookup"><span data-stu-id="def09-445">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="def09-446">V klientovi JavaScript jsou přenosy `transport` konfigurovány nastavením pole `withUrl`na objektu options, který je k dispozici :</span><span class="sxs-lookup"><span data-stu-id="def09-446">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="def09-447">V této verzi webových zásuvek klienta Java je pouze dostupný přenos.</span><span class="sxs-lookup"><span data-stu-id="def09-447">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="def09-448">Konfigurace ověřování nosiče</span><span class="sxs-lookup"><span data-stu-id="def09-448">Configure bearer authentication</span></span>

<span data-ttu-id="def09-449">Chcete-li poskytnout ověřovací data spolu s `AccessTokenProvider` požadavky`accessTokenFactory` SignalR, použijte možnost ( v Jazyce JavaScript a určete funkci, která vrátí požadovaný přístupový token.</span><span class="sxs-lookup"><span data-stu-id="def09-449">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="def09-450">V klientovi .NET je tento přístupový token předán jako http "Bearer Authentication" token (Pomocí `Authorization` hlavičky `Bearer`s typem ).</span><span class="sxs-lookup"><span data-stu-id="def09-450">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="def09-451">V klientovi JavaScript se přístupový token používá jako token Nosiče, **s výjimkou** několika případů, kdy rozhraní API prohlížeče omezují možnost použití záhlaví (konkrétně v požadavcích Události odeslané serverem a WebSockets).</span><span class="sxs-lookup"><span data-stu-id="def09-451">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="def09-452">V těchto případech je přístupový token k `access_token`dispozici jako hodnota řetězce dotazu .</span><span class="sxs-lookup"><span data-stu-id="def09-452">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="def09-453">V klientovi .NET `AccessTokenProvider` lze tuto možnost zadat `WithUrl`pomocí možností delegáta v aplikaci :</span><span class="sxs-lookup"><span data-stu-id="def09-453">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="def09-454">V klientovi JavaScript je přístupový token `accessTokenFactory` konfigurován nastavením `withUrl`pole na objektu options v aplikaci :</span><span class="sxs-lookup"><span data-stu-id="def09-454">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="def09-455">V klientovi SignalR Java můžete nakonfigurovat token nosiče pro ověřování poskytnutím objektu [HttpHubConnectionBuilder.](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)</span><span class="sxs-lookup"><span data-stu-id="def09-455">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="def09-456">Použijte [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) poskytnout [RxJava](https://github.com/ReactiveX/RxJava) [single\<string>](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="def09-456">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="def09-457">S [volánísingle.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), můžete napsat logiku k vytvoření přístupové tokeny pro vašeho klienta.</span><span class="sxs-lookup"><span data-stu-id="def09-457">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="def09-458">Konfigurace možností časového limitu a udržování v životě</span><span class="sxs-lookup"><span data-stu-id="def09-458">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="def09-459">Další možnosti pro konfiguraci časového limitu a `HubConnection` chování udržování naživu jsou k dispozici na samotném objektu:</span><span class="sxs-lookup"><span data-stu-id="def09-459">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="def09-460">.NET</span><span class="sxs-lookup"><span data-stu-id="def09-460">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="def09-461">Možnost</span><span class="sxs-lookup"><span data-stu-id="def09-461">Option</span></span> | <span data-ttu-id="def09-462">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="def09-462">Default value</span></span> | <span data-ttu-id="def09-463">Popis</span><span class="sxs-lookup"><span data-stu-id="def09-463">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="def09-464">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="def09-464">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="def09-465">Časový čas pro aktivitu serveru.</span><span class="sxs-lookup"><span data-stu-id="def09-465">Timeout for server activity.</span></span> <span data-ttu-id="def09-466">Pokud server v tomto intervalu neodeslal zprávu, klient uváží server `Closed` odpojený`onclose` a spustí událost (v Jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="def09-466">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="def09-467">Tato hodnota musí být dostatečně velká, aby zpráva ping byla odeslána ze serveru **a** přijata klientem v časovém intervalu.</span><span class="sxs-lookup"><span data-stu-id="def09-467">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="def09-468">Doporučená hodnota je číslo alespoň dvojnásobek `KeepAliveInterval` hodnoty serveru povolit čas pro příkaz ping k doručení.</span><span class="sxs-lookup"><span data-stu-id="def09-468">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="def09-469">15 sekund</span><span class="sxs-lookup"><span data-stu-id="def09-469">15 seconds</span></span> | <span data-ttu-id="def09-470">Časový limit pro počáteční server handshake.</span><span class="sxs-lookup"><span data-stu-id="def09-470">Timeout for initial server handshake.</span></span> <span data-ttu-id="def09-471">Pokud server neodešle odpověď handshake v tomto intervalu, klient zruší `Closed` handshake`onclose` a spustí událost (v Jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="def09-471">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="def09-472">Toto je rozšířené nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake z důvodu závažné latence sítě.</span><span class="sxs-lookup"><span data-stu-id="def09-472">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="def09-473">Další podrobnosti o procesu handshake naleznete v tématu [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="def09-473">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="def09-474">15 sekund</span><span class="sxs-lookup"><span data-stu-id="def09-474">15 seconds</span></span> | <span data-ttu-id="def09-475">Určuje interval, ve kterém klient odesílá zprávy ping.</span><span class="sxs-lookup"><span data-stu-id="def09-475">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="def09-476">Odesláním libovolné zprávy z klienta obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="def09-476">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="def09-477">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, server považuje klienta za odpojeného.</span><span class="sxs-lookup"><span data-stu-id="def09-477">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="def09-478">V klientovi .NET jsou hodnoty `TimeSpan` časového času určeny jako hodnoty.</span><span class="sxs-lookup"><span data-stu-id="def09-478">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="def09-479">JavaScript</span><span class="sxs-lookup"><span data-stu-id="def09-479">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="def09-480">Možnost</span><span class="sxs-lookup"><span data-stu-id="def09-480">Option</span></span> | <span data-ttu-id="def09-481">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="def09-481">Default value</span></span> | <span data-ttu-id="def09-482">Popis</span><span class="sxs-lookup"><span data-stu-id="def09-482">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="def09-483">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="def09-483">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="def09-484">Časový čas pro aktivitu serveru.</span><span class="sxs-lookup"><span data-stu-id="def09-484">Timeout for server activity.</span></span> <span data-ttu-id="def09-485">Pokud server neodeslal zprávu v tomto intervalu, klient považuje server odpojena a spustí `onclose` událost.</span><span class="sxs-lookup"><span data-stu-id="def09-485">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="def09-486">Tato hodnota musí být dostatečně velká, aby zpráva ping byla odeslána ze serveru **a** přijata klientem v časovém intervalu.</span><span class="sxs-lookup"><span data-stu-id="def09-486">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="def09-487">Doporučená hodnota je číslo alespoň dvojnásobek `KeepAliveInterval` hodnoty serveru povolit čas pro příkaz ping k doručení.</span><span class="sxs-lookup"><span data-stu-id="def09-487">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="def09-488">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="def09-488">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="def09-489">Určuje interval, ve kterém klient odesílá zprávy ping.</span><span class="sxs-lookup"><span data-stu-id="def09-489">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="def09-490">Odesláním libovolné zprávy z klienta obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="def09-490">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="def09-491">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, server považuje klienta za odpojeného.</span><span class="sxs-lookup"><span data-stu-id="def09-491">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="def09-492">Java</span><span class="sxs-lookup"><span data-stu-id="def09-492">Java</span></span>](#tab/java)

| <span data-ttu-id="def09-493">Možnost</span><span class="sxs-lookup"><span data-stu-id="def09-493">Option</span></span> | <span data-ttu-id="def09-494">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="def09-494">Default value</span></span> | <span data-ttu-id="def09-495">Popis</span><span class="sxs-lookup"><span data-stu-id="def09-495">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="def09-496">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="def09-496">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="def09-497">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="def09-497">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="def09-498">Časový čas pro aktivitu serveru.</span><span class="sxs-lookup"><span data-stu-id="def09-498">Timeout for server activity.</span></span> <span data-ttu-id="def09-499">Pokud server neodeslal zprávu v tomto intervalu, klient považuje server odpojena a spustí `onClose` událost.</span><span class="sxs-lookup"><span data-stu-id="def09-499">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="def09-500">Tato hodnota musí být dostatečně velká, aby zpráva ping byla odeslána ze serveru **a** přijata klientem v časovém intervalu.</span><span class="sxs-lookup"><span data-stu-id="def09-500">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="def09-501">Doporučená hodnota je číslo alespoň dvojnásobek `KeepAliveInterval` hodnoty serveru povolit čas pro příkaz ping k doručení.</span><span class="sxs-lookup"><span data-stu-id="def09-501">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="def09-502">15 sekund</span><span class="sxs-lookup"><span data-stu-id="def09-502">15 seconds</span></span> | <span data-ttu-id="def09-503">Časový limit pro počáteční server handshake.</span><span class="sxs-lookup"><span data-stu-id="def09-503">Timeout for initial server handshake.</span></span> <span data-ttu-id="def09-504">Pokud server neodešle odpověď handshake v tomto intervalu, klient zruší `onClose` handshake a spustí událost.</span><span class="sxs-lookup"><span data-stu-id="def09-504">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="def09-505">Toto je rozšířené nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake z důvodu závažné latence sítě.</span><span class="sxs-lookup"><span data-stu-id="def09-505">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="def09-506">Další podrobnosti o procesu handshake naleznete v tématu [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="def09-506">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="def09-507">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="def09-507">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="def09-508">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="def09-508">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="def09-509">Určuje interval, ve kterém klient odesílá zprávy ping.</span><span class="sxs-lookup"><span data-stu-id="def09-509">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="def09-510">Odesláním libovolné zprávy z klienta obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="def09-510">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="def09-511">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, server považuje klienta za odpojeného.</span><span class="sxs-lookup"><span data-stu-id="def09-511">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="def09-512">Konfigurace dalších možností</span><span class="sxs-lookup"><span data-stu-id="def09-512">Configure additional options</span></span>

<span data-ttu-id="def09-513">Další možnosti lze `WithUrl` konfigurovat`withUrl` v metodě `HubConnectionBuilder` (v Jazyce JavaScript) `HttpHubConnectionBuilder` na různých konfiguračních API v klientovi java:</span><span class="sxs-lookup"><span data-stu-id="def09-513">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="def09-514">.NET</span><span class="sxs-lookup"><span data-stu-id="def09-514">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="def09-515">Možnost .NET</span><span class="sxs-lookup"><span data-stu-id="def09-515">.NET Option</span></span> |  <span data-ttu-id="def09-516">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="def09-516">Default value</span></span> | <span data-ttu-id="def09-517">Popis</span><span class="sxs-lookup"><span data-stu-id="def09-517">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="def09-518">Funkce vracející řetězec, který je k dispozici jako ověřovací token Nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="def09-518">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="def09-519">Nastavte tuto `true` na přeskočení kroku vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="def09-519">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="def09-520">**Podporováno pouze v případě, že přenos WebSockets je pouze povolený přenos**.</span><span class="sxs-lookup"><span data-stu-id="def09-520">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="def09-521">Toto nastavení nelze povolit při použití služby Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="def09-521">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="def09-522">Prázdné</span><span class="sxs-lookup"><span data-stu-id="def09-522">Empty</span></span> | <span data-ttu-id="def09-523">Kolekce certifikátů TLS k odeslání k ověření požadavků.</span><span class="sxs-lookup"><span data-stu-id="def09-523">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="def09-524">Prázdné</span><span class="sxs-lookup"><span data-stu-id="def09-524">Empty</span></span> | <span data-ttu-id="def09-525">Kolekce souborů cookie HTTP, které se mají odesílat s každým požadavkem HTTP.</span><span class="sxs-lookup"><span data-stu-id="def09-525">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="def09-526">Prázdné</span><span class="sxs-lookup"><span data-stu-id="def09-526">Empty</span></span> | <span data-ttu-id="def09-527">Pověření k odeslání s každým požadavkem HTTP.</span><span class="sxs-lookup"><span data-stu-id="def09-527">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="def09-528">5 sekund</span><span class="sxs-lookup"><span data-stu-id="def09-528">5 seconds</span></span> | <span data-ttu-id="def09-529">Pouze websockets.</span><span class="sxs-lookup"><span data-stu-id="def09-529">WebSockets only.</span></span> <span data-ttu-id="def09-530">Maximální doba, po kterou klient čeká po zavření, aby server potvrdil požadavek na uzavření.</span><span class="sxs-lookup"><span data-stu-id="def09-530">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="def09-531">Pokud server nepotvrdí uzavření během této doby, klient se odpojí.</span><span class="sxs-lookup"><span data-stu-id="def09-531">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="def09-532">Prázdné</span><span class="sxs-lookup"><span data-stu-id="def09-532">Empty</span></span> | <span data-ttu-id="def09-533">Mapa dalších hlaviček PROTOKOLU HTTP, které se mají odesílat s každým požadavkem HTTP.</span><span class="sxs-lookup"><span data-stu-id="def09-533">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="def09-534">Delegát, který lze použít ke `HttpMessageHandler` konfiguraci nebo nahrazení použitých k odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="def09-534">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="def09-535">Nepoužívá se pro připojení WebSocket.</span><span class="sxs-lookup"><span data-stu-id="def09-535">Not used for WebSocket connections.</span></span> <span data-ttu-id="def09-536">Tento delegát musí vrátit hodnotu bez hodnoty null a obdrží výchozí hodnotu jako parametr.</span><span class="sxs-lookup"><span data-stu-id="def09-536">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="def09-537">Buď upravte nastavení této výchozí hodnoty a `HttpMessageHandler` vraťte ji, nebo vraťte novou instanci.</span><span class="sxs-lookup"><span data-stu-id="def09-537">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="def09-538">**Při výměně obslužné rutiny nezapomeňte zkopírovat nastavení, které chcete zachovat z poskytnuté obslužné rutiny, jinak nakonfigurované možnosti (například soubory cookie a záhlaví) se na novou obslužnou rutinu nevztahují.**</span><span class="sxs-lookup"><span data-stu-id="def09-538">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="def09-539">Proxy HTTP, který se má použít při odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="def09-539">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="def09-540">Nastavte tuto logickou hodnotu tak, aby odesílala výchozí pověření pro požadavky HTTP a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="def09-540">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="def09-541">To umožňuje použití ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="def09-541">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="def09-542">Delegát, který lze použít ke konfiguraci dalších možností WebSocket.</span><span class="sxs-lookup"><span data-stu-id="def09-542">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="def09-543">Přijímá instanci [ClientWebSocketOptions,](/dotnet/api/system.net.websockets.clientwebsocketoptions) která může být použita ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="def09-543">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="def09-544">JavaScript</span><span class="sxs-lookup"><span data-stu-id="def09-544">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="def09-545">Možnost JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="def09-545">JavaScript Option</span></span> | <span data-ttu-id="def09-546">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="def09-546">Default Value</span></span> | <span data-ttu-id="def09-547">Popis</span><span class="sxs-lookup"><span data-stu-id="def09-547">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="def09-548">Funkce vracející řetězec, který je k dispozici jako ověřovací token Nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="def09-548">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="def09-549">Nastavte tuto `true` na přeskočení kroku vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="def09-549">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="def09-550">**Podporováno pouze v případě, že přenos WebSockets je pouze povolený přenos**.</span><span class="sxs-lookup"><span data-stu-id="def09-550">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="def09-551">Toto nastavení nelze povolit při použití služby Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="def09-551">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="def09-552">Java</span><span class="sxs-lookup"><span data-stu-id="def09-552">Java</span></span>](#tab/java)

| <span data-ttu-id="def09-553">Java možnost</span><span class="sxs-lookup"><span data-stu-id="def09-553">Java Option</span></span> | <span data-ttu-id="def09-554">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="def09-554">Default Value</span></span> | <span data-ttu-id="def09-555">Popis</span><span class="sxs-lookup"><span data-stu-id="def09-555">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="def09-556">Funkce vracející řetězec, který je k dispozici jako ověřovací token Nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="def09-556">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="def09-557">Nastavte tuto `true` na přeskočení kroku vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="def09-557">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="def09-558">**Podporováno pouze v případě, že přenos WebSockets je pouze povolený přenos**.</span><span class="sxs-lookup"><span data-stu-id="def09-558">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="def09-559">Toto nastavení nelze povolit při použití služby Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="def09-559">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="def09-560">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="def09-560">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="def09-561">Prázdné</span><span class="sxs-lookup"><span data-stu-id="def09-561">Empty</span></span> | <span data-ttu-id="def09-562">Mapa dalších hlaviček PROTOKOLU HTTP, které se mají odesílat s každým požadavkem HTTP.</span><span class="sxs-lookup"><span data-stu-id="def09-562">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="def09-563">V klientovi .NET mohou být tyto možnosti změněny možnostmi, které delegát poskytuje `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="def09-563">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="def09-564">V klientovi JavaScript mohou být tyto možnosti poskytnuty `withUrl`v objektu JavaScript u :</span><span class="sxs-lookup"><span data-stu-id="def09-564">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="def09-565">V klientovi Java lze tyto možnosti nakonfigurovat pomocí metod na vráceném `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="def09-565">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="def09-566">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="def09-566">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="def09-567">Možnosti serializace balíčku JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="def09-567">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="def09-568">ASP.NET Core SignalR podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="def09-568">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="def09-569">Každý protokol má možnosti konfigurace serializace.</span><span class="sxs-lookup"><span data-stu-id="def09-569">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="def09-570">Serializaci JSON lze na serveru nakonfigurovat pomocí metody rozšíření [AddJsonProtocol,](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) kterou `Startup.ConfigureServices` lze přidat za [addsignalr](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) ve vaší metodě.</span><span class="sxs-lookup"><span data-stu-id="def09-570">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="def09-571">Metoda `AddJsonProtocol` trvá delegáta, který `options` obdrží objekt.</span><span class="sxs-lookup"><span data-stu-id="def09-571">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="def09-572">[Vlastnost PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) u tohoto objektu je JSON.NET `JsonSerializerSettings` objekt, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="def09-572">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="def09-573">Další informace naleznete v [dokumentaci k JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="def09-573">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="def09-574">Jako příklad nakonfigurujte serializátor tak, aby používal názvy vlastností "PascalCase", `Startup.ConfigureServices`namísto výchozích názvů camelCase, použijte následující kód v aplikaci :</span><span class="sxs-lookup"><span data-stu-id="def09-574">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="def09-575">V klientovi .NET `AddJsonProtocol` existuje stejná metoda rozšíření v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="def09-575">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="def09-576">Obor `Microsoft.Extensions.DependencyInjection` názvů musí být importován, aby se vyřešila metoda rozšíření:</span><span class="sxs-lookup"><span data-stu-id="def09-576">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="def09-577">V tuto chvíli není možné konfigurovat serializaci JSON v klientovi JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="def09-577">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="def09-578">Možnosti serializace messagepacku</span><span class="sxs-lookup"><span data-stu-id="def09-578">MessagePack serialization options</span></span>

<span data-ttu-id="def09-579">Serializace messagepack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol.](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol)</span><span class="sxs-lookup"><span data-stu-id="def09-579">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="def09-580">Další podrobnosti najdete [v tématu MessagePack v SignalR.](xref:signalr/messagepackhubprotocol)</span><span class="sxs-lookup"><span data-stu-id="def09-580">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="def09-581">V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="def09-581">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="def09-582">Konfigurace možností serveru</span><span class="sxs-lookup"><span data-stu-id="def09-582">Configure server options</span></span>

<span data-ttu-id="def09-583">Následující tabulka popisuje možnosti konfigurace rozbočovačů SignalR:</span><span class="sxs-lookup"><span data-stu-id="def09-583">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="def09-584">Možnost</span><span class="sxs-lookup"><span data-stu-id="def09-584">Option</span></span> | <span data-ttu-id="def09-585">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="def09-585">Default Value</span></span> | <span data-ttu-id="def09-586">Popis</span><span class="sxs-lookup"><span data-stu-id="def09-586">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="def09-587">15 sekund</span><span class="sxs-lookup"><span data-stu-id="def09-587">15 seconds</span></span> | <span data-ttu-id="def09-588">Pokud klient neodešle počáteční zprávu handshake v tomto časovém intervalu, připojení je ukončeno.</span><span class="sxs-lookup"><span data-stu-id="def09-588">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="def09-589">Toto je rozšířené nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake z důvodu závažné latence sítě.</span><span class="sxs-lookup"><span data-stu-id="def09-589">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="def09-590">Další podrobnosti o procesu handshake naleznete v tématu [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="def09-590">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="def09-591">15 sekund</span><span class="sxs-lookup"><span data-stu-id="def09-591">15 seconds</span></span> | <span data-ttu-id="def09-592">Pokud server neodeslal zprávu v tomto intervalu, je automaticky odeslána zpráva ping, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="def09-592">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="def09-593">Při `KeepAliveInterval`změně změňte `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení klienta.</span><span class="sxs-lookup"><span data-stu-id="def09-593">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="def09-594">Doporučená `ServerTimeout` / `serverTimeoutInMilliseconds` hodnota je `KeepAliveInterval` dvojnásobek hodnoty.</span><span class="sxs-lookup"><span data-stu-id="def09-594">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="def09-595">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="def09-595">All installed protocols</span></span> | <span data-ttu-id="def09-596">Protokoly podporované tímto centrem.</span><span class="sxs-lookup"><span data-stu-id="def09-596">Protocols supported by this hub.</span></span> <span data-ttu-id="def09-597">Ve výchozím nastavení jsou povoleny všechny protokoly registrované na serveru, ale protokoly mohou být odebrány z tohoto seznamu zakázat konkrétní protokoly pro jednotlivé rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="def09-597">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="def09-598">Pokud `true`, podrobné zprávy o výjimce jsou vráceny klientům při vyvolání výjimky v Hub metody.</span><span class="sxs-lookup"><span data-stu-id="def09-598">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="def09-599">Výchozí hodnota `false`je , protože tyto zprávy o výjimce mohou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="def09-599">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="def09-600">Možnosti lze nakonfigurovat pro všechny rozbočovače `Startup.ConfigureServices`poskytnutím možnosti delegáta volání v aplikaci `AddSignalR` .</span><span class="sxs-lookup"><span data-stu-id="def09-600">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="def09-601">Možnosti pro jeden rozbočovač přepíší globální možnosti uvedené v `AddSignalR` aplikaci a lze je konfigurovat pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span><span class="sxs-lookup"><span data-stu-id="def09-601">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="def09-602">Rozšířené možnosti konfigurace protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="def09-602">Advanced HTTP configuration options</span></span>

<span data-ttu-id="def09-603">Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="def09-603">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="def09-604">Tyto možnosti jsou konfigurovány předáním delegáta [maphub\<t>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="def09-604">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="def09-605">Následující tabulka popisuje možnosti konfigurace ASP.NET pokročilých možností protokolu HTTP nástroje Core SignalR:</span><span class="sxs-lookup"><span data-stu-id="def09-605">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="def09-606">Možnost</span><span class="sxs-lookup"><span data-stu-id="def09-606">Option</span></span> | <span data-ttu-id="def09-607">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="def09-607">Default Value</span></span> | <span data-ttu-id="def09-608">Popis</span><span class="sxs-lookup"><span data-stu-id="def09-608">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="def09-609">32 KB</span><span class="sxs-lookup"><span data-stu-id="def09-609">32 KB</span></span> | <span data-ttu-id="def09-610">Maximální počet bajtů přijatých od klienta, které server vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="def09-610">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="def09-611">Zvýšení této hodnoty umožňuje serveru přijímat větší zprávy, ale může negativně ovlivnit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="def09-611">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="def09-612">Data automaticky získaná z `Authorize` atributů použitých pro třídu Hub.</span><span class="sxs-lookup"><span data-stu-id="def09-612">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="def09-613">Seznam objektů [IAuthorizeData,](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) které slouží k určení, zda je klient oprávněn připojit k rozbočovači.</span><span class="sxs-lookup"><span data-stu-id="def09-613">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="def09-614">32 KB</span><span class="sxs-lookup"><span data-stu-id="def09-614">32 KB</span></span> | <span data-ttu-id="def09-615">Maximální počet bajtů odeslaných aplikací, které server vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="def09-615">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="def09-616">Zvýšení této hodnoty umožňuje serveru odesílat větší zprávy, ale může negativně ovlivnit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="def09-616">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="def09-617">Všechny přenosy jsou povoleny.</span><span class="sxs-lookup"><span data-stu-id="def09-617">All Transports are enabled.</span></span> | <span data-ttu-id="def09-618">Bit příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenosy klienta můžete použít k připojení.</span><span class="sxs-lookup"><span data-stu-id="def09-618">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="def09-619">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="def09-619">See below.</span></span> | <span data-ttu-id="def09-620">Další možnosti specifické pro přenos dlouhé dotazování.</span><span class="sxs-lookup"><span data-stu-id="def09-620">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="def09-621">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="def09-621">See below.</span></span> | <span data-ttu-id="def09-622">Další možnosti specifické pro přenos WebSockets.</span><span class="sxs-lookup"><span data-stu-id="def09-622">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="def09-623">Přenos dlouhé dotazování má další možnosti, které lze nakonfigurovat pomocí vlastnosti: `LongPolling`</span><span class="sxs-lookup"><span data-stu-id="def09-623">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="def09-624">Možnost</span><span class="sxs-lookup"><span data-stu-id="def09-624">Option</span></span> | <span data-ttu-id="def09-625">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="def09-625">Default Value</span></span> | <span data-ttu-id="def09-626">Popis</span><span class="sxs-lookup"><span data-stu-id="def09-626">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="def09-627">90 sekund</span><span class="sxs-lookup"><span data-stu-id="def09-627">90 seconds</span></span> | <span data-ttu-id="def09-628">Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jednoho požadavku na dotazování.</span><span class="sxs-lookup"><span data-stu-id="def09-628">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="def09-629">Snížení této hodnoty způsobí, že klient vydávat nové požadavky na dotazování častěji.</span><span class="sxs-lookup"><span data-stu-id="def09-629">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="def09-630">Přenos WebSocket má další možnosti, `WebSockets` které lze nakonfigurovat pomocí vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="def09-630">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="def09-631">Možnost</span><span class="sxs-lookup"><span data-stu-id="def09-631">Option</span></span> | <span data-ttu-id="def09-632">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="def09-632">Default Value</span></span> | <span data-ttu-id="def09-633">Popis</span><span class="sxs-lookup"><span data-stu-id="def09-633">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="def09-634">5 sekund</span><span class="sxs-lookup"><span data-stu-id="def09-634">5 seconds</span></span> | <span data-ttu-id="def09-635">Po ukončení serveru, pokud se klientovi nepodaří zavřít v tomto časovém intervalu, je připojení ukončeno.</span><span class="sxs-lookup"><span data-stu-id="def09-635">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="def09-636">Delegát, který lze použít `Sec-WebSocket-Protocol` k nastavení záhlaví na vlastní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="def09-636">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="def09-637">Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že vrátí požadovanou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="def09-637">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="def09-638">Konfigurace možností klienta</span><span class="sxs-lookup"><span data-stu-id="def09-638">Configure client options</span></span>

<span data-ttu-id="def09-639">Možnosti klienta lze `HubConnectionBuilder` konfigurovat na typu (k dispozici v klientech .NET a JavaScript).</span><span class="sxs-lookup"><span data-stu-id="def09-639">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="def09-640">Je také k dispozici v klientovi `HttpHubConnectionBuilder` Java, ale podtřída je to, co `HubConnection` obsahuje možnosti konfigurace tvůrce, stejně jako na sobě.</span><span class="sxs-lookup"><span data-stu-id="def09-640">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="def09-641">Konfigurace protokolování</span><span class="sxs-lookup"><span data-stu-id="def09-641">Configure logging</span></span>

<span data-ttu-id="def09-642">Protokolování je konfigurováno v `ConfigureLogging` klientovi .NET pomocí metody.</span><span class="sxs-lookup"><span data-stu-id="def09-642">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="def09-643">Zprostředkovatelé protokolování a filtry mohou být registrovány stejným způsobem jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="def09-643">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="def09-644">Další informace naleznete v dokumentaci [protokolování ASP.NET jádra.](xref:fundamentals/logging/index)</span><span class="sxs-lookup"><span data-stu-id="def09-644">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="def09-645">Chcete-li zaregistrovat zprostředkovatele protokolování, je nutné nainstalovat potřebné balíčky.</span><span class="sxs-lookup"><span data-stu-id="def09-645">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="def09-646">Úplný seznam najdete v části [Vestavěné zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentech.</span><span class="sxs-lookup"><span data-stu-id="def09-646">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="def09-647">Chcete-li například povolit `Microsoft.Extensions.Logging.Console` protokolování konzoly, nainstalujte balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="def09-647">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="def09-648">Volání `AddConsole` metody rozšíření:</span><span class="sxs-lookup"><span data-stu-id="def09-648">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="def09-649">V klientovi JavaScript `configureLogging` existuje podobná metoda.</span><span class="sxs-lookup"><span data-stu-id="def09-649">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="def09-650">Zadejte `LogLevel` hodnotu označující minimální úroveň zpráv protokolu k vytvoření.</span><span class="sxs-lookup"><span data-stu-id="def09-650">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="def09-651">Protokoly jsou zapsány do okna konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="def09-651">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="def09-652">Chcete-li protokolování `signalR.LogLevel.None` zcela `configureLogging` zakázat, zadejte v metodě.</span><span class="sxs-lookup"><span data-stu-id="def09-652">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="def09-653">Další informace o protokolování naleznete v [dokumentaci k diagnostice signalr .](xref:signalr/diagnostics)</span><span class="sxs-lookup"><span data-stu-id="def09-653">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="def09-654">Klient Java SignalR používá pro protokolování knihovnu [SLF4J.](https://www.slf4j.org/)</span><span class="sxs-lookup"><span data-stu-id="def09-654">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="def09-655">Jedná se o rozhraní API pro protokolování vysoké úrovně, které umožňuje uživatelům knihovny zvolit vlastní implementaci protokolování tím, že přivedou konkrétní závislost protokolování.</span><span class="sxs-lookup"><span data-stu-id="def09-655">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="def09-656">Následující fragment kódu ukazuje, jak `java.util.logging` se používat s klientem Java SignalR.</span><span class="sxs-lookup"><span data-stu-id="def09-656">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="def09-657">Pokud nenakonfigurujete protokolování v závislostech, SLF4J načte výchozí protokolovací nástroj bez operace s následující varovnou zprávou:</span><span class="sxs-lookup"><span data-stu-id="def09-657">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="def09-658">To lze bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="def09-658">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="def09-659">Konfigurace povolených přenosů</span><span class="sxs-lookup"><span data-stu-id="def09-659">Configure allowed transports</span></span>

<span data-ttu-id="def09-660">Přenosy používané SignalR lze konfigurovat `WithUrl` ve`withUrl` volání (v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="def09-660">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="def09-661">Bitové nebo hodnoty `HttpTransportType` lze omezit klienta používat pouze zadané přenosy.</span><span class="sxs-lookup"><span data-stu-id="def09-661">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="def09-662">Všechny přenosy jsou ve výchozím nastavení povoleny.</span><span class="sxs-lookup"><span data-stu-id="def09-662">All transports are enabled by default.</span></span>

<span data-ttu-id="def09-663">Chcete-li například zakázat přenos událostí odeslaných serverem, ale povolit připojení WebSockets a Long Polling:</span><span class="sxs-lookup"><span data-stu-id="def09-663">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="def09-664">V klientovi JavaScript jsou přenosy `transport` konfigurovány nastavením pole `withUrl`na objektu options, který je k dispozici :</span><span class="sxs-lookup"><span data-stu-id="def09-664">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="def09-665">Konfigurace ověřování nosiče</span><span class="sxs-lookup"><span data-stu-id="def09-665">Configure bearer authentication</span></span>

<span data-ttu-id="def09-666">Chcete-li poskytnout ověřovací data spolu s `AccessTokenProvider` požadavky`accessTokenFactory` SignalR, použijte možnost ( v Jazyce JavaScript a určete funkci, která vrátí požadovaný přístupový token.</span><span class="sxs-lookup"><span data-stu-id="def09-666">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="def09-667">V klientovi .NET je tento přístupový token předán jako http "Bearer Authentication" token (Pomocí `Authorization` hlavičky `Bearer`s typem ).</span><span class="sxs-lookup"><span data-stu-id="def09-667">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="def09-668">V klientovi JavaScript se přístupový token používá jako token Nosiče, **s výjimkou** několika případů, kdy rozhraní API prohlížeče omezují možnost použití záhlaví (konkrétně v požadavcích Události odeslané serverem a WebSockets).</span><span class="sxs-lookup"><span data-stu-id="def09-668">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="def09-669">V těchto případech je přístupový token k `access_token`dispozici jako hodnota řetězce dotazu .</span><span class="sxs-lookup"><span data-stu-id="def09-669">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="def09-670">V klientovi .NET `AccessTokenProvider` lze tuto možnost zadat `WithUrl`pomocí možností delegáta v aplikaci :</span><span class="sxs-lookup"><span data-stu-id="def09-670">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="def09-671">V klientovi JavaScript je přístupový token `accessTokenFactory` konfigurován nastavením `withUrl`pole na objektu options v aplikaci :</span><span class="sxs-lookup"><span data-stu-id="def09-671">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="def09-672">V klientovi SignalR Java můžete nakonfigurovat token nosiče pro ověřování poskytnutím objektu [HttpHubConnectionBuilder.](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)</span><span class="sxs-lookup"><span data-stu-id="def09-672">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="def09-673">Použijte [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) poskytnout [RxJava](https://github.com/ReactiveX/RxJava) [single\<string>](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="def09-673">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="def09-674">S [volánísingle.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), můžete napsat logiku k vytvoření přístupové tokeny pro vašeho klienta.</span><span class="sxs-lookup"><span data-stu-id="def09-674">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="def09-675">Konfigurace možností časového limitu a udržování v životě</span><span class="sxs-lookup"><span data-stu-id="def09-675">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="def09-676">Další možnosti pro konfiguraci časového limitu a `HubConnection` chování udržování naživu jsou k dispozici na samotném objektu:</span><span class="sxs-lookup"><span data-stu-id="def09-676">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="def09-677">.NET</span><span class="sxs-lookup"><span data-stu-id="def09-677">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="def09-678">Možnost</span><span class="sxs-lookup"><span data-stu-id="def09-678">Option</span></span> | <span data-ttu-id="def09-679">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="def09-679">Default value</span></span> | <span data-ttu-id="def09-680">Popis</span><span class="sxs-lookup"><span data-stu-id="def09-680">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="def09-681">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="def09-681">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="def09-682">Časový čas pro aktivitu serveru.</span><span class="sxs-lookup"><span data-stu-id="def09-682">Timeout for server activity.</span></span> <span data-ttu-id="def09-683">Pokud server v tomto intervalu neodeslal zprávu, klient uváží server `Closed` odpojený`onclose` a spustí událost (v Jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="def09-683">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="def09-684">Tato hodnota musí být dostatečně velká, aby zpráva ping byla odeslána ze serveru **a** přijata klientem v časovém intervalu.</span><span class="sxs-lookup"><span data-stu-id="def09-684">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="def09-685">Doporučená hodnota je číslo alespoň dvojnásobek `KeepAliveInterval` hodnoty serveru povolit čas pro příkaz ping k doručení.</span><span class="sxs-lookup"><span data-stu-id="def09-685">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="def09-686">15 sekund</span><span class="sxs-lookup"><span data-stu-id="def09-686">15 seconds</span></span> | <span data-ttu-id="def09-687">Časový limit pro počáteční server handshake.</span><span class="sxs-lookup"><span data-stu-id="def09-687">Timeout for initial server handshake.</span></span> <span data-ttu-id="def09-688">Pokud server neodešle odpověď handshake v tomto intervalu, klient zruší `Closed` handshake`onclose` a spustí událost (v Jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="def09-688">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="def09-689">Toto je rozšířené nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake z důvodu závažné latence sítě.</span><span class="sxs-lookup"><span data-stu-id="def09-689">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="def09-690">Další podrobnosti o procesu handshake naleznete v tématu [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="def09-690">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="def09-691">V klientovi .NET jsou hodnoty `TimeSpan` časového času určeny jako hodnoty.</span><span class="sxs-lookup"><span data-stu-id="def09-691">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="def09-692">JavaScript</span><span class="sxs-lookup"><span data-stu-id="def09-692">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="def09-693">Možnost</span><span class="sxs-lookup"><span data-stu-id="def09-693">Option</span></span> | <span data-ttu-id="def09-694">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="def09-694">Default value</span></span> | <span data-ttu-id="def09-695">Popis</span><span class="sxs-lookup"><span data-stu-id="def09-695">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="def09-696">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="def09-696">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="def09-697">Časový čas pro aktivitu serveru.</span><span class="sxs-lookup"><span data-stu-id="def09-697">Timeout for server activity.</span></span> <span data-ttu-id="def09-698">Pokud server neodeslal zprávu v tomto intervalu, klient považuje server odpojena a spustí `onclose` událost.</span><span class="sxs-lookup"><span data-stu-id="def09-698">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="def09-699">Tato hodnota musí být dostatečně velká, aby zpráva ping byla odeslána ze serveru **a** přijata klientem v časovém intervalu.</span><span class="sxs-lookup"><span data-stu-id="def09-699">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="def09-700">Doporučená hodnota je číslo alespoň dvojnásobek `KeepAliveInterval` hodnoty serveru povolit čas pro příkaz ping k doručení.</span><span class="sxs-lookup"><span data-stu-id="def09-700">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="def09-701">Java</span><span class="sxs-lookup"><span data-stu-id="def09-701">Java</span></span>](#tab/java)

| <span data-ttu-id="def09-702">Možnost</span><span class="sxs-lookup"><span data-stu-id="def09-702">Option</span></span> | <span data-ttu-id="def09-703">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="def09-703">Default value</span></span> | <span data-ttu-id="def09-704">Popis</span><span class="sxs-lookup"><span data-stu-id="def09-704">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="def09-705">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="def09-705">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="def09-706">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="def09-706">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="def09-707">Časový čas pro aktivitu serveru.</span><span class="sxs-lookup"><span data-stu-id="def09-707">Timeout for server activity.</span></span> <span data-ttu-id="def09-708">Pokud server neodeslal zprávu v tomto intervalu, klient považuje server odpojena a spustí `onClose` událost.</span><span class="sxs-lookup"><span data-stu-id="def09-708">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="def09-709">Tato hodnota musí být dostatečně velká, aby zpráva ping byla odeslána ze serveru **a** přijata klientem v časovém intervalu.</span><span class="sxs-lookup"><span data-stu-id="def09-709">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="def09-710">Doporučená hodnota je číslo alespoň dvojnásobek `KeepAliveInterval` hodnoty serveru, aby čas pro příkaz ping k doručení.</span><span class="sxs-lookup"><span data-stu-id="def09-710">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="def09-711">15 sekund</span><span class="sxs-lookup"><span data-stu-id="def09-711">15 seconds</span></span> | <span data-ttu-id="def09-712">Časový limit pro počáteční server handshake.</span><span class="sxs-lookup"><span data-stu-id="def09-712">Timeout for initial server handshake.</span></span> <span data-ttu-id="def09-713">Pokud server neodešle odpověď handshake v tomto intervalu, klient zruší `onClose` handshake a spustí událost.</span><span class="sxs-lookup"><span data-stu-id="def09-713">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="def09-714">Toto je rozšířené nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake z důvodu závažné latence sítě.</span><span class="sxs-lookup"><span data-stu-id="def09-714">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="def09-715">Další podrobnosti o procesu handshake naleznete v tématu [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="def09-715">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="def09-716">Konfigurace dalších možností</span><span class="sxs-lookup"><span data-stu-id="def09-716">Configure additional options</span></span>

<span data-ttu-id="def09-717">Další možnosti lze `WithUrl` konfigurovat`withUrl` v metodě `HubConnectionBuilder` (v Jazyce JavaScript) `HttpHubConnectionBuilder` na různých konfiguračních API v klientovi java:</span><span class="sxs-lookup"><span data-stu-id="def09-717">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="def09-718">.NET</span><span class="sxs-lookup"><span data-stu-id="def09-718">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="def09-719">Možnost .NET</span><span class="sxs-lookup"><span data-stu-id="def09-719">.NET Option</span></span> |  <span data-ttu-id="def09-720">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="def09-720">Default value</span></span> | <span data-ttu-id="def09-721">Popis</span><span class="sxs-lookup"><span data-stu-id="def09-721">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="def09-722">Funkce vracející řetězec, který je k dispozici jako ověřovací token Nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="def09-722">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="def09-723">Nastavte tuto `true` na přeskočení kroku vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="def09-723">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="def09-724">**Podporováno pouze v případě, že přenos WebSockets je pouze povolený přenos**.</span><span class="sxs-lookup"><span data-stu-id="def09-724">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="def09-725">Toto nastavení nelze povolit při použití služby Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="def09-725">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="def09-726">Prázdné</span><span class="sxs-lookup"><span data-stu-id="def09-726">Empty</span></span> | <span data-ttu-id="def09-727">Kolekce certifikátů TLS k odeslání k ověření požadavků.</span><span class="sxs-lookup"><span data-stu-id="def09-727">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="def09-728">Prázdné</span><span class="sxs-lookup"><span data-stu-id="def09-728">Empty</span></span> | <span data-ttu-id="def09-729">Kolekce souborů cookie HTTP, které se mají odesílat s každým požadavkem HTTP.</span><span class="sxs-lookup"><span data-stu-id="def09-729">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="def09-730">Prázdné</span><span class="sxs-lookup"><span data-stu-id="def09-730">Empty</span></span> | <span data-ttu-id="def09-731">Pověření k odeslání s každým požadavkem HTTP.</span><span class="sxs-lookup"><span data-stu-id="def09-731">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="def09-732">5 sekund</span><span class="sxs-lookup"><span data-stu-id="def09-732">5 seconds</span></span> | <span data-ttu-id="def09-733">Pouze websockets.</span><span class="sxs-lookup"><span data-stu-id="def09-733">WebSockets only.</span></span> <span data-ttu-id="def09-734">Maximální doba, po kterou klient čeká po zavření, aby server potvrdil požadavek na uzavření.</span><span class="sxs-lookup"><span data-stu-id="def09-734">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="def09-735">Pokud server nepotvrdí uzavření během této doby, klient se odpojí.</span><span class="sxs-lookup"><span data-stu-id="def09-735">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="def09-736">Prázdné</span><span class="sxs-lookup"><span data-stu-id="def09-736">Empty</span></span> | <span data-ttu-id="def09-737">Mapa dalších hlaviček PROTOKOLU HTTP, které se mají odesílat s každým požadavkem HTTP.</span><span class="sxs-lookup"><span data-stu-id="def09-737">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="def09-738">Delegát, který lze použít ke `HttpMessageHandler` konfiguraci nebo nahrazení použitých k odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="def09-738">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="def09-739">Nepoužívá se pro připojení WebSocket.</span><span class="sxs-lookup"><span data-stu-id="def09-739">Not used for WebSocket connections.</span></span> <span data-ttu-id="def09-740">Tento delegát musí vrátit hodnotu bez hodnoty null a obdrží výchozí hodnotu jako parametr.</span><span class="sxs-lookup"><span data-stu-id="def09-740">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="def09-741">Buď upravte nastavení této výchozí hodnoty a `HttpMessageHandler` vraťte ji, nebo vraťte novou instanci.</span><span class="sxs-lookup"><span data-stu-id="def09-741">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="def09-742">**Při výměně obslužné rutiny nezapomeňte zkopírovat nastavení, které chcete zachovat z poskytnuté obslužné rutiny, jinak nakonfigurované možnosti (například soubory cookie a záhlaví) se na novou obslužnou rutinu nevztahují.**</span><span class="sxs-lookup"><span data-stu-id="def09-742">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="def09-743">Proxy HTTP, který se má použít při odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="def09-743">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="def09-744">Nastavte tuto logickou hodnotu tak, aby odesílala výchozí pověření pro požadavky HTTP a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="def09-744">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="def09-745">To umožňuje použití ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="def09-745">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="def09-746">Delegát, který lze použít ke konfiguraci dalších možností WebSocket.</span><span class="sxs-lookup"><span data-stu-id="def09-746">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="def09-747">Přijímá instanci [ClientWebSocketOptions,](/dotnet/api/system.net.websockets.clientwebsocketoptions) která může být použita ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="def09-747">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="def09-748">JavaScript</span><span class="sxs-lookup"><span data-stu-id="def09-748">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="def09-749">Možnost JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="def09-749">JavaScript Option</span></span> | <span data-ttu-id="def09-750">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="def09-750">Default Value</span></span> | <span data-ttu-id="def09-751">Popis</span><span class="sxs-lookup"><span data-stu-id="def09-751">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="def09-752">Funkce vracející řetězec, který je k dispozici jako ověřovací token Nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="def09-752">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="def09-753">Nastavte tuto `true` na přeskočení kroku vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="def09-753">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="def09-754">**Podporováno pouze v případě, že přenos WebSockets je pouze povolený přenos**.</span><span class="sxs-lookup"><span data-stu-id="def09-754">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="def09-755">Toto nastavení nelze povolit při použití služby Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="def09-755">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="def09-756">Java</span><span class="sxs-lookup"><span data-stu-id="def09-756">Java</span></span>](#tab/java)

| <span data-ttu-id="def09-757">Java možnost</span><span class="sxs-lookup"><span data-stu-id="def09-757">Java Option</span></span> | <span data-ttu-id="def09-758">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="def09-758">Default Value</span></span> | <span data-ttu-id="def09-759">Popis</span><span class="sxs-lookup"><span data-stu-id="def09-759">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="def09-760">Funkce vracející řetězec, který je k dispozici jako ověřovací token Nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="def09-760">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="def09-761">Nastavte tuto `true` na přeskočení kroku vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="def09-761">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="def09-762">**Podporováno pouze v případě, že přenos WebSockets je pouze povolený přenos**.</span><span class="sxs-lookup"><span data-stu-id="def09-762">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="def09-763">Toto nastavení nelze povolit při použití služby Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="def09-763">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="def09-764">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="def09-764">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="def09-765">Prázdné</span><span class="sxs-lookup"><span data-stu-id="def09-765">Empty</span></span> | <span data-ttu-id="def09-766">Mapa dalších hlaviček PROTOKOLU HTTP, které se mají odesílat s každým požadavkem HTTP.</span><span class="sxs-lookup"><span data-stu-id="def09-766">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="def09-767">V klientovi .NET mohou být tyto možnosti změněny možnostmi, které delegát poskytuje `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="def09-767">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="def09-768">V klientovi JavaScript mohou být tyto možnosti poskytnuty `withUrl`v objektu JavaScript u :</span><span class="sxs-lookup"><span data-stu-id="def09-768">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="def09-769">V klientovi Java lze tyto možnosti nakonfigurovat pomocí metod na vráceném `HttpHubConnectionBuilder``HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="def09-769">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="def09-770">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="def09-770">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
