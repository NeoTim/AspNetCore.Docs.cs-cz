---
title: Konfigurace ASP.NET Core SignalR
author: bradygaster
description: Naučte se konfigurovat aplikace ASP.NET Core SignalR.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 12/10/2019
no-loc:
- SignalR
uid: signalr/configuration
ms.openlocfilehash: c225ff88110dc17185a430ac1c422d2433306115
ms.sourcegitcommit: 2cb857f0de774df421e35289662ba92cfe56ffd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75358109"
---
# <a name="aspnet-core-opno-locsignalr-configuration"></a><span data-ttu-id="900c8-103">Konfigurace ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="900c8-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="900c8-104">Možnosti serializace JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="900c8-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="900c8-105">ASP.NET Core SignalR podporuje pro kódování zprávy dva protokoly: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="900c8-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="900c8-106">Každý protokol má možnosti konfigurace serializace.</span><span class="sxs-lookup"><span data-stu-id="900c8-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="900c8-107">Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="900c8-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="900c8-108">`AddJsonProtocol` lze přidat po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="900c8-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="900c8-109">Metoda `AddJsonProtocol` přebírá delegáta, který přijímá objekt `options`.</span><span class="sxs-lookup"><span data-stu-id="900c8-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="900c8-110">Vlastnost [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) tohoto objektu je objekt <xref:System.Text.Json.JsonSerializerOptions> `System.Text.Json`, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="900c8-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="900c8-111">Další informace naleznete v [dokumentaci System. text. JSON](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="900c8-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="900c8-112">Chcete-li například nakonfigurovat serializátor, aby neměnil velká a malá písmena názvů vlastností namísto výchozích názvů "camelCase", použijte následující kód v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="900c8-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="900c8-113">V klientovi .NET existuje stejná `AddJsonProtocol` metoda rozšíření v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="900c8-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="900c8-114">Aby bylo možné přeložit metodu rozšíření, je třeba importovat obor názvů `Microsoft.Extensions.DependencyInjection`:</span><span class="sxs-lookup"><span data-stu-id="900c8-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="900c8-115">V tuto chvíli není možné konfigurovat serializaci JSON v klientovi jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="900c8-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="900c8-116">Přepnout na Newtonsoft. JSON</span><span class="sxs-lookup"><span data-stu-id="900c8-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="900c8-117">Pokud potřebujete funkce `Newtonsoft.Json`, které nejsou v `System.Text.Json`podporované, přečtěte si téma [Přepnutí na Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="900c8-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="900c8-118">Možnosti serializace MessagePack</span><span class="sxs-lookup"><span data-stu-id="900c8-118">MessagePack serialization options</span></span>

<span data-ttu-id="900c8-119">Serializaci MessagePack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="900c8-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="900c8-120">Další podrobnosti najdete [v tématu MessagePack v SignalR](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="900c8-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="900c8-121">V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi v jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="900c8-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="900c8-122">Konfigurovat možnosti serveru</span><span class="sxs-lookup"><span data-stu-id="900c8-122">Configure server options</span></span>

<span data-ttu-id="900c8-123">Následující tabulka popisuje možnosti konfigurace SignalRch Center:</span><span class="sxs-lookup"><span data-stu-id="900c8-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="900c8-124">Možnost</span><span class="sxs-lookup"><span data-stu-id="900c8-124">Option</span></span> | <span data-ttu-id="900c8-125">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="900c8-125">Default Value</span></span> | <span data-ttu-id="900c8-126">Popis</span><span class="sxs-lookup"><span data-stu-id="900c8-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="900c8-127">30 sekund</span><span class="sxs-lookup"><span data-stu-id="900c8-127">30 seconds</span></span> | <span data-ttu-id="900c8-128">Server bude předpokládat, že je klient odpojený, pokud v tomto intervalu neobdržel zprávu (včetně Keep-Alive).</span><span class="sxs-lookup"><span data-stu-id="900c8-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="900c8-129">Může trvat delší dobu, než je tento časový limit, aby bylo možné klienta ve skutečnosti označit jako odpojený, protože to je implementováno.</span><span class="sxs-lookup"><span data-stu-id="900c8-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="900c8-130">Doporučená hodnota je dvojnásobná hodnota `KeepAliveInterval`.</span><span class="sxs-lookup"><span data-stu-id="900c8-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="900c8-131">15 sekund</span><span class="sxs-lookup"><span data-stu-id="900c8-131">15 seconds</span></span> | <span data-ttu-id="900c8-132">Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře.</span><span class="sxs-lookup"><span data-stu-id="900c8-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="900c8-133">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="900c8-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="900c8-134">Další informace o procesu handshake najdete v tématu [specifikace protokoluSignalR hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="900c8-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="900c8-135">15 sekund</span><span class="sxs-lookup"><span data-stu-id="900c8-135">15 seconds</span></span> | <span data-ttu-id="900c8-136">Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="900c8-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="900c8-137">Při změně `KeepAliveInterval`změňte nastavení `ServerTimeout`/`serverTimeoutInMilliseconds` na klientovi.</span><span class="sxs-lookup"><span data-stu-id="900c8-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="900c8-138">Doporučená `ServerTimeout`/`serverTimeoutInMilliseconds` hodnota je dvojnásobek hodnoty `KeepAliveInterval`.</span><span class="sxs-lookup"><span data-stu-id="900c8-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="900c8-139">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="900c8-139">All installed protocols</span></span> | <span data-ttu-id="900c8-140">Protokoly podporované tímto rozbočovačem</span><span class="sxs-lookup"><span data-stu-id="900c8-140">Protocols supported by this hub.</span></span> <span data-ttu-id="900c8-141">Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly.</span><span class="sxs-lookup"><span data-stu-id="900c8-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="900c8-142">Pokud `true`, jsou klientovi vraceny podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="900c8-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="900c8-143">Výchozí hodnota je `false`, protože tyto zprávy o výjimce mohou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="900c8-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="900c8-144">Maximální počet položek, které lze uložit do vyrovnávací paměti pro datové proudy pro odeslání klienta.</span><span class="sxs-lookup"><span data-stu-id="900c8-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="900c8-145">Pokud je dosaženo tohoto limitu, zpracování volání je blokováno, dokud server nezpracovává položky datového proudu.</span><span class="sxs-lookup"><span data-stu-id="900c8-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="900c8-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="900c8-146">32 KB</span></span> | <span data-ttu-id="900c8-147">Maximální velikost jedné příchozí zprávy centra</span><span class="sxs-lookup"><span data-stu-id="900c8-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="900c8-148">Možnosti lze nakonfigurovat pro všechna centra tím, že poskytnete delegáty možností `AddSignalR` volání `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="900c8-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="900c8-149">Možnosti pro jeden rozbočovač přepíší globální možnosti poskytované v `AddSignalR` a dají se nakonfigurovat pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span><span class="sxs-lookup"><span data-stu-id="900c8-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="900c8-150">Rozšířené možnosti konfigurace protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="900c8-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="900c8-151">Pomocí `HttpConnectionDispatcherOptions` můžete nakonfigurovat upřesňující nastavení týkající se přenosů a správy vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="900c8-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="900c8-152">Tyto možnosti jsou nakonfigurovány předáním delegáta [MapHub\<t >](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="900c8-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="900c8-153">Následující tabulka obsahuje popis možností konfigurace ASP.NET Core SignalRpokročilé možnosti protokolu HTTP:</span><span class="sxs-lookup"><span data-stu-id="900c8-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="900c8-154">Možnost</span><span class="sxs-lookup"><span data-stu-id="900c8-154">Option</span></span> | <span data-ttu-id="900c8-155">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="900c8-155">Default Value</span></span> | <span data-ttu-id="900c8-156">Popis</span><span class="sxs-lookup"><span data-stu-id="900c8-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="900c8-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="900c8-157">32 KB</span></span> | <span data-ttu-id="900c8-158">Maximální počet bajtů přijatých od klienta, které jsou ukládány do vyrovnávací paměti serveru před použitím protitlaku.</span><span class="sxs-lookup"><span data-stu-id="900c8-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="900c8-159">Zvýšením této hodnoty umožníte, aby server přijímal větší zprávy rychleji bez použití zatížení, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="900c8-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="900c8-160">Data, která se automaticky shromažďují z atributů `Authorize` použitých pro třídu centra</span><span class="sxs-lookup"><span data-stu-id="900c8-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="900c8-161">Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru</span><span class="sxs-lookup"><span data-stu-id="900c8-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="900c8-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="900c8-162">32 KB</span></span> | <span data-ttu-id="900c8-163">Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru před pozorováním protitlaku.</span><span class="sxs-lookup"><span data-stu-id="900c8-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="900c8-164">Zvýšením této hodnoty umožníte serveru rychleji ukládat větší zprávy bez nutnosti očekávat zatížení, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="900c8-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="900c8-165">Všechny přenosy jsou povolené.</span><span class="sxs-lookup"><span data-stu-id="900c8-165">All Transports are enabled.</span></span> | <span data-ttu-id="900c8-166">Bitové příznaky vyčíslují `HttpTransportType` hodnoty, které mohou omezit přenos, který může klient použít pro připojení.</span><span class="sxs-lookup"><span data-stu-id="900c8-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="900c8-167">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="900c8-167">See below.</span></span> | <span data-ttu-id="900c8-168">Další možnosti specifické pro přenos dlouhého cyklického dotazování.</span><span class="sxs-lookup"><span data-stu-id="900c8-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="900c8-169">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="900c8-169">See below.</span></span> | <span data-ttu-id="900c8-170">Další možnosti specifické pro přenos pomocí protokolu WebSockets</span><span class="sxs-lookup"><span data-stu-id="900c8-170">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="900c8-171">Přenos dlouhého cyklického dotazování má další možnosti, které je možné konfigurovat pomocí vlastnosti `LongPolling`:</span><span class="sxs-lookup"><span data-stu-id="900c8-171">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="900c8-172">Možnost</span><span class="sxs-lookup"><span data-stu-id="900c8-172">Option</span></span> | <span data-ttu-id="900c8-173">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="900c8-173">Default Value</span></span> | <span data-ttu-id="900c8-174">Popis</span><span class="sxs-lookup"><span data-stu-id="900c8-174">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="900c8-175">90 sekund</span><span class="sxs-lookup"><span data-stu-id="900c8-175">90 seconds</span></span> | <span data-ttu-id="900c8-176">Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jedné žádosti o cyklické dotazování.</span><span class="sxs-lookup"><span data-stu-id="900c8-176">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="900c8-177">Snížení této hodnoty způsobí, že klient bude vydávat nové požadavky na dotaz častěji.</span><span class="sxs-lookup"><span data-stu-id="900c8-177">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="900c8-178">Přenos přes protokol WebSocket má další možnosti, které je možné konfigurovat pomocí vlastnosti `WebSockets`:</span><span class="sxs-lookup"><span data-stu-id="900c8-178">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="900c8-179">Možnost</span><span class="sxs-lookup"><span data-stu-id="900c8-179">Option</span></span> | <span data-ttu-id="900c8-180">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="900c8-180">Default Value</span></span> | <span data-ttu-id="900c8-181">Popis</span><span class="sxs-lookup"><span data-stu-id="900c8-181">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="900c8-182">5 sekund</span><span class="sxs-lookup"><span data-stu-id="900c8-182">5 seconds</span></span> | <span data-ttu-id="900c8-183">Pokud se po ukončení serveru aplikace v tomto časovém intervalu nepovede zavřít, připojení se ukončí.</span><span class="sxs-lookup"><span data-stu-id="900c8-183">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="900c8-184">Delegát, který lze použít k nastavení záhlaví `Sec-WebSocket-Protocol` na vlastní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="900c8-184">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="900c8-185">Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že se vrátí požadovaná hodnota.</span><span class="sxs-lookup"><span data-stu-id="900c8-185">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="900c8-186">Konfigurace možností klienta</span><span class="sxs-lookup"><span data-stu-id="900c8-186">Configure client options</span></span>

<span data-ttu-id="900c8-187">Možnosti klienta lze nakonfigurovat u `HubConnectionBuilder`ho typu (k dispozici v klientech rozhraní .NET a v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="900c8-187">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="900c8-188">Je také k dispozici v klientovi Java, ale `HttpHubConnectionBuilder` podtřídou je to, co obsahuje možnosti konfigurace tvůrce a také na `HubConnection` sám.</span><span class="sxs-lookup"><span data-stu-id="900c8-188">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="900c8-189">Konfigurovat protokolování</span><span class="sxs-lookup"><span data-stu-id="900c8-189">Configure logging</span></span>

<span data-ttu-id="900c8-190">Protokolování je konfigurováno v klientovi .NET pomocí metody `ConfigureLogging`.</span><span class="sxs-lookup"><span data-stu-id="900c8-190">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="900c8-191">Zprostředkovatelé protokolování a filtry je možné zaregistrovat stejným způsobem jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="900c8-191">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="900c8-192">Další informace najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="900c8-192">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="900c8-193">Aby bylo možné registrovat poskytovatele protokolování, je nutné nainstalovat potřebné balíčky.</span><span class="sxs-lookup"><span data-stu-id="900c8-193">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="900c8-194">Úplný seznam najdete v části [předdefinovaná zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="900c8-194">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="900c8-195">Chcete-li například povolit protokolování konzoly, nainstalujte balíček `Microsoft.Extensions.Logging.Console` NuGet.</span><span class="sxs-lookup"><span data-stu-id="900c8-195">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="900c8-196">Zavolejte metodu rozšíření `AddConsole`:</span><span class="sxs-lookup"><span data-stu-id="900c8-196">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="900c8-197">V klientovi jazyka JavaScript existuje podobná `configureLogging` metoda.</span><span class="sxs-lookup"><span data-stu-id="900c8-197">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="900c8-198">Zadejte `LogLevel`ovou hodnotu označující minimální úroveň zpráv protokolu, které se mají vytvořit.</span><span class="sxs-lookup"><span data-stu-id="900c8-198">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="900c8-199">Protokoly se zapisují do okna konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="900c8-199">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="900c8-200">Místo `LogLevel` hodnoty můžete zadat také hodnotu `string` reprezentující název úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="900c8-200">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="900c8-201">To je užitečné při konfiguraci SignalR protokolování v prostředích, kde nemáte přístup k konstantám `LogLevel`.</span><span class="sxs-lookup"><span data-stu-id="900c8-201">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="900c8-202">V následující tabulce jsou uvedeny dostupné úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="900c8-202">The following table lists the available log levels.</span></span> <span data-ttu-id="900c8-203">Hodnota, kterou zadáte, `configureLogging` nastaví **minimální** úroveň protokolování, která se bude protokolovat.</span><span class="sxs-lookup"><span data-stu-id="900c8-203">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="900c8-204">Zprávy zaznamenané na této úrovni **nebo úrovně uvedené za ní v tabulce**budou protokolovány.</span><span class="sxs-lookup"><span data-stu-id="900c8-204">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="900c8-205">String</span><span class="sxs-lookup"><span data-stu-id="900c8-205">String</span></span>                      | <span data-ttu-id="900c8-206">LogLevel</span><span class="sxs-lookup"><span data-stu-id="900c8-206">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="900c8-207">`info` **nebo** `information`</span><span class="sxs-lookup"><span data-stu-id="900c8-207">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="900c8-208">`warn` **nebo** `warning`</span><span class="sxs-lookup"><span data-stu-id="900c8-208">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="900c8-209">Pokud chcete protokolování zcela zakázat, zadejte `signalR.LogLevel.None` v metodě `configureLogging`.</span><span class="sxs-lookup"><span data-stu-id="900c8-209">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="900c8-210">Další informace o protokolování najdete v dokumentaci k [diagnosticeSignalR](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="900c8-210">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="900c8-211">SignalR klient Java používá k protokolování knihovnu [SLF4J](https://www.slf4j.org/) .</span><span class="sxs-lookup"><span data-stu-id="900c8-211">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="900c8-212">Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí.</span><span class="sxs-lookup"><span data-stu-id="900c8-212">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="900c8-213">Následující fragment kódu ukazuje, jak použít `java.util.logging` s klientem SignalR Java.</span><span class="sxs-lookup"><span data-stu-id="900c8-213">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="900c8-214">Pokud ve svých závislostech nenakonfigurujete protokolování, SLF4J načte výchozí protokolovací nástroj No-operation s následující zprávou upozornění:</span><span class="sxs-lookup"><span data-stu-id="900c8-214">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="900c8-215">Tuto možnost lze bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="900c8-215">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="900c8-216">Konfigurace povolených přenosů</span><span class="sxs-lookup"><span data-stu-id="900c8-216">Configure allowed transports</span></span>

<span data-ttu-id="900c8-217">Transporty používané SignalR lze konfigurovat v volání `WithUrl` (`withUrl` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="900c8-217">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="900c8-218">Bitové nebo hodnoty `HttpTransportType` lze použít k omezení klienta na používání pouze určených přenosů.</span><span class="sxs-lookup"><span data-stu-id="900c8-218">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="900c8-219">Ve výchozím nastavení jsou povolené všechny přenosy.</span><span class="sxs-lookup"><span data-stu-id="900c8-219">All transports are enabled by default.</span></span>

<span data-ttu-id="900c8-220">Pokud třeba chcete zakázat přenos událostí odeslaných serverem, ale povolit WebSockets a dlouhá připojení s dotazem:</span><span class="sxs-lookup"><span data-stu-id="900c8-220">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="900c8-221">V klientu jazyka JavaScript jsou přenosy konfigurovány nastavením pole `transport` v objektu Options, který je k dispozici pro `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="900c8-221">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="900c8-222">V této verzi je jediným dostupným přenosem klientský WebSocket v jazyce Java.</span><span class="sxs-lookup"><span data-stu-id="900c8-222">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="900c8-223">V klientovi Java se Transport vybere pomocí metody `withTransport` v `HttpHubConnectionBuilder`.</span><span class="sxs-lookup"><span data-stu-id="900c8-223">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="900c8-224">Klient Java standardně používá přenos pomocí protokolu WebSockets.</span><span class="sxs-lookup"><span data-stu-id="900c8-224">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="900c8-225">Klient nástroje SignalR Java nepodporuje ještě záložní přenos.</span><span class="sxs-lookup"><span data-stu-id="900c8-225">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="900c8-226">Konfigurace ověřování nosiče</span><span class="sxs-lookup"><span data-stu-id="900c8-226">Configure bearer authentication</span></span>

<span data-ttu-id="900c8-227">K poskytnutí ověřovacích dat spolu s požadavky SignalR použijte možnost `AccessTokenProvider` (`accessTokenFactory` v JavaScriptu) k určení funkce, která vrací požadovaný přístupový token.</span><span class="sxs-lookup"><span data-stu-id="900c8-227">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="900c8-228">V klientovi .NET se tento přístupový token předává jako token ověřování HTTP "Bearer" (pomocí `Authorization` záhlaví s typem `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="900c8-228">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="900c8-229">V klientu jazyka JavaScript se přístupový token používá jako nosný token, **s výjimkou** případů, kdy rozhraní API prohlížeče omezuje možnost použít hlavičky (konkrétně v požadavcích na události odeslané serverem a objekty WebSockets).</span><span class="sxs-lookup"><span data-stu-id="900c8-229">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="900c8-230">V těchto případech je přístupový token k dispozici jako hodnota řetězce dotazu `access_token`.</span><span class="sxs-lookup"><span data-stu-id="900c8-230">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="900c8-231">V klientu .NET lze zadat možnost `AccessTokenProvider` pomocí delegáta možností v `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="900c8-231">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="900c8-232">V klientovi JavaScriptu je přístupový token nakonfigurovaný nastavením pole `accessTokenFactory` u objektu Options v `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="900c8-232">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="900c8-233">V SignalR klienta Java můžete nakonfigurovat nosný token, který se má použít k ověřování tím, že poskytne továrnu přístupového tokenu [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="900c8-233">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="900c8-234">Použijte [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) k poskytnutí [> jednoho\<ového řetězce](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava) .</span><span class="sxs-lookup"><span data-stu-id="900c8-234">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="900c8-235">Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.</span><span class="sxs-lookup"><span data-stu-id="900c8-235">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="900c8-236">Konfigurace možností timeout a Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="900c8-236">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="900c8-237">Další možnosti konfigurace časového limitu a chování při udržování připojení jsou k dispozici na samotném objektu `HubConnection`:</span><span class="sxs-lookup"><span data-stu-id="900c8-237">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="900c8-238">.NET</span><span class="sxs-lookup"><span data-stu-id="900c8-238">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="900c8-239">Možnost</span><span class="sxs-lookup"><span data-stu-id="900c8-239">Option</span></span> | <span data-ttu-id="900c8-240">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="900c8-240">Default value</span></span> | <span data-ttu-id="900c8-241">Popis</span><span class="sxs-lookup"><span data-stu-id="900c8-241">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="900c8-242">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="900c8-242">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="900c8-243">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="900c8-243">Timeout for server activity.</span></span> <span data-ttu-id="900c8-244">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí událost `Closed` (`onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="900c8-244">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="900c8-245">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="900c8-245">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="900c8-246">Doporučená hodnota je číslo minimálně dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy k zadání času.</span><span class="sxs-lookup"><span data-stu-id="900c8-246">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="900c8-247">15 sekund</span><span class="sxs-lookup"><span data-stu-id="900c8-247">15 seconds</span></span> | <span data-ttu-id="900c8-248">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="900c8-248">Timeout for initial server handshake.</span></span> <span data-ttu-id="900c8-249">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje událost `Closed` (`onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="900c8-249">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="900c8-250">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="900c8-250">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="900c8-251">Další informace o procesu handshake najdete v tématu [specifikace protokoluSignalR hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="900c8-251">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="900c8-252">15 sekund</span><span class="sxs-lookup"><span data-stu-id="900c8-252">15 seconds</span></span> | <span data-ttu-id="900c8-253">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="900c8-253">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="900c8-254">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="900c8-254">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="900c8-255">Pokud klient neodeslal zprávu ve `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="900c8-255">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="900c8-256">V klientovi .NET jsou hodnoty časového limitu zadány jako `TimeSpan` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="900c8-256">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="900c8-257">JavaScript</span><span class="sxs-lookup"><span data-stu-id="900c8-257">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="900c8-258">Možnost</span><span class="sxs-lookup"><span data-stu-id="900c8-258">Option</span></span> | <span data-ttu-id="900c8-259">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="900c8-259">Default value</span></span> | <span data-ttu-id="900c8-260">Popis</span><span class="sxs-lookup"><span data-stu-id="900c8-260">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="900c8-261">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="900c8-261">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="900c8-262">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="900c8-262">Timeout for server activity.</span></span> <span data-ttu-id="900c8-263">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí událost `onclose`.</span><span class="sxs-lookup"><span data-stu-id="900c8-263">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="900c8-264">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="900c8-264">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="900c8-265">Doporučená hodnota je číslo minimálně dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy k zadání času.</span><span class="sxs-lookup"><span data-stu-id="900c8-265">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="900c8-266">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="900c8-266">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="900c8-267">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="900c8-267">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="900c8-268">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="900c8-268">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="900c8-269">Pokud klient neodeslal zprávu ve `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="900c8-269">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="900c8-270">Java</span><span class="sxs-lookup"><span data-stu-id="900c8-270">Java</span></span>](#tab/java)

| <span data-ttu-id="900c8-271">Možnost</span><span class="sxs-lookup"><span data-stu-id="900c8-271">Option</span></span> | <span data-ttu-id="900c8-272">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="900c8-272">Default value</span></span> | <span data-ttu-id="900c8-273">Popis</span><span class="sxs-lookup"><span data-stu-id="900c8-273">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="900c8-274">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="900c8-274">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="900c8-275">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="900c8-275">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="900c8-276">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="900c8-276">Timeout for server activity.</span></span> <span data-ttu-id="900c8-277">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí událost `onClose`.</span><span class="sxs-lookup"><span data-stu-id="900c8-277">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="900c8-278">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="900c8-278">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="900c8-279">Doporučená hodnota je číslo minimálně dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy k zadání času.</span><span class="sxs-lookup"><span data-stu-id="900c8-279">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="900c8-280">15 sekund</span><span class="sxs-lookup"><span data-stu-id="900c8-280">15 seconds</span></span> | <span data-ttu-id="900c8-281">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="900c8-281">Timeout for initial server handshake.</span></span> <span data-ttu-id="900c8-282">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje událost `onClose`.</span><span class="sxs-lookup"><span data-stu-id="900c8-282">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="900c8-283">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="900c8-283">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="900c8-284">Další informace o procesu handshake najdete v tématu [specifikace protokoluSignalR hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="900c8-284">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="900c8-285">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="900c8-285">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="900c8-286">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="900c8-286">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="900c8-287">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="900c8-287">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="900c8-288">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="900c8-288">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="900c8-289">Pokud klient neodeslal zprávu ve `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="900c8-289">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="900c8-290">Konfigurace dalších možností</span><span class="sxs-lookup"><span data-stu-id="900c8-290">Configure additional options</span></span>

<span data-ttu-id="900c8-291">Další možnosti lze nakonfigurovat v metodě `WithUrl` (`withUrl` in JavaScript) na `HubConnectionBuilder` nebo na různých konfiguračních rozhraních API `HttpHubConnectionBuilder` v klientovi Java:</span><span class="sxs-lookup"><span data-stu-id="900c8-291">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="900c8-292">.NET</span><span class="sxs-lookup"><span data-stu-id="900c8-292">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="900c8-293">Možnost .NET</span><span class="sxs-lookup"><span data-stu-id="900c8-293">.NET Option</span></span> |  <span data-ttu-id="900c8-294">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="900c8-294">Default value</span></span> | <span data-ttu-id="900c8-295">Popis</span><span class="sxs-lookup"><span data-stu-id="900c8-295">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="900c8-296">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="900c8-296">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="900c8-297">Nastavte tuto hodnotu na `true`, aby se přeskočil krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="900c8-297">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="900c8-298">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="900c8-298">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="900c8-299">Toto nastavení se nedá povolit při použití služby Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="900c8-299">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="900c8-300">Prázdné</span><span class="sxs-lookup"><span data-stu-id="900c8-300">Empty</span></span> | <span data-ttu-id="900c8-301">Kolekce certifikátů TLS pro odeslání na požadavky na ověření.</span><span class="sxs-lookup"><span data-stu-id="900c8-301">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="900c8-302">Prázdné</span><span class="sxs-lookup"><span data-stu-id="900c8-302">Empty</span></span> | <span data-ttu-id="900c8-303">Kolekce souborů cookie protokolu HTTP, které se mají odeslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="900c8-303">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="900c8-304">Prázdné</span><span class="sxs-lookup"><span data-stu-id="900c8-304">Empty</span></span> | <span data-ttu-id="900c8-305">Přihlašovací údaje, které se mají poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="900c8-305">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="900c8-306">5 sekund</span><span class="sxs-lookup"><span data-stu-id="900c8-306">5 seconds</span></span> | <span data-ttu-id="900c8-307">Jenom objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="900c8-307">WebSockets only.</span></span> <span data-ttu-id="900c8-308">Maximální doba, po jejímž uplynutí bude klient čekat na ukončení žádosti o uzavření.</span><span class="sxs-lookup"><span data-stu-id="900c8-308">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="900c8-309">Pokud server nepotvrdí zavření v této době, klient se odpojí.</span><span class="sxs-lookup"><span data-stu-id="900c8-309">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="900c8-310">Prázdné</span><span class="sxs-lookup"><span data-stu-id="900c8-310">Empty</span></span> | <span data-ttu-id="900c8-311">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="900c8-311">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="900c8-312">Delegát, který lze použít ke konfiguraci nebo nahrazení `HttpMessageHandler` používaných k odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="900c8-312">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="900c8-313">Nepoužívá se pro připojení pomocí protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="900c8-313">Not used for WebSocket connections.</span></span> <span data-ttu-id="900c8-314">Tento delegát musí vracet hodnotu, která není null, a obdrží výchozí hodnotu jako parametr.</span><span class="sxs-lookup"><span data-stu-id="900c8-314">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="900c8-315">Buď změňte nastavení pro tuto výchozí hodnotu a vraťte je, nebo vraťte novou instanci `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="900c8-315">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="900c8-316">**Při nahrazování obslužné rutiny Nezapomeňte zkopírovat nastavení, která chcete zachovat, od poskytnuté obslužné rutiny, jinak konfigurované možnosti (například soubory cookie a záhlaví) nebudou použity pro novou obslužnou rutinu.**</span><span class="sxs-lookup"><span data-stu-id="900c8-316">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="900c8-317">Proxy server HTTP, který se má použít při odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="900c8-317">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="900c8-318">Nastavte tuto logickou hodnotu pro odeslání výchozích přihlašovacích údajů pro požadavky HTTP a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="900c8-318">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="900c8-319">To umožňuje použití ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="900c8-319">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="900c8-320">Delegát, který lze použít ke konfiguraci dalších možností protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="900c8-320">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="900c8-321">Přijímá instanci [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , která se dá použít ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="900c8-321">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="900c8-322">JavaScript</span><span class="sxs-lookup"><span data-stu-id="900c8-322">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="900c8-323">Možnost JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="900c8-323">JavaScript Option</span></span> | <span data-ttu-id="900c8-324">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="900c8-324">Default Value</span></span> | <span data-ttu-id="900c8-325">Popis</span><span class="sxs-lookup"><span data-stu-id="900c8-325">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="900c8-326">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="900c8-326">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="900c8-327">Nastavte tuto hodnotu na `true`, aby se přeskočil krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="900c8-327">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="900c8-328">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="900c8-328">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="900c8-329">Toto nastavení se nedá povolit při použití služby Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="900c8-329">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="900c8-330">Java</span><span class="sxs-lookup"><span data-stu-id="900c8-330">Java</span></span>](#tab/java)

| <span data-ttu-id="900c8-331">Možnost jazyka Java</span><span class="sxs-lookup"><span data-stu-id="900c8-331">Java Option</span></span> | <span data-ttu-id="900c8-332">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="900c8-332">Default Value</span></span> | <span data-ttu-id="900c8-333">Popis</span><span class="sxs-lookup"><span data-stu-id="900c8-333">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="900c8-334">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="900c8-334">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="900c8-335">Nastavte tuto hodnotu na `true`, aby se přeskočil krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="900c8-335">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="900c8-336">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="900c8-336">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="900c8-337">Toto nastavení se nedá povolit při použití služby Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="900c8-337">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="900c8-338">`withHeader``withHeaders`</span><span class="sxs-lookup"><span data-stu-id="900c8-338">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="900c8-339">Prázdné</span><span class="sxs-lookup"><span data-stu-id="900c8-339">Empty</span></span> | <span data-ttu-id="900c8-340">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="900c8-340">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="900c8-341">V klientu .NET můžete tyto možnosti upravit pomocí delegáta možností, který je k dispozici pro `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="900c8-341">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="900c8-342">V klientovi JavaScriptu lze tyto možnosti poskytnout v objektu jazyka JavaScript, který je k dispozici pro `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="900c8-342">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="900c8-343">V klientovi Java je možné nakonfigurovat tyto možnosti pomocí metod `HttpHubConnectionBuilder` vrácených z `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="900c8-343">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="900c8-344">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="900c8-344">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="900c8-345">Možnosti serializace JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="900c8-345">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="900c8-346">ASP.NET Core SignalR podporuje pro kódování zprávy dva protokoly: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="900c8-346">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="900c8-347">Každý protokol má možnosti konfigurace serializace.</span><span class="sxs-lookup"><span data-stu-id="900c8-347">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="900c8-348">Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , kterou lze přidat po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) do metody `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="900c8-348">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="900c8-349">Metoda `AddJsonProtocol` přebírá delegáta, který přijímá objekt `options`.</span><span class="sxs-lookup"><span data-stu-id="900c8-349">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="900c8-350">Vlastnost [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) tohoto objektu je objekt JSON.NET `JsonSerializerSettings`, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="900c8-350">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="900c8-351">Další informace najdete v [dokumentaci k JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="900c8-351">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="900c8-352">Chcete-li například nakonfigurovat serializátor pro použití názvů vlastností "PascalCase" namísto výchozích názvů "camelCase", použijte následující kód v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="900c8-352">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="900c8-353">V klientovi .NET existuje stejná `AddJsonProtocol` metoda rozšíření v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="900c8-353">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="900c8-354">Aby bylo možné přeložit metodu rozšíření, je třeba importovat obor názvů `Microsoft.Extensions.DependencyInjection`:</span><span class="sxs-lookup"><span data-stu-id="900c8-354">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="900c8-355">V tuto chvíli není možné konfigurovat serializaci JSON v klientovi jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="900c8-355">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="900c8-356">Možnosti serializace MessagePack</span><span class="sxs-lookup"><span data-stu-id="900c8-356">MessagePack serialization options</span></span>

<span data-ttu-id="900c8-357">Serializaci MessagePack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="900c8-357">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="900c8-358">Další podrobnosti najdete [v tématu MessagePack v SignalR](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="900c8-358">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="900c8-359">V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi v jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="900c8-359">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="900c8-360">Konfigurovat možnosti serveru</span><span class="sxs-lookup"><span data-stu-id="900c8-360">Configure server options</span></span>

<span data-ttu-id="900c8-361">Následující tabulka popisuje možnosti konfigurace SignalRch Center:</span><span class="sxs-lookup"><span data-stu-id="900c8-361">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="900c8-362">Možnost</span><span class="sxs-lookup"><span data-stu-id="900c8-362">Option</span></span> | <span data-ttu-id="900c8-363">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="900c8-363">Default Value</span></span> | <span data-ttu-id="900c8-364">Popis</span><span class="sxs-lookup"><span data-stu-id="900c8-364">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="900c8-365">30 sekund</span><span class="sxs-lookup"><span data-stu-id="900c8-365">30 seconds</span></span> | <span data-ttu-id="900c8-366">Server bude předpokládat, že je klient odpojený, pokud v tomto intervalu neobdržel zprávu (včetně Keep-Alive).</span><span class="sxs-lookup"><span data-stu-id="900c8-366">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="900c8-367">Může trvat delší dobu, než je tento časový limit, aby bylo možné klienta ve skutečnosti označit jako odpojený, protože to je implementováno.</span><span class="sxs-lookup"><span data-stu-id="900c8-367">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="900c8-368">Doporučená hodnota je dvojnásobná hodnota `KeepAliveInterval`.</span><span class="sxs-lookup"><span data-stu-id="900c8-368">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="900c8-369">15 sekund</span><span class="sxs-lookup"><span data-stu-id="900c8-369">15 seconds</span></span> | <span data-ttu-id="900c8-370">Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře.</span><span class="sxs-lookup"><span data-stu-id="900c8-370">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="900c8-371">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="900c8-371">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="900c8-372">Další informace o procesu handshake najdete v tématu [specifikace protokoluSignalR hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="900c8-372">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="900c8-373">15 sekund</span><span class="sxs-lookup"><span data-stu-id="900c8-373">15 seconds</span></span> | <span data-ttu-id="900c8-374">Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="900c8-374">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="900c8-375">Při změně `KeepAliveInterval`změňte nastavení `ServerTimeout`/`serverTimeoutInMilliseconds` na klientovi.</span><span class="sxs-lookup"><span data-stu-id="900c8-375">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="900c8-376">Doporučená `ServerTimeout`/`serverTimeoutInMilliseconds` hodnota je dvojnásobek hodnoty `KeepAliveInterval`.</span><span class="sxs-lookup"><span data-stu-id="900c8-376">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="900c8-377">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="900c8-377">All installed protocols</span></span> | <span data-ttu-id="900c8-378">Protokoly podporované tímto rozbočovačem</span><span class="sxs-lookup"><span data-stu-id="900c8-378">Protocols supported by this hub.</span></span> <span data-ttu-id="900c8-379">Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly.</span><span class="sxs-lookup"><span data-stu-id="900c8-379">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="900c8-380">Pokud `true`, jsou klientovi vraceny podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="900c8-380">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="900c8-381">Výchozí hodnota je `false`, protože tyto zprávy o výjimce mohou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="900c8-381">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="900c8-382">Možnosti lze nakonfigurovat pro všechna centra tím, že poskytnete delegáty možností `AddSignalR` volání `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="900c8-382">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="900c8-383">Možnosti pro jeden rozbočovač přepíší globální možnosti poskytované v `AddSignalR` a dají se nakonfigurovat pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span><span class="sxs-lookup"><span data-stu-id="900c8-383">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="900c8-384">Rozšířené možnosti konfigurace protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="900c8-384">Advanced HTTP configuration options</span></span>

<span data-ttu-id="900c8-385">Pomocí `HttpConnectionDispatcherOptions` můžete nakonfigurovat upřesňující nastavení týkající se přenosů a správy vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="900c8-385">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="900c8-386">Tyto možnosti jsou nakonfigurovány předáním delegáta [MapHub\<t >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="900c8-386">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="900c8-387">Následující tabulka obsahuje popis možností konfigurace ASP.NET Core SignalRpokročilé možnosti protokolu HTTP:</span><span class="sxs-lookup"><span data-stu-id="900c8-387">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="900c8-388">Možnost</span><span class="sxs-lookup"><span data-stu-id="900c8-388">Option</span></span> | <span data-ttu-id="900c8-389">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="900c8-389">Default Value</span></span> | <span data-ttu-id="900c8-390">Popis</span><span class="sxs-lookup"><span data-stu-id="900c8-390">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="900c8-391">32 KB</span><span class="sxs-lookup"><span data-stu-id="900c8-391">32 KB</span></span> | <span data-ttu-id="900c8-392">Maximální počet bajtů přijatých od klienta, které jsou vyrovnávací paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="900c8-392">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="900c8-393">Zvýšením této hodnoty umožníte serveru přijímat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="900c8-393">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="900c8-394">Data, která se automaticky shromažďují z atributů `Authorize` použitých pro třídu centra</span><span class="sxs-lookup"><span data-stu-id="900c8-394">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="900c8-395">Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru</span><span class="sxs-lookup"><span data-stu-id="900c8-395">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="900c8-396">32 KB</span><span class="sxs-lookup"><span data-stu-id="900c8-396">32 KB</span></span> | <span data-ttu-id="900c8-397">Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="900c8-397">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="900c8-398">Zvýšením této hodnoty umožníte serveru odesílat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="900c8-398">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="900c8-399">Všechny přenosy jsou povolené.</span><span class="sxs-lookup"><span data-stu-id="900c8-399">All Transports are enabled.</span></span> | <span data-ttu-id="900c8-400">Bitové příznaky vyčíslují `HttpTransportType` hodnoty, které mohou omezit přenos, který může klient použít pro připojení.</span><span class="sxs-lookup"><span data-stu-id="900c8-400">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="900c8-401">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="900c8-401">See below.</span></span> | <span data-ttu-id="900c8-402">Další možnosti specifické pro přenos dlouhého cyklického dotazování.</span><span class="sxs-lookup"><span data-stu-id="900c8-402">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="900c8-403">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="900c8-403">See below.</span></span> | <span data-ttu-id="900c8-404">Další možnosti specifické pro přenos pomocí protokolu WebSockets</span><span class="sxs-lookup"><span data-stu-id="900c8-404">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="900c8-405">Přenos dlouhého cyklického dotazování má další možnosti, které je možné konfigurovat pomocí vlastnosti `LongPolling`:</span><span class="sxs-lookup"><span data-stu-id="900c8-405">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="900c8-406">Možnost</span><span class="sxs-lookup"><span data-stu-id="900c8-406">Option</span></span> | <span data-ttu-id="900c8-407">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="900c8-407">Default Value</span></span> | <span data-ttu-id="900c8-408">Popis</span><span class="sxs-lookup"><span data-stu-id="900c8-408">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="900c8-409">90 sekund</span><span class="sxs-lookup"><span data-stu-id="900c8-409">90 seconds</span></span> | <span data-ttu-id="900c8-410">Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jedné žádosti o cyklické dotazování.</span><span class="sxs-lookup"><span data-stu-id="900c8-410">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="900c8-411">Snížení této hodnoty způsobí, že klient bude vydávat nové požadavky na dotaz častěji.</span><span class="sxs-lookup"><span data-stu-id="900c8-411">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="900c8-412">Přenos přes protokol WebSocket má další možnosti, které je možné konfigurovat pomocí vlastnosti `WebSockets`:</span><span class="sxs-lookup"><span data-stu-id="900c8-412">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="900c8-413">Možnost</span><span class="sxs-lookup"><span data-stu-id="900c8-413">Option</span></span> | <span data-ttu-id="900c8-414">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="900c8-414">Default Value</span></span> | <span data-ttu-id="900c8-415">Popis</span><span class="sxs-lookup"><span data-stu-id="900c8-415">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="900c8-416">5 sekund</span><span class="sxs-lookup"><span data-stu-id="900c8-416">5 seconds</span></span> | <span data-ttu-id="900c8-417">Pokud se po ukončení serveru aplikace v tomto časovém intervalu nepovede zavřít, připojení se ukončí.</span><span class="sxs-lookup"><span data-stu-id="900c8-417">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="900c8-418">Delegát, který lze použít k nastavení záhlaví `Sec-WebSocket-Protocol` na vlastní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="900c8-418">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="900c8-419">Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že se vrátí požadovaná hodnota.</span><span class="sxs-lookup"><span data-stu-id="900c8-419">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="900c8-420">Konfigurace možností klienta</span><span class="sxs-lookup"><span data-stu-id="900c8-420">Configure client options</span></span>

<span data-ttu-id="900c8-421">Možnosti klienta lze nakonfigurovat u `HubConnectionBuilder`ho typu (k dispozici v klientech rozhraní .NET a v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="900c8-421">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="900c8-422">Je také k dispozici v klientovi Java, ale `HttpHubConnectionBuilder` podtřídou je to, co obsahuje možnosti konfigurace tvůrce a také na `HubConnection` sám.</span><span class="sxs-lookup"><span data-stu-id="900c8-422">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="900c8-423">Konfigurovat protokolování</span><span class="sxs-lookup"><span data-stu-id="900c8-423">Configure logging</span></span>

<span data-ttu-id="900c8-424">Protokolování je konfigurováno v klientovi .NET pomocí metody `ConfigureLogging`.</span><span class="sxs-lookup"><span data-stu-id="900c8-424">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="900c8-425">Zprostředkovatelé protokolování a filtry je možné zaregistrovat stejným způsobem jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="900c8-425">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="900c8-426">Další informace najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="900c8-426">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="900c8-427">Aby bylo možné registrovat poskytovatele protokolování, je nutné nainstalovat potřebné balíčky.</span><span class="sxs-lookup"><span data-stu-id="900c8-427">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="900c8-428">Úplný seznam najdete v části [předdefinovaná zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="900c8-428">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="900c8-429">Chcete-li například povolit protokolování konzoly, nainstalujte balíček `Microsoft.Extensions.Logging.Console` NuGet.</span><span class="sxs-lookup"><span data-stu-id="900c8-429">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="900c8-430">Zavolejte metodu rozšíření `AddConsole`:</span><span class="sxs-lookup"><span data-stu-id="900c8-430">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="900c8-431">V klientovi jazyka JavaScript existuje podobná `configureLogging` metoda.</span><span class="sxs-lookup"><span data-stu-id="900c8-431">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="900c8-432">Zadejte `LogLevel`ovou hodnotu označující minimální úroveň zpráv protokolu, které se mají vytvořit.</span><span class="sxs-lookup"><span data-stu-id="900c8-432">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="900c8-433">Protokoly se zapisují do okna konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="900c8-433">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="900c8-434">Pokud chcete protokolování zcela zakázat, zadejte `signalR.LogLevel.None` v metodě `configureLogging`.</span><span class="sxs-lookup"><span data-stu-id="900c8-434">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="900c8-435">Další informace o protokolování najdete v dokumentaci k [diagnosticeSignalR](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="900c8-435">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="900c8-436">SignalR klient Java používá k protokolování knihovnu [SLF4J](https://www.slf4j.org/) .</span><span class="sxs-lookup"><span data-stu-id="900c8-436">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="900c8-437">Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí.</span><span class="sxs-lookup"><span data-stu-id="900c8-437">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="900c8-438">Následující fragment kódu ukazuje, jak použít `java.util.logging` s klientem SignalR Java.</span><span class="sxs-lookup"><span data-stu-id="900c8-438">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="900c8-439">Pokud ve svých závislostech nenakonfigurujete protokolování, SLF4J načte výchozí protokolovací nástroj No-operation s následující zprávou upozornění:</span><span class="sxs-lookup"><span data-stu-id="900c8-439">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="900c8-440">Tuto možnost lze bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="900c8-440">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="900c8-441">Konfigurace povolených přenosů</span><span class="sxs-lookup"><span data-stu-id="900c8-441">Configure allowed transports</span></span>

<span data-ttu-id="900c8-442">Transporty používané SignalR lze konfigurovat v volání `WithUrl` (`withUrl` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="900c8-442">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="900c8-443">Bitové nebo hodnoty `HttpTransportType` lze použít k omezení klienta na používání pouze určených přenosů.</span><span class="sxs-lookup"><span data-stu-id="900c8-443">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="900c8-444">Ve výchozím nastavení jsou povolené všechny přenosy.</span><span class="sxs-lookup"><span data-stu-id="900c8-444">All transports are enabled by default.</span></span>

<span data-ttu-id="900c8-445">Pokud třeba chcete zakázat přenos událostí odeslaných serverem, ale povolit WebSockets a dlouhá připojení s dotazem:</span><span class="sxs-lookup"><span data-stu-id="900c8-445">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="900c8-446">V klientu jazyka JavaScript jsou přenosy konfigurovány nastavením pole `transport` v objektu Options, který je k dispozici pro `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="900c8-446">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="900c8-447">V této verzi je jediným dostupným přenosem klientský WebSocket v jazyce Java.</span><span class="sxs-lookup"><span data-stu-id="900c8-447">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="900c8-448">Konfigurace ověřování nosiče</span><span class="sxs-lookup"><span data-stu-id="900c8-448">Configure bearer authentication</span></span>

<span data-ttu-id="900c8-449">K poskytnutí ověřovacích dat spolu s požadavky SignalR použijte možnost `AccessTokenProvider` (`accessTokenFactory` v JavaScriptu) k určení funkce, která vrací požadovaný přístupový token.</span><span class="sxs-lookup"><span data-stu-id="900c8-449">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="900c8-450">V klientovi .NET se tento přístupový token předává jako token ověřování HTTP "Bearer" (pomocí `Authorization` záhlaví s typem `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="900c8-450">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="900c8-451">V klientu jazyka JavaScript se přístupový token používá jako nosný token, **s výjimkou** případů, kdy rozhraní API prohlížeče omezuje možnost použít hlavičky (konkrétně v požadavcích na události odeslané serverem a objekty WebSockets).</span><span class="sxs-lookup"><span data-stu-id="900c8-451">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="900c8-452">V těchto případech je přístupový token k dispozici jako hodnota řetězce dotazu `access_token`.</span><span class="sxs-lookup"><span data-stu-id="900c8-452">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="900c8-453">V klientu .NET lze zadat možnost `AccessTokenProvider` pomocí delegáta možností v `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="900c8-453">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="900c8-454">V klientovi JavaScriptu je přístupový token nakonfigurovaný nastavením pole `accessTokenFactory` u objektu Options v `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="900c8-454">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="900c8-455">V SignalR klienta Java můžete nakonfigurovat nosný token, který se má použít k ověřování tím, že poskytne továrnu přístupového tokenu [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="900c8-455">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="900c8-456">Použijte [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) k poskytnutí [> jednoho\<ového řetězce](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava) .</span><span class="sxs-lookup"><span data-stu-id="900c8-456">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="900c8-457">Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.</span><span class="sxs-lookup"><span data-stu-id="900c8-457">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="900c8-458">Konfigurace možností timeout a Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="900c8-458">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="900c8-459">Další možnosti konfigurace časového limitu a chování při udržování připojení jsou k dispozici na samotném objektu `HubConnection`:</span><span class="sxs-lookup"><span data-stu-id="900c8-459">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="900c8-460">.NET</span><span class="sxs-lookup"><span data-stu-id="900c8-460">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="900c8-461">Možnost</span><span class="sxs-lookup"><span data-stu-id="900c8-461">Option</span></span> | <span data-ttu-id="900c8-462">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="900c8-462">Default value</span></span> | <span data-ttu-id="900c8-463">Popis</span><span class="sxs-lookup"><span data-stu-id="900c8-463">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="900c8-464">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="900c8-464">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="900c8-465">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="900c8-465">Timeout for server activity.</span></span> <span data-ttu-id="900c8-466">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí událost `Closed` (`onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="900c8-466">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="900c8-467">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="900c8-467">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="900c8-468">Doporučená hodnota je číslo minimálně dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy k zadání času.</span><span class="sxs-lookup"><span data-stu-id="900c8-468">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="900c8-469">15 sekund</span><span class="sxs-lookup"><span data-stu-id="900c8-469">15 seconds</span></span> | <span data-ttu-id="900c8-470">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="900c8-470">Timeout for initial server handshake.</span></span> <span data-ttu-id="900c8-471">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje událost `Closed` (`onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="900c8-471">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="900c8-472">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="900c8-472">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="900c8-473">Další informace o procesu handshake najdete v tématu [specifikace protokoluSignalR hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="900c8-473">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="900c8-474">15 sekund</span><span class="sxs-lookup"><span data-stu-id="900c8-474">15 seconds</span></span> | <span data-ttu-id="900c8-475">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="900c8-475">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="900c8-476">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="900c8-476">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="900c8-477">Pokud klient neodeslal zprávu ve `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="900c8-477">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="900c8-478">V klientovi .NET jsou hodnoty časového limitu zadány jako `TimeSpan` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="900c8-478">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="900c8-479">JavaScript</span><span class="sxs-lookup"><span data-stu-id="900c8-479">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="900c8-480">Možnost</span><span class="sxs-lookup"><span data-stu-id="900c8-480">Option</span></span> | <span data-ttu-id="900c8-481">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="900c8-481">Default value</span></span> | <span data-ttu-id="900c8-482">Popis</span><span class="sxs-lookup"><span data-stu-id="900c8-482">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="900c8-483">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="900c8-483">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="900c8-484">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="900c8-484">Timeout for server activity.</span></span> <span data-ttu-id="900c8-485">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí událost `onclose`.</span><span class="sxs-lookup"><span data-stu-id="900c8-485">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="900c8-486">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="900c8-486">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="900c8-487">Doporučená hodnota je číslo minimálně dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy k zadání času.</span><span class="sxs-lookup"><span data-stu-id="900c8-487">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="900c8-488">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="900c8-488">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="900c8-489">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="900c8-489">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="900c8-490">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="900c8-490">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="900c8-491">Pokud klient neodeslal zprávu ve `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="900c8-491">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="900c8-492">Java</span><span class="sxs-lookup"><span data-stu-id="900c8-492">Java</span></span>](#tab/java)

| <span data-ttu-id="900c8-493">Možnost</span><span class="sxs-lookup"><span data-stu-id="900c8-493">Option</span></span> | <span data-ttu-id="900c8-494">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="900c8-494">Default value</span></span> | <span data-ttu-id="900c8-495">Popis</span><span class="sxs-lookup"><span data-stu-id="900c8-495">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="900c8-496">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="900c8-496">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="900c8-497">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="900c8-497">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="900c8-498">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="900c8-498">Timeout for server activity.</span></span> <span data-ttu-id="900c8-499">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí událost `onClose`.</span><span class="sxs-lookup"><span data-stu-id="900c8-499">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="900c8-500">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="900c8-500">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="900c8-501">Doporučená hodnota je číslo minimálně dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy k zadání času.</span><span class="sxs-lookup"><span data-stu-id="900c8-501">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="900c8-502">15 sekund</span><span class="sxs-lookup"><span data-stu-id="900c8-502">15 seconds</span></span> | <span data-ttu-id="900c8-503">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="900c8-503">Timeout for initial server handshake.</span></span> <span data-ttu-id="900c8-504">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje událost `onClose`.</span><span class="sxs-lookup"><span data-stu-id="900c8-504">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="900c8-505">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="900c8-505">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="900c8-506">Další informace o procesu handshake najdete v tématu [specifikace protokoluSignalR hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="900c8-506">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="900c8-507">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="900c8-507">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="900c8-508">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="900c8-508">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="900c8-509">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="900c8-509">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="900c8-510">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="900c8-510">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="900c8-511">Pokud klient neodeslal zprávu ve `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="900c8-511">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="900c8-512">Konfigurace dalších možností</span><span class="sxs-lookup"><span data-stu-id="900c8-512">Configure additional options</span></span>

<span data-ttu-id="900c8-513">Další možnosti lze nakonfigurovat v metodě `WithUrl` (`withUrl` in JavaScript) na `HubConnectionBuilder` nebo na různých konfiguračních rozhraních API `HttpHubConnectionBuilder` v klientovi Java:</span><span class="sxs-lookup"><span data-stu-id="900c8-513">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="900c8-514">.NET</span><span class="sxs-lookup"><span data-stu-id="900c8-514">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="900c8-515">Možnost .NET</span><span class="sxs-lookup"><span data-stu-id="900c8-515">.NET Option</span></span> |  <span data-ttu-id="900c8-516">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="900c8-516">Default value</span></span> | <span data-ttu-id="900c8-517">Popis</span><span class="sxs-lookup"><span data-stu-id="900c8-517">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="900c8-518">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="900c8-518">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="900c8-519">Nastavte tuto hodnotu na `true`, aby se přeskočil krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="900c8-519">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="900c8-520">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="900c8-520">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="900c8-521">Toto nastavení se nedá povolit při použití služby Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="900c8-521">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="900c8-522">Prázdné</span><span class="sxs-lookup"><span data-stu-id="900c8-522">Empty</span></span> | <span data-ttu-id="900c8-523">Kolekce certifikátů TLS pro odeslání na požadavky na ověření.</span><span class="sxs-lookup"><span data-stu-id="900c8-523">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="900c8-524">Prázdné</span><span class="sxs-lookup"><span data-stu-id="900c8-524">Empty</span></span> | <span data-ttu-id="900c8-525">Kolekce souborů cookie protokolu HTTP, které se mají odeslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="900c8-525">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="900c8-526">Prázdné</span><span class="sxs-lookup"><span data-stu-id="900c8-526">Empty</span></span> | <span data-ttu-id="900c8-527">Přihlašovací údaje, které se mají poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="900c8-527">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="900c8-528">5 sekund</span><span class="sxs-lookup"><span data-stu-id="900c8-528">5 seconds</span></span> | <span data-ttu-id="900c8-529">Jenom objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="900c8-529">WebSockets only.</span></span> <span data-ttu-id="900c8-530">Maximální doba, po jejímž uplynutí bude klient čekat na ukončení žádosti o uzavření.</span><span class="sxs-lookup"><span data-stu-id="900c8-530">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="900c8-531">Pokud server nepotvrdí zavření v této době, klient se odpojí.</span><span class="sxs-lookup"><span data-stu-id="900c8-531">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="900c8-532">Prázdné</span><span class="sxs-lookup"><span data-stu-id="900c8-532">Empty</span></span> | <span data-ttu-id="900c8-533">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="900c8-533">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="900c8-534">Delegát, který lze použít ke konfiguraci nebo nahrazení `HttpMessageHandler` používaných k odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="900c8-534">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="900c8-535">Nepoužívá se pro připojení pomocí protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="900c8-535">Not used for WebSocket connections.</span></span> <span data-ttu-id="900c8-536">Tento delegát musí vracet hodnotu, která není null, a obdrží výchozí hodnotu jako parametr.</span><span class="sxs-lookup"><span data-stu-id="900c8-536">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="900c8-537">Buď změňte nastavení pro tuto výchozí hodnotu a vraťte je, nebo vraťte novou instanci `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="900c8-537">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="900c8-538">**Při nahrazování obslužné rutiny Nezapomeňte zkopírovat nastavení, která chcete zachovat, od poskytnuté obslužné rutiny, jinak konfigurované možnosti (například soubory cookie a záhlaví) nebudou použity pro novou obslužnou rutinu.**</span><span class="sxs-lookup"><span data-stu-id="900c8-538">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="900c8-539">Proxy server HTTP, který se má použít při odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="900c8-539">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="900c8-540">Nastavte tuto logickou hodnotu pro odeslání výchozích přihlašovacích údajů pro požadavky HTTP a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="900c8-540">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="900c8-541">To umožňuje použití ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="900c8-541">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="900c8-542">Delegát, který lze použít ke konfiguraci dalších možností protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="900c8-542">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="900c8-543">Přijímá instanci [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , která se dá použít ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="900c8-543">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="900c8-544">JavaScript</span><span class="sxs-lookup"><span data-stu-id="900c8-544">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="900c8-545">Možnost JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="900c8-545">JavaScript Option</span></span> | <span data-ttu-id="900c8-546">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="900c8-546">Default Value</span></span> | <span data-ttu-id="900c8-547">Popis</span><span class="sxs-lookup"><span data-stu-id="900c8-547">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="900c8-548">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="900c8-548">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="900c8-549">Nastavte tuto hodnotu na `true`, aby se přeskočil krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="900c8-549">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="900c8-550">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="900c8-550">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="900c8-551">Toto nastavení se nedá povolit při použití služby Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="900c8-551">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="900c8-552">Java</span><span class="sxs-lookup"><span data-stu-id="900c8-552">Java</span></span>](#tab/java)

| <span data-ttu-id="900c8-553">Možnost jazyka Java</span><span class="sxs-lookup"><span data-stu-id="900c8-553">Java Option</span></span> | <span data-ttu-id="900c8-554">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="900c8-554">Default Value</span></span> | <span data-ttu-id="900c8-555">Popis</span><span class="sxs-lookup"><span data-stu-id="900c8-555">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="900c8-556">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="900c8-556">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="900c8-557">Nastavte tuto hodnotu na `true`, aby se přeskočil krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="900c8-557">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="900c8-558">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="900c8-558">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="900c8-559">Toto nastavení se nedá povolit při použití služby Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="900c8-559">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="900c8-560">`withHeader``withHeaders`</span><span class="sxs-lookup"><span data-stu-id="900c8-560">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="900c8-561">Prázdné</span><span class="sxs-lookup"><span data-stu-id="900c8-561">Empty</span></span> | <span data-ttu-id="900c8-562">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="900c8-562">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="900c8-563">V klientu .NET můžete tyto možnosti upravit pomocí delegáta možností, který je k dispozici pro `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="900c8-563">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="900c8-564">V klientovi JavaScriptu lze tyto možnosti poskytnout v objektu jazyka JavaScript, který je k dispozici pro `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="900c8-564">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="900c8-565">V klientovi Java je možné nakonfigurovat tyto možnosti pomocí metod `HttpHubConnectionBuilder` vrácených z `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="900c8-565">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="900c8-566">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="900c8-566">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="900c8-567">Možnosti serializace JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="900c8-567">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="900c8-568">ASP.NET Core SignalR podporuje pro kódování zprávy dva protokoly: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="900c8-568">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="900c8-569">Každý protokol má možnosti konfigurace serializace.</span><span class="sxs-lookup"><span data-stu-id="900c8-569">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="900c8-570">Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , kterou lze přidat po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) do metody `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="900c8-570">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="900c8-571">Metoda `AddJsonProtocol` přebírá delegáta, který přijímá objekt `options`.</span><span class="sxs-lookup"><span data-stu-id="900c8-571">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="900c8-572">Vlastnost [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) tohoto objektu je objekt JSON.NET `JsonSerializerSettings`, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="900c8-572">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="900c8-573">Další informace najdete v [dokumentaci k JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="900c8-573">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="900c8-574">Chcete-li například nakonfigurovat serializátor pro použití názvů vlastností "PascalCase" namísto výchozích názvů "camelCase", použijte následující kód v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="900c8-574">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="900c8-575">V klientovi .NET existuje stejná `AddJsonProtocol` metoda rozšíření v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="900c8-575">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="900c8-576">Aby bylo možné přeložit metodu rozšíření, je třeba importovat obor názvů `Microsoft.Extensions.DependencyInjection`:</span><span class="sxs-lookup"><span data-stu-id="900c8-576">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="900c8-577">V tuto chvíli není možné konfigurovat serializaci JSON v klientovi jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="900c8-577">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="900c8-578">Možnosti serializace MessagePack</span><span class="sxs-lookup"><span data-stu-id="900c8-578">MessagePack serialization options</span></span>

<span data-ttu-id="900c8-579">Serializaci MessagePack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="900c8-579">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="900c8-580">Další podrobnosti najdete [v tématu MessagePack v SignalR](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="900c8-580">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="900c8-581">V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi v jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="900c8-581">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="900c8-582">Konfigurovat možnosti serveru</span><span class="sxs-lookup"><span data-stu-id="900c8-582">Configure server options</span></span>

<span data-ttu-id="900c8-583">Následující tabulka popisuje možnosti konfigurace SignalRch Center:</span><span class="sxs-lookup"><span data-stu-id="900c8-583">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="900c8-584">Možnost</span><span class="sxs-lookup"><span data-stu-id="900c8-584">Option</span></span> | <span data-ttu-id="900c8-585">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="900c8-585">Default Value</span></span> | <span data-ttu-id="900c8-586">Popis</span><span class="sxs-lookup"><span data-stu-id="900c8-586">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="900c8-587">15 sekund</span><span class="sxs-lookup"><span data-stu-id="900c8-587">15 seconds</span></span> | <span data-ttu-id="900c8-588">Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře.</span><span class="sxs-lookup"><span data-stu-id="900c8-588">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="900c8-589">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="900c8-589">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="900c8-590">Další informace o procesu handshake najdete v tématu [specifikace protokoluSignalR hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="900c8-590">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="900c8-591">15 sekund</span><span class="sxs-lookup"><span data-stu-id="900c8-591">15 seconds</span></span> | <span data-ttu-id="900c8-592">Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="900c8-592">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="900c8-593">Při změně `KeepAliveInterval`změňte nastavení `ServerTimeout`/`serverTimeoutInMilliseconds` na klientovi.</span><span class="sxs-lookup"><span data-stu-id="900c8-593">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="900c8-594">Doporučená `ServerTimeout`/`serverTimeoutInMilliseconds` hodnota je dvojnásobek hodnoty `KeepAliveInterval`.</span><span class="sxs-lookup"><span data-stu-id="900c8-594">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="900c8-595">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="900c8-595">All installed protocols</span></span> | <span data-ttu-id="900c8-596">Protokoly podporované tímto rozbočovačem</span><span class="sxs-lookup"><span data-stu-id="900c8-596">Protocols supported by this hub.</span></span> <span data-ttu-id="900c8-597">Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly.</span><span class="sxs-lookup"><span data-stu-id="900c8-597">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="900c8-598">Pokud `true`, jsou klientovi vraceny podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="900c8-598">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="900c8-599">Výchozí hodnota je `false`, protože tyto zprávy o výjimce mohou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="900c8-599">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="900c8-600">Možnosti lze nakonfigurovat pro všechna centra tím, že poskytnete delegáty možností `AddSignalR` volání `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="900c8-600">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="900c8-601">Možnosti pro jeden rozbočovač přepíší globální možnosti poskytované v `AddSignalR` a dají se nakonfigurovat pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span><span class="sxs-lookup"><span data-stu-id="900c8-601">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="900c8-602">Rozšířené možnosti konfigurace protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="900c8-602">Advanced HTTP configuration options</span></span>

<span data-ttu-id="900c8-603">Pomocí `HttpConnectionDispatcherOptions` můžete nakonfigurovat upřesňující nastavení týkající se přenosů a správy vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="900c8-603">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="900c8-604">Tyto možnosti jsou nakonfigurovány předáním delegáta [MapHub\<t >](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="900c8-604">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="900c8-605">Následující tabulka obsahuje popis možností konfigurace ASP.NET Core SignalRpokročilé možnosti protokolu HTTP:</span><span class="sxs-lookup"><span data-stu-id="900c8-605">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="900c8-606">Možnost</span><span class="sxs-lookup"><span data-stu-id="900c8-606">Option</span></span> | <span data-ttu-id="900c8-607">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="900c8-607">Default Value</span></span> | <span data-ttu-id="900c8-608">Popis</span><span class="sxs-lookup"><span data-stu-id="900c8-608">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="900c8-609">32 KB</span><span class="sxs-lookup"><span data-stu-id="900c8-609">32 KB</span></span> | <span data-ttu-id="900c8-610">Maximální počet bajtů přijatých od klienta, které jsou vyrovnávací paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="900c8-610">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="900c8-611">Zvýšením této hodnoty umožníte serveru přijímat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="900c8-611">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="900c8-612">Data, která se automaticky shromažďují z atributů `Authorize` použitých pro třídu centra</span><span class="sxs-lookup"><span data-stu-id="900c8-612">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="900c8-613">Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru</span><span class="sxs-lookup"><span data-stu-id="900c8-613">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="900c8-614">32 KB</span><span class="sxs-lookup"><span data-stu-id="900c8-614">32 KB</span></span> | <span data-ttu-id="900c8-615">Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="900c8-615">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="900c8-616">Zvýšením této hodnoty umožníte serveru odesílat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="900c8-616">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="900c8-617">Všechny přenosy jsou povolené.</span><span class="sxs-lookup"><span data-stu-id="900c8-617">All Transports are enabled.</span></span> | <span data-ttu-id="900c8-618">Bitové příznaky vyčíslují `HttpTransportType` hodnoty, které mohou omezit přenos, který může klient použít pro připojení.</span><span class="sxs-lookup"><span data-stu-id="900c8-618">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="900c8-619">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="900c8-619">See below.</span></span> | <span data-ttu-id="900c8-620">Další možnosti specifické pro přenos dlouhého cyklického dotazování.</span><span class="sxs-lookup"><span data-stu-id="900c8-620">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="900c8-621">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="900c8-621">See below.</span></span> | <span data-ttu-id="900c8-622">Další možnosti specifické pro přenos pomocí protokolu WebSockets</span><span class="sxs-lookup"><span data-stu-id="900c8-622">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="900c8-623">Přenos dlouhého cyklického dotazování má další možnosti, které je možné konfigurovat pomocí vlastnosti `LongPolling`:</span><span class="sxs-lookup"><span data-stu-id="900c8-623">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="900c8-624">Možnost</span><span class="sxs-lookup"><span data-stu-id="900c8-624">Option</span></span> | <span data-ttu-id="900c8-625">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="900c8-625">Default Value</span></span> | <span data-ttu-id="900c8-626">Popis</span><span class="sxs-lookup"><span data-stu-id="900c8-626">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="900c8-627">90 sekund</span><span class="sxs-lookup"><span data-stu-id="900c8-627">90 seconds</span></span> | <span data-ttu-id="900c8-628">Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jedné žádosti o cyklické dotazování.</span><span class="sxs-lookup"><span data-stu-id="900c8-628">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="900c8-629">Snížení této hodnoty způsobí, že klient bude vydávat nové požadavky na dotaz častěji.</span><span class="sxs-lookup"><span data-stu-id="900c8-629">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="900c8-630">Přenos přes protokol WebSocket má další možnosti, které je možné konfigurovat pomocí vlastnosti `WebSockets`:</span><span class="sxs-lookup"><span data-stu-id="900c8-630">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="900c8-631">Možnost</span><span class="sxs-lookup"><span data-stu-id="900c8-631">Option</span></span> | <span data-ttu-id="900c8-632">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="900c8-632">Default Value</span></span> | <span data-ttu-id="900c8-633">Popis</span><span class="sxs-lookup"><span data-stu-id="900c8-633">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="900c8-634">5 sekund</span><span class="sxs-lookup"><span data-stu-id="900c8-634">5 seconds</span></span> | <span data-ttu-id="900c8-635">Pokud se po ukončení serveru aplikace v tomto časovém intervalu nepovede zavřít, připojení se ukončí.</span><span class="sxs-lookup"><span data-stu-id="900c8-635">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="900c8-636">Delegát, který lze použít k nastavení záhlaví `Sec-WebSocket-Protocol` na vlastní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="900c8-636">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="900c8-637">Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že se vrátí požadovaná hodnota.</span><span class="sxs-lookup"><span data-stu-id="900c8-637">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="900c8-638">Konfigurace možností klienta</span><span class="sxs-lookup"><span data-stu-id="900c8-638">Configure client options</span></span>

<span data-ttu-id="900c8-639">Možnosti klienta lze nakonfigurovat u `HubConnectionBuilder`ho typu (k dispozici v klientech rozhraní .NET a v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="900c8-639">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="900c8-640">Je také k dispozici v klientovi Java, ale `HttpHubConnectionBuilder` podtřídou je to, co obsahuje možnosti konfigurace tvůrce a také na `HubConnection` sám.</span><span class="sxs-lookup"><span data-stu-id="900c8-640">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="900c8-641">Konfigurovat protokolování</span><span class="sxs-lookup"><span data-stu-id="900c8-641">Configure logging</span></span>

<span data-ttu-id="900c8-642">Protokolování je konfigurováno v klientovi .NET pomocí metody `ConfigureLogging`.</span><span class="sxs-lookup"><span data-stu-id="900c8-642">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="900c8-643">Zprostředkovatelé protokolování a filtry je možné zaregistrovat stejným způsobem jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="900c8-643">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="900c8-644">Další informace najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="900c8-644">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="900c8-645">Aby bylo možné registrovat poskytovatele protokolování, je nutné nainstalovat potřebné balíčky.</span><span class="sxs-lookup"><span data-stu-id="900c8-645">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="900c8-646">Úplný seznam najdete v části [předdefinovaná zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="900c8-646">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="900c8-647">Chcete-li například povolit protokolování konzoly, nainstalujte balíček `Microsoft.Extensions.Logging.Console` NuGet.</span><span class="sxs-lookup"><span data-stu-id="900c8-647">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="900c8-648">Zavolejte metodu rozšíření `AddConsole`:</span><span class="sxs-lookup"><span data-stu-id="900c8-648">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="900c8-649">V klientovi jazyka JavaScript existuje podobná `configureLogging` metoda.</span><span class="sxs-lookup"><span data-stu-id="900c8-649">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="900c8-650">Zadejte `LogLevel`ovou hodnotu označující minimální úroveň zpráv protokolu, které se mají vytvořit.</span><span class="sxs-lookup"><span data-stu-id="900c8-650">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="900c8-651">Protokoly se zapisují do okna konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="900c8-651">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="900c8-652">Pokud chcete protokolování zcela zakázat, zadejte `signalR.LogLevel.None` v metodě `configureLogging`.</span><span class="sxs-lookup"><span data-stu-id="900c8-652">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="900c8-653">Další informace o protokolování najdete v dokumentaci k [diagnosticeSignalR](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="900c8-653">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="900c8-654">SignalR klient Java používá k protokolování knihovnu [SLF4J](https://www.slf4j.org/) .</span><span class="sxs-lookup"><span data-stu-id="900c8-654">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="900c8-655">Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí.</span><span class="sxs-lookup"><span data-stu-id="900c8-655">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="900c8-656">Následující fragment kódu ukazuje, jak použít `java.util.logging` s klientem SignalR Java.</span><span class="sxs-lookup"><span data-stu-id="900c8-656">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="900c8-657">Pokud ve svých závislostech nenakonfigurujete protokolování, SLF4J načte výchozí protokolovací nástroj No-operation s následující zprávou upozornění:</span><span class="sxs-lookup"><span data-stu-id="900c8-657">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="900c8-658">Tuto možnost lze bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="900c8-658">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="900c8-659">Konfigurace povolených přenosů</span><span class="sxs-lookup"><span data-stu-id="900c8-659">Configure allowed transports</span></span>

<span data-ttu-id="900c8-660">Transporty používané SignalR lze konfigurovat v volání `WithUrl` (`withUrl` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="900c8-660">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="900c8-661">Bitové nebo hodnoty `HttpTransportType` lze použít k omezení klienta na používání pouze určených přenosů.</span><span class="sxs-lookup"><span data-stu-id="900c8-661">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="900c8-662">Ve výchozím nastavení jsou povolené všechny přenosy.</span><span class="sxs-lookup"><span data-stu-id="900c8-662">All transports are enabled by default.</span></span>

<span data-ttu-id="900c8-663">Pokud třeba chcete zakázat přenos událostí odeslaných serverem, ale povolit WebSockets a dlouhá připojení s dotazem:</span><span class="sxs-lookup"><span data-stu-id="900c8-663">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="900c8-664">V klientu jazyka JavaScript jsou přenosy konfigurovány nastavením pole `transport` v objektu Options, který je k dispozici pro `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="900c8-664">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="900c8-665">Konfigurace ověřování nosiče</span><span class="sxs-lookup"><span data-stu-id="900c8-665">Configure bearer authentication</span></span>

<span data-ttu-id="900c8-666">K poskytnutí ověřovacích dat spolu s požadavky SignalR použijte možnost `AccessTokenProvider` (`accessTokenFactory` v JavaScriptu) k určení funkce, která vrací požadovaný přístupový token.</span><span class="sxs-lookup"><span data-stu-id="900c8-666">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="900c8-667">V klientovi .NET se tento přístupový token předává jako token ověřování HTTP "Bearer" (pomocí `Authorization` záhlaví s typem `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="900c8-667">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="900c8-668">V klientu jazyka JavaScript se přístupový token používá jako nosný token, **s výjimkou** případů, kdy rozhraní API prohlížeče omezuje možnost použít hlavičky (konkrétně v požadavcích na události odeslané serverem a objekty WebSockets).</span><span class="sxs-lookup"><span data-stu-id="900c8-668">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="900c8-669">V těchto případech je přístupový token k dispozici jako hodnota řetězce dotazu `access_token`.</span><span class="sxs-lookup"><span data-stu-id="900c8-669">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="900c8-670">V klientu .NET lze zadat možnost `AccessTokenProvider` pomocí delegáta možností v `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="900c8-670">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="900c8-671">V klientovi JavaScriptu je přístupový token nakonfigurovaný nastavením pole `accessTokenFactory` u objektu Options v `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="900c8-671">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="900c8-672">V SignalR klienta Java můžete nakonfigurovat nosný token, který se má použít k ověřování tím, že poskytne továrnu přístupového tokenu [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="900c8-672">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="900c8-673">Použijte [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) k poskytnutí [> jednoho\<ového řetězce](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava) .</span><span class="sxs-lookup"><span data-stu-id="900c8-673">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="900c8-674">Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.</span><span class="sxs-lookup"><span data-stu-id="900c8-674">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="900c8-675">Konfigurace možností timeout a Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="900c8-675">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="900c8-676">Další možnosti konfigurace časového limitu a chování při udržování připojení jsou k dispozici na samotném objektu `HubConnection`:</span><span class="sxs-lookup"><span data-stu-id="900c8-676">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="900c8-677">.NET</span><span class="sxs-lookup"><span data-stu-id="900c8-677">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="900c8-678">Možnost</span><span class="sxs-lookup"><span data-stu-id="900c8-678">Option</span></span> | <span data-ttu-id="900c8-679">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="900c8-679">Default value</span></span> | <span data-ttu-id="900c8-680">Popis</span><span class="sxs-lookup"><span data-stu-id="900c8-680">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="900c8-681">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="900c8-681">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="900c8-682">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="900c8-682">Timeout for server activity.</span></span> <span data-ttu-id="900c8-683">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí událost `Closed` (`onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="900c8-683">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="900c8-684">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="900c8-684">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="900c8-685">Doporučená hodnota je číslo minimálně dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy k zadání času.</span><span class="sxs-lookup"><span data-stu-id="900c8-685">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="900c8-686">15 sekund</span><span class="sxs-lookup"><span data-stu-id="900c8-686">15 seconds</span></span> | <span data-ttu-id="900c8-687">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="900c8-687">Timeout for initial server handshake.</span></span> <span data-ttu-id="900c8-688">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje událost `Closed` (`onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="900c8-688">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="900c8-689">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="900c8-689">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="900c8-690">Další informace o procesu handshake najdete v tématu [specifikace protokoluSignalR hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="900c8-690">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="900c8-691">V klientovi .NET jsou hodnoty časového limitu zadány jako `TimeSpan` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="900c8-691">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="900c8-692">JavaScript</span><span class="sxs-lookup"><span data-stu-id="900c8-692">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="900c8-693">Možnost</span><span class="sxs-lookup"><span data-stu-id="900c8-693">Option</span></span> | <span data-ttu-id="900c8-694">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="900c8-694">Default value</span></span> | <span data-ttu-id="900c8-695">Popis</span><span class="sxs-lookup"><span data-stu-id="900c8-695">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="900c8-696">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="900c8-696">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="900c8-697">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="900c8-697">Timeout for server activity.</span></span> <span data-ttu-id="900c8-698">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí událost `onclose`.</span><span class="sxs-lookup"><span data-stu-id="900c8-698">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="900c8-699">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="900c8-699">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="900c8-700">Doporučená hodnota je číslo minimálně dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy k zadání času.</span><span class="sxs-lookup"><span data-stu-id="900c8-700">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="900c8-701">Java</span><span class="sxs-lookup"><span data-stu-id="900c8-701">Java</span></span>](#tab/java)

| <span data-ttu-id="900c8-702">Možnost</span><span class="sxs-lookup"><span data-stu-id="900c8-702">Option</span></span> | <span data-ttu-id="900c8-703">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="900c8-703">Default value</span></span> | <span data-ttu-id="900c8-704">Popis</span><span class="sxs-lookup"><span data-stu-id="900c8-704">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="900c8-705">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="900c8-705">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="900c8-706">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="900c8-706">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="900c8-707">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="900c8-707">Timeout for server activity.</span></span> <span data-ttu-id="900c8-708">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí událost `onClose`.</span><span class="sxs-lookup"><span data-stu-id="900c8-708">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="900c8-709">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="900c8-709">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="900c8-710">Doporučená hodnota je číslo minimálně dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy k zadání času.</span><span class="sxs-lookup"><span data-stu-id="900c8-710">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="900c8-711">15 sekund</span><span class="sxs-lookup"><span data-stu-id="900c8-711">15 seconds</span></span> | <span data-ttu-id="900c8-712">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="900c8-712">Timeout for initial server handshake.</span></span> <span data-ttu-id="900c8-713">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje událost `onClose`.</span><span class="sxs-lookup"><span data-stu-id="900c8-713">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="900c8-714">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="900c8-714">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="900c8-715">Další informace o procesu handshake najdete v tématu [specifikace protokoluSignalR hub](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="900c8-715">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="900c8-716">Konfigurace dalších možností</span><span class="sxs-lookup"><span data-stu-id="900c8-716">Configure additional options</span></span>

<span data-ttu-id="900c8-717">Další možnosti lze nakonfigurovat v metodě `WithUrl` (`withUrl` in JavaScript) na `HubConnectionBuilder` nebo na různých konfiguračních rozhraních API `HttpHubConnectionBuilder` v klientovi Java:</span><span class="sxs-lookup"><span data-stu-id="900c8-717">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="nettabdotnet"></a>[<span data-ttu-id="900c8-718">.NET</span><span class="sxs-lookup"><span data-stu-id="900c8-718">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="900c8-719">Možnost .NET</span><span class="sxs-lookup"><span data-stu-id="900c8-719">.NET Option</span></span> |  <span data-ttu-id="900c8-720">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="900c8-720">Default value</span></span> | <span data-ttu-id="900c8-721">Popis</span><span class="sxs-lookup"><span data-stu-id="900c8-721">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="900c8-722">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="900c8-722">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="900c8-723">Nastavte tuto hodnotu na `true`, aby se přeskočil krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="900c8-723">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="900c8-724">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="900c8-724">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="900c8-725">Toto nastavení se nedá povolit při použití služby Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="900c8-725">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="900c8-726">Prázdné</span><span class="sxs-lookup"><span data-stu-id="900c8-726">Empty</span></span> | <span data-ttu-id="900c8-727">Kolekce certifikátů TLS pro odeslání na požadavky na ověření.</span><span class="sxs-lookup"><span data-stu-id="900c8-727">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="900c8-728">Prázdné</span><span class="sxs-lookup"><span data-stu-id="900c8-728">Empty</span></span> | <span data-ttu-id="900c8-729">Kolekce souborů cookie protokolu HTTP, které se mají odeslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="900c8-729">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="900c8-730">Prázdné</span><span class="sxs-lookup"><span data-stu-id="900c8-730">Empty</span></span> | <span data-ttu-id="900c8-731">Přihlašovací údaje, které se mají poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="900c8-731">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="900c8-732">5 sekund</span><span class="sxs-lookup"><span data-stu-id="900c8-732">5 seconds</span></span> | <span data-ttu-id="900c8-733">Jenom objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="900c8-733">WebSockets only.</span></span> <span data-ttu-id="900c8-734">Maximální doba, po jejímž uplynutí bude klient čekat na ukončení žádosti o uzavření.</span><span class="sxs-lookup"><span data-stu-id="900c8-734">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="900c8-735">Pokud server nepotvrdí zavření v této době, klient se odpojí.</span><span class="sxs-lookup"><span data-stu-id="900c8-735">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="900c8-736">Prázdné</span><span class="sxs-lookup"><span data-stu-id="900c8-736">Empty</span></span> | <span data-ttu-id="900c8-737">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="900c8-737">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="900c8-738">Delegát, který lze použít ke konfiguraci nebo nahrazení `HttpMessageHandler` používaných k odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="900c8-738">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="900c8-739">Nepoužívá se pro připojení pomocí protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="900c8-739">Not used for WebSocket connections.</span></span> <span data-ttu-id="900c8-740">Tento delegát musí vracet hodnotu, která není null, a obdrží výchozí hodnotu jako parametr.</span><span class="sxs-lookup"><span data-stu-id="900c8-740">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="900c8-741">Buď změňte nastavení pro tuto výchozí hodnotu a vraťte je, nebo vraťte novou instanci `HttpMessageHandler`.</span><span class="sxs-lookup"><span data-stu-id="900c8-741">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="900c8-742">**Při nahrazování obslužné rutiny Nezapomeňte zkopírovat nastavení, která chcete zachovat, od poskytnuté obslužné rutiny, jinak konfigurované možnosti (například soubory cookie a záhlaví) nebudou použity pro novou obslužnou rutinu.**</span><span class="sxs-lookup"><span data-stu-id="900c8-742">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="900c8-743">Proxy server HTTP, který se má použít při odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="900c8-743">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="900c8-744">Nastavte tuto logickou hodnotu pro odeslání výchozích přihlašovacích údajů pro požadavky HTTP a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="900c8-744">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="900c8-745">To umožňuje použití ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="900c8-745">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="900c8-746">Delegát, který lze použít ke konfiguraci dalších možností protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="900c8-746">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="900c8-747">Přijímá instanci [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , která se dá použít ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="900c8-747">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascripttabjavascript"></a>[<span data-ttu-id="900c8-748">JavaScript</span><span class="sxs-lookup"><span data-stu-id="900c8-748">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="900c8-749">Možnost JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="900c8-749">JavaScript Option</span></span> | <span data-ttu-id="900c8-750">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="900c8-750">Default Value</span></span> | <span data-ttu-id="900c8-751">Popis</span><span class="sxs-lookup"><span data-stu-id="900c8-751">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="900c8-752">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="900c8-752">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="900c8-753">Nastavte tuto hodnotu na `true`, aby se přeskočil krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="900c8-753">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="900c8-754">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="900c8-754">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="900c8-755">Toto nastavení se nedá povolit při použití služby Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="900c8-755">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="javatabjava"></a>[<span data-ttu-id="900c8-756">Java</span><span class="sxs-lookup"><span data-stu-id="900c8-756">Java</span></span>](#tab/java)

| <span data-ttu-id="900c8-757">Možnost jazyka Java</span><span class="sxs-lookup"><span data-stu-id="900c8-757">Java Option</span></span> | <span data-ttu-id="900c8-758">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="900c8-758">Default Value</span></span> | <span data-ttu-id="900c8-759">Popis</span><span class="sxs-lookup"><span data-stu-id="900c8-759">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="900c8-760">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="900c8-760">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="900c8-761">Nastavte tuto hodnotu na `true`, aby se přeskočil krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="900c8-761">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="900c8-762">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="900c8-762">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="900c8-763">Toto nastavení se nedá povolit při použití služby Azure SignalR.</span><span class="sxs-lookup"><span data-stu-id="900c8-763">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="900c8-764">`withHeader``withHeaders`</span><span class="sxs-lookup"><span data-stu-id="900c8-764">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="900c8-765">Prázdné</span><span class="sxs-lookup"><span data-stu-id="900c8-765">Empty</span></span> | <span data-ttu-id="900c8-766">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="900c8-766">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="900c8-767">V klientu .NET můžete tyto možnosti upravit pomocí delegáta možností, který je k dispozici pro `WithUrl`:</span><span class="sxs-lookup"><span data-stu-id="900c8-767">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="900c8-768">V klientovi JavaScriptu lze tyto možnosti poskytnout v objektu jazyka JavaScript, který je k dispozici pro `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="900c8-768">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="900c8-769">V klientovi Java je možné nakonfigurovat tyto možnosti pomocí metod `HttpHubConnectionBuilder` vrácených z `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="900c8-769">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="900c8-770">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="900c8-770">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end