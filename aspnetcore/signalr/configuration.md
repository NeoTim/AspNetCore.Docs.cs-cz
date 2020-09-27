---
title: SignalRKonfigurace ASP.NET Core
author: bradygaster
description: Naučte se konfigurovat SignalR aplikace ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 579491cfe60a26593ca038a1691f9b52f0fb1d06
ms.sourcegitcommit: 74f4a4ddbe3c2f11e2e09d05d2a979784d89d3f5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2020
ms.locfileid: "91393870"
---
# <a name="aspnet-core-no-locsignalr-configuration"></a><span data-ttu-id="633b4-103">SignalRKonfigurace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="633b4-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="633b4-104">Možnosti serializace JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="633b4-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="633b4-105">ASP.NET Core SignalR podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="633b4-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="633b4-106">Každý protokol má možnosti konfigurace serializace.</span><span class="sxs-lookup"><span data-stu-id="633b4-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="633b4-107">Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="633b4-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="633b4-108">`AddJsonProtocol`lze přidat po [Přidání SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) do `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="633b4-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="633b4-109">`AddJsonProtocol`Metoda přebírá delegáta, který přijímá `options` objekt.</span><span class="sxs-lookup"><span data-stu-id="633b4-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="633b4-110">Vlastnost [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) tohoto objektu je `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objekt, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="633b4-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="633b4-111">Další informace najdete v [System.Text.Jsv dokumentaci](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="633b4-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="633b4-112">Chcete-li například nakonfigurovat serializátor, aby neměnil velká a malá písmena názvů vlastností namísto výchozích názvů "camelCase", použijte následující kód v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="633b4-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="633b4-113">V klientovi .NET `AddJsonProtocol` existuje stejná rozšiřující metoda v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="633b4-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="633b4-114">`Microsoft.Extensions.DependencyInjection`Obor názvů musí být importován, aby bylo možné přeložit metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="633b4-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="633b4-115">V tuto chvíli není možné konfigurovat serializaci JSON v klientovi jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="633b4-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="633b4-116">Přepnout na Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="633b4-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="633b4-117">Pokud potřebujete funkce nástroje `Newtonsoft.Json` , které nejsou podporované v systému `System.Text.Json` , přečtěte si téma [Přepnutí na Newtonsoft.Jsna](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="633b4-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="633b4-118">Možnosti serializace MessagePack</span><span class="sxs-lookup"><span data-stu-id="633b4-118">MessagePack serialization options</span></span>

<span data-ttu-id="633b4-119">Serializaci MessagePack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="633b4-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="633b4-120">Další podrobnosti najdete [v SignalR tématu MessagePack](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="633b4-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="633b4-121">V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi v jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="633b4-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="633b4-122">Konfigurovat možnosti serveru</span><span class="sxs-lookup"><span data-stu-id="633b4-122">Configure server options</span></span>

<span data-ttu-id="633b4-123">Následující tabulka popisuje možnosti konfigurace SignalR Center:</span><span class="sxs-lookup"><span data-stu-id="633b4-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="633b4-124">Možnost</span><span class="sxs-lookup"><span data-stu-id="633b4-124">Option</span></span> | <span data-ttu-id="633b4-125">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-125">Default Value</span></span> | <span data-ttu-id="633b4-126">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="633b4-127">30 sekund</span><span class="sxs-lookup"><span data-stu-id="633b4-127">30 seconds</span></span> | <span data-ttu-id="633b4-128">Server bude předpokládat, že je klient odpojený, pokud v tomto intervalu neobdržel zprávu (včetně Keep-Alive).</span><span class="sxs-lookup"><span data-stu-id="633b4-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="633b4-129">Může trvat delší dobu, než je tento časový limit, aby bylo možné klienta ve skutečnosti označit jako odpojený, protože to je implementováno.</span><span class="sxs-lookup"><span data-stu-id="633b4-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="633b4-130">Doporučená hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="633b4-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="633b4-131">15 sekund</span><span class="sxs-lookup"><span data-stu-id="633b4-131">15 seconds</span></span> | <span data-ttu-id="633b4-132">Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře.</span><span class="sxs-lookup"><span data-stu-id="633b4-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="633b4-133">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="633b4-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="633b4-134">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="633b4-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="633b4-135">15 sekund</span><span class="sxs-lookup"><span data-stu-id="633b4-135">15 seconds</span></span> | <span data-ttu-id="633b4-136">Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="633b4-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="633b4-137">Při změně `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení změňte nastavení v klientovi.</span><span class="sxs-lookup"><span data-stu-id="633b4-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="633b4-138">Doporučená `ServerTimeout` / `serverTimeoutInMilliseconds` hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="633b4-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="633b4-139">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="633b4-139">All installed protocols</span></span> | <span data-ttu-id="633b4-140">Protokoly podporované tímto rozbočovačem</span><span class="sxs-lookup"><span data-stu-id="633b4-140">Protocols supported by this hub.</span></span> <span data-ttu-id="633b4-141">Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly.</span><span class="sxs-lookup"><span data-stu-id="633b4-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="633b4-142">Pokud se `true` do klientů vrátí podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="633b4-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="633b4-143">Výchozí hodnota je `false` , protože tyto zprávy o výjimce můžou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="633b4-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="633b4-144">Maximální počet položek, které lze uložit do vyrovnávací paměti pro datové proudy pro odeslání klienta.</span><span class="sxs-lookup"><span data-stu-id="633b4-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="633b4-145">Pokud je dosaženo tohoto limitu, zpracování volání je blokováno, dokud server nezpracovává položky datového proudu.</span><span class="sxs-lookup"><span data-stu-id="633b4-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="633b4-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="633b4-146">32 KB</span></span> | <span data-ttu-id="633b4-147">Maximální velikost jedné příchozí zprávy centra</span><span class="sxs-lookup"><span data-stu-id="633b4-147">Maximum size of a single incoming hub message.</span></span> |
| `MaximumParallelInvocationsPerClient` | <span data-ttu-id="633b4-148">1</span><span class="sxs-lookup"><span data-stu-id="633b4-148">1</span></span> | <span data-ttu-id="633b4-149">Maximální počet metod centra, které může každý klient volat paralelně před zařazením do fronty.</span><span class="sxs-lookup"><span data-stu-id="633b4-149">The maximum number of hub methods that each client can call in parallel before queueing.</span></span> |

<span data-ttu-id="633b4-150">Možnosti lze nakonfigurovat pro všechna centra tím, že poskytnete možnosti delegáta pro `AddSignalR` volání v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="633b4-150">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="633b4-151">Možnosti pro jeden rozbočovač přepíší globální možnosti poskytované v `AddSignalR` a můžou být nakonfigurované pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="633b4-151">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="633b4-152">Rozšířené možnosti konfigurace protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="633b4-152">Advanced HTTP configuration options</span></span>

<span data-ttu-id="633b4-153">Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="633b4-153">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="633b4-154">Tyto možnosti jsou nakonfigurovány předáním delegáta [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="633b4-154">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="633b4-155">Následující tabulka popisuje možnosti konfigurace SignalR rozšířených možností protokolu HTTP v ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="633b4-155">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="633b4-156">Možnost</span><span class="sxs-lookup"><span data-stu-id="633b4-156">Option</span></span> | <span data-ttu-id="633b4-157">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-157">Default Value</span></span> | <span data-ttu-id="633b4-158">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-158">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="633b4-159">32 KB</span><span class="sxs-lookup"><span data-stu-id="633b4-159">32 KB</span></span> | <span data-ttu-id="633b4-160">Maximální počet bajtů přijatých od klienta, které jsou ukládány do vyrovnávací paměti serveru před použitím protitlaku.</span><span class="sxs-lookup"><span data-stu-id="633b4-160">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="633b4-161">Zvýšením této hodnoty umožníte, aby server přijímal větší zprávy rychleji bez použití zatížení, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="633b4-161">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="633b4-162">Data se automaticky shromažďují z `Authorize` atributů použitých pro třídu centra.</span><span class="sxs-lookup"><span data-stu-id="633b4-162">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="633b4-163">Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru</span><span class="sxs-lookup"><span data-stu-id="633b4-163">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="633b4-164">32 KB</span><span class="sxs-lookup"><span data-stu-id="633b4-164">32 KB</span></span> | <span data-ttu-id="633b4-165">Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru před pozorováním protitlaku.</span><span class="sxs-lookup"><span data-stu-id="633b4-165">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="633b4-166">Zvýšením této hodnoty umožníte serveru rychleji ukládat větší zprávy bez nutnosti očekávat zatížení, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="633b4-166">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="633b4-167">Všechny přenosy jsou povolené.</span><span class="sxs-lookup"><span data-stu-id="633b4-167">All Transports are enabled.</span></span> | <span data-ttu-id="633b4-168">Bitové příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenos, který může klient použít pro připojení.</span><span class="sxs-lookup"><span data-stu-id="633b4-168">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="633b4-169">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="633b4-169">See below.</span></span> | <span data-ttu-id="633b4-170">Další možnosti specifické pro přenos dlouhého cyklického dotazování.</span><span class="sxs-lookup"><span data-stu-id="633b4-170">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="633b4-171">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="633b4-171">See below.</span></span> | <span data-ttu-id="633b4-172">Další možnosti specifické pro přenos pomocí protokolu WebSockets</span><span class="sxs-lookup"><span data-stu-id="633b4-172">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="633b4-173">0</span><span class="sxs-lookup"><span data-stu-id="633b4-173">0</span></span> | <span data-ttu-id="633b4-174">Zadejte minimální verzi protokolu Negotiate.</span><span class="sxs-lookup"><span data-stu-id="633b4-174">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="633b4-175">Slouží k omezení klientů na novější verze.</span><span class="sxs-lookup"><span data-stu-id="633b4-175">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="633b4-176">Přenos dlouhého cyklického dotazování má další možnosti, které je možné konfigurovat pomocí `LongPolling` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="633b4-176">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="633b4-177">Možnost</span><span class="sxs-lookup"><span data-stu-id="633b4-177">Option</span></span> | <span data-ttu-id="633b4-178">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-178">Default Value</span></span> | <span data-ttu-id="633b4-179">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-179">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="633b4-180">90 sekund</span><span class="sxs-lookup"><span data-stu-id="633b4-180">90 seconds</span></span> | <span data-ttu-id="633b4-181">Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jedné žádosti o cyklické dotazování.</span><span class="sxs-lookup"><span data-stu-id="633b4-181">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="633b4-182">Snížení této hodnoty způsobí, že klient bude vydávat nové požadavky na dotaz častěji.</span><span class="sxs-lookup"><span data-stu-id="633b4-182">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="633b4-183">Přenos protokolu WebSocket má další možnosti, které je možné konfigurovat pomocí `WebSockets` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="633b4-183">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="633b4-184">Možnost</span><span class="sxs-lookup"><span data-stu-id="633b4-184">Option</span></span> | <span data-ttu-id="633b4-185">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-185">Default Value</span></span> | <span data-ttu-id="633b4-186">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-186">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="633b4-187">5 sekund</span><span class="sxs-lookup"><span data-stu-id="633b4-187">5 seconds</span></span> | <span data-ttu-id="633b4-188">Pokud se po ukončení serveru aplikace v tomto časovém intervalu nepovede zavřít, připojení se ukončí.</span><span class="sxs-lookup"><span data-stu-id="633b4-188">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="633b4-189">Delegát, který lze použít k nastavení `Sec-WebSocket-Protocol` záhlaví na vlastní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="633b4-189">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="633b4-190">Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že se vrátí požadovaná hodnota.</span><span class="sxs-lookup"><span data-stu-id="633b4-190">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="633b4-191">Konfigurace možností klienta</span><span class="sxs-lookup"><span data-stu-id="633b4-191">Configure client options</span></span>

<span data-ttu-id="633b4-192">Možnosti klienta lze nakonfigurovat pro `HubConnectionBuilder` typ (k dispozici v klientech rozhraní .NET a v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="633b4-192">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="633b4-193">Je také k dispozici v klientovi Java, ale `HttpHubConnectionBuilder` podtřídou je to, co obsahuje možnosti konfigurace tvůrce a také na `HubConnection` sebe sama.</span><span class="sxs-lookup"><span data-stu-id="633b4-193">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="633b4-194">Konfigurovat protokolování</span><span class="sxs-lookup"><span data-stu-id="633b4-194">Configure logging</span></span>

<span data-ttu-id="633b4-195">Protokolování je konfigurováno v klientovi .NET pomocí `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="633b4-195">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="633b4-196">Zprostředkovatelé protokolování a filtry je možné zaregistrovat stejným způsobem jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="633b4-196">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="633b4-197">Další informace najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="633b4-197">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="633b4-198">Aby bylo možné registrovat poskytovatele protokolování, je nutné nainstalovat potřebné balíčky.</span><span class="sxs-lookup"><span data-stu-id="633b4-198">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="633b4-199">Úplný seznam najdete v části [předdefinovaná zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="633b4-199">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="633b4-200">Chcete-li například povolit protokolování konzoly, nainstalujte `Microsoft.Extensions.Logging.Console` balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="633b4-200">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="633b4-201">Zavolejte `AddConsole` metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="633b4-201">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="633b4-202">V klientovi jazyka JavaScript existuje podobná `configureLogging` metoda.</span><span class="sxs-lookup"><span data-stu-id="633b4-202">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="633b4-203">Zadejte `LogLevel` hodnotu, která určuje minimální úroveň zpráv protokolu, které mají být vyprodukovány.</span><span class="sxs-lookup"><span data-stu-id="633b4-203">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="633b4-204">Protokoly se zapisují do okna konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="633b4-204">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="633b4-205">Místo `LogLevel` hodnoty můžete zadat také `string` hodnotu představující název úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="633b4-205">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="633b4-206">To je užitečné při konfiguraci SignalR protokolování v prostředích, kde nemáte přístup k `LogLevel` konstantám.</span><span class="sxs-lookup"><span data-stu-id="633b4-206">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="633b4-207">V následující tabulce jsou uvedeny dostupné úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="633b4-207">The following table lists the available log levels.</span></span> <span data-ttu-id="633b4-208">Hodnota, kterou zadáte, `configureLogging` nastaví **minimální** úroveň protokolu, která se bude protokolovat.</span><span class="sxs-lookup"><span data-stu-id="633b4-208">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="633b4-209">Zprávy zaznamenané na této úrovni **nebo úrovně uvedené za ní v tabulce**budou protokolovány.</span><span class="sxs-lookup"><span data-stu-id="633b4-209">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="633b4-210">Řetězec</span><span class="sxs-lookup"><span data-stu-id="633b4-210">String</span></span>                      | <span data-ttu-id="633b4-211">LogLevel</span><span class="sxs-lookup"><span data-stu-id="633b4-211">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="633b4-212">`info` **nebo** `information`</span><span class="sxs-lookup"><span data-stu-id="633b4-212">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="633b4-213">`warn` **nebo** `warning`</span><span class="sxs-lookup"><span data-stu-id="633b4-213">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="633b4-214">Chcete-li protokolování zcela zakázat, zadejte `signalR.LogLevel.None` v `configureLogging` metodě.</span><span class="sxs-lookup"><span data-stu-id="633b4-214">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="633b4-215">Další informace o protokolování naleznete v dokumentaci k [ SignalR diagnostice](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="633b4-215">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="633b4-216">SignalRKlient Java používá k protokolování knihovnu [SLF4J](https://www.slf4j.org/) .</span><span class="sxs-lookup"><span data-stu-id="633b4-216">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="633b4-217">Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí.</span><span class="sxs-lookup"><span data-stu-id="633b4-217">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="633b4-218">Následující fragment kódu ukazuje, jak používat `java.util.logging` s SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="633b4-218">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="633b4-219">Pokud ve svých závislostech nenakonfigurujete protokolování, SLF4J načte výchozí protokolovací nástroj No-operation s následující zprávou upozornění:</span><span class="sxs-lookup"><span data-stu-id="633b4-219">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="633b4-220">Tuto možnost lze bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="633b4-220">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="633b4-221">Konfigurace povolených přenosů</span><span class="sxs-lookup"><span data-stu-id="633b4-221">Configure allowed transports</span></span>

<span data-ttu-id="633b4-222">Přenosy používané nástrojem SignalR lze konfigurovat ve `WithUrl` volání ( `withUrl` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="633b4-222">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="633b4-223">Bitové nebo hodnoty `HttpTransportType` lze použít k omezení klienta na používání pouze zadaných přenosů.</span><span class="sxs-lookup"><span data-stu-id="633b4-223">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="633b4-224">Ve výchozím nastavení jsou povolené všechny přenosy.</span><span class="sxs-lookup"><span data-stu-id="633b4-224">All transports are enabled by default.</span></span>

<span data-ttu-id="633b4-225">Pokud třeba chcete zakázat přenos událostí odeslaných serverem, ale povolit WebSockets a dlouhá připojení s dotazem:</span><span class="sxs-lookup"><span data-stu-id="633b4-225">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="633b4-226">V klientu jazyka JavaScript jsou přenosy konfigurovány nastavením `transport` pole v objektu Options, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="633b4-226">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="633b4-227">V této verzi je jediným dostupným přenosem klientský WebSocket v jazyce Java.</span><span class="sxs-lookup"><span data-stu-id="633b4-227">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="633b4-228">V klientovi Java se přenos vybere s `withTransport` metodou na `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="633b4-228">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="633b4-229">Klient Java standardně používá přenos pomocí protokolu WebSockets.</span><span class="sxs-lookup"><span data-stu-id="633b4-229">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="633b4-230">SignalRKlient Java ještě nepodporuje přenosovou zálohu.</span><span class="sxs-lookup"><span data-stu-id="633b4-230">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="633b4-231">Konfigurace ověřování nosiče</span><span class="sxs-lookup"><span data-stu-id="633b4-231">Configure bearer authentication</span></span>

<span data-ttu-id="633b4-232">K poskytnutí ověřovacích dat spolu s SignalR požadavky použijte `AccessTokenProvider` možnost ( `accessTokenFactory` v jazyce JavaScript) k určení funkce, která vrací požadovaný přístupový token.</span><span class="sxs-lookup"><span data-stu-id="633b4-232">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="633b4-233">V klientovi .NET se tento přístupový token předává jako token ověřování HTTP "Bearer" (s použitím `Authorization` hlavičky s typem `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="633b4-233">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="633b4-234">V klientu jazyka JavaScript se přístupový token používá jako nosný token, **s výjimkou** případů, kdy rozhraní API prohlížeče omezuje možnost použít hlavičky (konkrétně v požadavcích na události odeslané serverem a objekty WebSockets).</span><span class="sxs-lookup"><span data-stu-id="633b4-234">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="633b4-235">V těchto případech je přístupový token k dispozici jako hodnota řetězce dotazu `access_token` .</span><span class="sxs-lookup"><span data-stu-id="633b4-235">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="633b4-236">V klientu .NET `AccessTokenProvider` lze možnost zadat pomocí delegáta možnosti v `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="633b4-236">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="633b4-237">V klientovi JavaScriptu je přístupový token nakonfigurovaný nastavením `accessTokenFactory` pole v objektu Options v `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="633b4-237">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="633b4-238">V SignalR klientovi Java můžete nakonfigurovat nosný token, který se bude používat pro ověřování, a to poskytnutím továrny přístupového tokenu [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="633b4-238">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="633b4-239">Pomocí [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) můžete zadat [RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="633b4-239">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="633b4-240">Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.</span><span class="sxs-lookup"><span data-stu-id="633b4-240">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="633b4-241">Konfigurace možností timeout a Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="633b4-241">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="633b4-242">Další možnosti konfigurace časového limitu a chování funkce Keep-Alive jsou k dispozici na `HubConnection` samotném objektu:</span><span class="sxs-lookup"><span data-stu-id="633b4-242">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="633b4-243">.NET</span><span class="sxs-lookup"><span data-stu-id="633b4-243">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="633b4-244">Možnost</span><span class="sxs-lookup"><span data-stu-id="633b4-244">Option</span></span> | <span data-ttu-id="633b4-245">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-245">Default value</span></span> | <span data-ttu-id="633b4-246">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-246">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="633b4-247">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="633b4-247">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="633b4-248">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="633b4-248">Timeout for server activity.</span></span> <span data-ttu-id="633b4-249">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí `Closed` událost ( `onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="633b4-249">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="633b4-250">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="633b4-250">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="633b4-251">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="633b4-251">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="633b4-252">15 sekund</span><span class="sxs-lookup"><span data-stu-id="633b4-252">15 seconds</span></span> | <span data-ttu-id="633b4-253">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="633b4-253">Timeout for initial server handshake.</span></span> <span data-ttu-id="633b4-254">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a spustí `Closed` událost ( `onclose` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="633b4-254">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="633b4-255">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="633b4-255">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="633b4-256">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="633b4-256">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="633b4-257">15 sekund</span><span class="sxs-lookup"><span data-stu-id="633b4-257">15 seconds</span></span> | <span data-ttu-id="633b4-258">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="633b4-258">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="633b4-259">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="633b4-259">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="633b4-260">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="633b4-260">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="633b4-261">V klientovi .NET jsou hodnoty časového limitu zadány jako `TimeSpan` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="633b4-261">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="633b4-262">JavaScript</span><span class="sxs-lookup"><span data-stu-id="633b4-262">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="633b4-263">Možnost</span><span class="sxs-lookup"><span data-stu-id="633b4-263">Option</span></span> | <span data-ttu-id="633b4-264">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-264">Default value</span></span> | <span data-ttu-id="633b4-265">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-265">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="633b4-266">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="633b4-266">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="633b4-267">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="633b4-267">Timeout for server activity.</span></span> <span data-ttu-id="633b4-268">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onclose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="633b4-268">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="633b4-269">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="633b4-269">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="633b4-270">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="633b4-270">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="633b4-271">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="633b4-271">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="633b4-272">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="633b4-272">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="633b4-273">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="633b4-273">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="633b4-274">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="633b4-274">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="633b4-275">Java</span><span class="sxs-lookup"><span data-stu-id="633b4-275">Java</span></span>](#tab/java)

| <span data-ttu-id="633b4-276">Možnost</span><span class="sxs-lookup"><span data-stu-id="633b4-276">Option</span></span> | <span data-ttu-id="633b4-277">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-277">Default value</span></span> | <span data-ttu-id="633b4-278">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-278">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="633b4-279">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="633b4-279">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="633b4-280">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="633b4-280">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="633b4-281">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="633b4-281">Timeout for server activity.</span></span> <span data-ttu-id="633b4-282">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onClose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="633b4-282">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="633b4-283">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="633b4-283">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="633b4-284">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="633b4-284">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="633b4-285">15 sekund</span><span class="sxs-lookup"><span data-stu-id="633b4-285">15 seconds</span></span> | <span data-ttu-id="633b4-286">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="633b4-286">Timeout for initial server handshake.</span></span> <span data-ttu-id="633b4-287">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje `onClose` událost.</span><span class="sxs-lookup"><span data-stu-id="633b4-287">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="633b4-288">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="633b4-288">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="633b4-289">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="633b4-289">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="633b4-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="633b4-290">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="633b4-291">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="633b4-291">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="633b4-292">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="633b4-292">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="633b4-293">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="633b4-293">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="633b4-294">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="633b4-294">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="633b4-295">Konfigurace dalších možností</span><span class="sxs-lookup"><span data-stu-id="633b4-295">Configure additional options</span></span>

<span data-ttu-id="633b4-296">Další možnosti lze konfigurovat v `WithUrl` `withUrl` metodě (v jazyce JavaScript) na `HubConnectionBuilder` různých konfiguračních rozhraních API v `HttpHubConnectionBuilder` klientovi Java:</span><span class="sxs-lookup"><span data-stu-id="633b4-296">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="633b4-297">.NET</span><span class="sxs-lookup"><span data-stu-id="633b4-297">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="633b4-298">Možnost .NET</span><span class="sxs-lookup"><span data-stu-id="633b4-298">.NET Option</span></span> |  <span data-ttu-id="633b4-299">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-299">Default value</span></span> | <span data-ttu-id="633b4-300">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-300">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="633b4-301">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="633b4-301">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="633b4-302">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="633b4-302">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="633b4-303">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="633b4-303">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="633b4-304">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="633b4-304">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="633b4-305">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="633b4-305">Empty</span></span> | <span data-ttu-id="633b4-306">Kolekce certifikátů TLS pro odeslání na požadavky na ověření.</span><span class="sxs-lookup"><span data-stu-id="633b4-306">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="633b4-307">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="633b4-307">Empty</span></span> | <span data-ttu-id="633b4-308">Kolekce HTTP cookie s pro odeslání s každou žádostí http</span><span class="sxs-lookup"><span data-stu-id="633b4-308">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="633b4-309">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="633b4-309">Empty</span></span> | <span data-ttu-id="633b4-310">Přihlašovací údaje, které se mají poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="633b4-310">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="633b4-311">5 sekund</span><span class="sxs-lookup"><span data-stu-id="633b4-311">5 seconds</span></span> | <span data-ttu-id="633b4-312">Jenom objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="633b4-312">WebSockets only.</span></span> <span data-ttu-id="633b4-313">Maximální doba, po jejímž uplynutí bude klient čekat na ukončení žádosti o uzavření.</span><span class="sxs-lookup"><span data-stu-id="633b4-313">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="633b4-314">Pokud server nepotvrdí zavření v této době, klient se odpojí.</span><span class="sxs-lookup"><span data-stu-id="633b4-314">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="633b4-315">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="633b4-315">Empty</span></span> | <span data-ttu-id="633b4-316">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="633b4-316">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="633b4-317">Delegát, který lze použít ke konfiguraci nebo nahrazení `HttpMessageHandler` používaného k odeslání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="633b4-317">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="633b4-318">Nepoužívá se pro připojení pomocí protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="633b4-318">Not used for WebSocket connections.</span></span> <span data-ttu-id="633b4-319">Tento delegát musí vracet hodnotu, která není null, a obdrží výchozí hodnotu jako parametr.</span><span class="sxs-lookup"><span data-stu-id="633b4-319">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="633b4-320">Buď změňte nastavení u této výchozí hodnoty a vraťte je, nebo vraťte novou `HttpMessageHandler` instanci.</span><span class="sxs-lookup"><span data-stu-id="633b4-320">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="633b4-321">**Při nahrazování obslužné rutiny Nezapomeňte zkopírovat nastavení, která chcete zachovat od poskytnuté obslužné rutiny, jinak konfigurované možnosti (například Cookie s a záhlaví) nebudou použity pro novou obslužnou rutinu.**</span><span class="sxs-lookup"><span data-stu-id="633b4-321">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="633b4-322">Proxy server HTTP, který se má použít při odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="633b4-322">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="633b4-323">Nastavte tuto logickou hodnotu pro odeslání výchozích přihlašovacích údajů pro požadavky HTTP a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="633b4-323">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="633b4-324">To umožňuje použití ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="633b4-324">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="633b4-325">Delegát, který lze použít ke konfiguraci dalších možností protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="633b4-325">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="633b4-326">Přijímá instanci [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , která se dá použít ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="633b4-326">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="633b4-327">JavaScript</span><span class="sxs-lookup"><span data-stu-id="633b4-327">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="633b4-328">Možnost JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="633b4-328">JavaScript Option</span></span> | <span data-ttu-id="633b4-329">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-329">Default Value</span></span> | <span data-ttu-id="633b4-330">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-330">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="633b4-331">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="633b4-331">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `headers` | `null` | <span data-ttu-id="633b4-332">Slovník hlaviček odeslaných s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="633b4-332">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="633b4-333">Odesílání hlaviček v prohlížeči nefunguje pro objekty WebSockets ani <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> Stream.</span><span class="sxs-lookup"><span data-stu-id="633b4-333">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="633b4-334">Nastavte na `true` protokolování bajtů/znaků zpráv odesílaných a přijímaných klientem.</span><span class="sxs-lookup"><span data-stu-id="633b4-334">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="633b4-335">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="633b4-335">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="633b4-336">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="633b4-336">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="633b4-337">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="633b4-337">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="633b4-338">Určuje, jestli se budou odesílat přihlašovací údaje s požadavkem CORS.</span><span class="sxs-lookup"><span data-stu-id="633b4-338">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="633b4-339">Azure App Service používá cookie s pro rychlé relace a vyžaduje, aby tato možnost fungovala správně.</span><span class="sxs-lookup"><span data-stu-id="633b4-339">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="633b4-340">Další informace o CORS s SignalR najdete v tématu <xref:signalr/security#cross-origin-resource-sharing> .</span><span class="sxs-lookup"><span data-stu-id="633b4-340">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="633b4-341">Java</span><span class="sxs-lookup"><span data-stu-id="633b4-341">Java</span></span>](#tab/java)

| <span data-ttu-id="633b4-342">Možnost jazyka Java</span><span class="sxs-lookup"><span data-stu-id="633b4-342">Java Option</span></span> | <span data-ttu-id="633b4-343">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-343">Default Value</span></span> | <span data-ttu-id="633b4-344">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-344">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="633b4-345">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="633b4-345">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="633b4-346">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="633b4-346">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="633b4-347">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="633b4-347">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="633b4-348">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="633b4-348">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="633b4-349">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="633b4-349">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="633b4-350">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="633b4-350">Empty</span></span> | <span data-ttu-id="633b4-351">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="633b4-351">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="633b4-352">V klientu .NET můžete tyto možnosti upravit pomocí delegáta možností, který je k dispozici pro `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="633b4-352">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="633b4-353">V klientovi JavaScriptu lze tyto možnosti poskytnout v objektu jazyka JavaScript, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="633b4-353">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="633b4-354">V klientovi Java lze tyto možnosti nakonfigurovat s metodami `HttpHubConnectionBuilder` vrácenými z `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="633b4-354">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="633b4-355">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="633b4-355">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="633b4-356">Možnosti serializace JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="633b4-356">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="633b4-357">ASP.NET Core SignalR podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="633b4-357">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="633b4-358">Každý protokol má možnosti konfigurace serializace.</span><span class="sxs-lookup"><span data-stu-id="633b4-358">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="633b4-359">Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="633b4-359">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="633b4-360">`AddJsonProtocol`lze přidat po [Přidání SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) do `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="633b4-360">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="633b4-361">`AddJsonProtocol`Metoda přebírá delegáta, který přijímá `options` objekt.</span><span class="sxs-lookup"><span data-stu-id="633b4-361">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="633b4-362">Vlastnost [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) tohoto objektu je `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objekt, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="633b4-362">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="633b4-363">Další informace najdete v [System.Text.Jsv dokumentaci](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="633b4-363">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="633b4-364">Chcete-li například nakonfigurovat serializátor, aby neměnil velká a malá písmena názvů vlastností namísto výchozích názvů "camelCase", použijte následující kód v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="633b4-364">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="633b4-365">V klientovi .NET `AddJsonProtocol` existuje stejná rozšiřující metoda v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="633b4-365">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="633b4-366">`Microsoft.Extensions.DependencyInjection`Obor názvů musí být importován, aby bylo možné přeložit metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="633b4-366">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="633b4-367">V tuto chvíli není možné konfigurovat serializaci JSON v klientovi jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="633b4-367">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="633b4-368">Přepnout na Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="633b4-368">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="633b4-369">Pokud potřebujete funkce nástroje `Newtonsoft.Json` , které nejsou podporované v systému `System.Text.Json` , přečtěte si téma [Přepnutí na Newtonsoft.Jsna](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="633b4-369">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="633b4-370">Možnosti serializace MessagePack</span><span class="sxs-lookup"><span data-stu-id="633b4-370">MessagePack serialization options</span></span>

<span data-ttu-id="633b4-371">Serializaci MessagePack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="633b4-371">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="633b4-372">Další podrobnosti najdete [v SignalR tématu MessagePack](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="633b4-372">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="633b4-373">V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi v jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="633b4-373">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="633b4-374">Konfigurovat možnosti serveru</span><span class="sxs-lookup"><span data-stu-id="633b4-374">Configure server options</span></span>

<span data-ttu-id="633b4-375">Následující tabulka popisuje možnosti konfigurace SignalR Center:</span><span class="sxs-lookup"><span data-stu-id="633b4-375">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="633b4-376">Možnost</span><span class="sxs-lookup"><span data-stu-id="633b4-376">Option</span></span> | <span data-ttu-id="633b4-377">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-377">Default Value</span></span> | <span data-ttu-id="633b4-378">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-378">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="633b4-379">30 sekund</span><span class="sxs-lookup"><span data-stu-id="633b4-379">30 seconds</span></span> | <span data-ttu-id="633b4-380">Server bude předpokládat, že je klient odpojený, pokud v tomto intervalu neobdržel zprávu (včetně Keep-Alive).</span><span class="sxs-lookup"><span data-stu-id="633b4-380">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="633b4-381">Může trvat delší dobu, než je tento časový limit, aby bylo možné klienta ve skutečnosti označit jako odpojený, protože to je implementováno.</span><span class="sxs-lookup"><span data-stu-id="633b4-381">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="633b4-382">Doporučená hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="633b4-382">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="633b4-383">15 sekund</span><span class="sxs-lookup"><span data-stu-id="633b4-383">15 seconds</span></span> | <span data-ttu-id="633b4-384">Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře.</span><span class="sxs-lookup"><span data-stu-id="633b4-384">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="633b4-385">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="633b4-385">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="633b4-386">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="633b4-386">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="633b4-387">15 sekund</span><span class="sxs-lookup"><span data-stu-id="633b4-387">15 seconds</span></span> | <span data-ttu-id="633b4-388">Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="633b4-388">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="633b4-389">Při změně `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení změňte nastavení v klientovi.</span><span class="sxs-lookup"><span data-stu-id="633b4-389">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="633b4-390">Doporučená `ServerTimeout` / `serverTimeoutInMilliseconds` hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="633b4-390">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="633b4-391">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="633b4-391">All installed protocols</span></span> | <span data-ttu-id="633b4-392">Protokoly podporované tímto rozbočovačem</span><span class="sxs-lookup"><span data-stu-id="633b4-392">Protocols supported by this hub.</span></span> <span data-ttu-id="633b4-393">Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly.</span><span class="sxs-lookup"><span data-stu-id="633b4-393">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="633b4-394">Pokud se `true` do klientů vrátí podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="633b4-394">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="633b4-395">Výchozí hodnota je `false` , protože tyto zprávy o výjimce můžou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="633b4-395">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="633b4-396">Maximální počet položek, které lze uložit do vyrovnávací paměti pro datové proudy pro odeslání klienta.</span><span class="sxs-lookup"><span data-stu-id="633b4-396">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="633b4-397">Pokud je dosaženo tohoto limitu, zpracování volání je blokováno, dokud server nezpracovává položky datového proudu.</span><span class="sxs-lookup"><span data-stu-id="633b4-397">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="633b4-398">32 KB</span><span class="sxs-lookup"><span data-stu-id="633b4-398">32 KB</span></span> | <span data-ttu-id="633b4-399">Maximální velikost jedné příchozí zprávy centra</span><span class="sxs-lookup"><span data-stu-id="633b4-399">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="633b4-400">Možnosti lze nakonfigurovat pro všechna centra tím, že poskytnete možnosti delegáta pro `AddSignalR` volání v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="633b4-400">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="633b4-401">Možnosti pro jeden rozbočovač přepíší globální možnosti poskytované v `AddSignalR` a můžou být nakonfigurované pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="633b4-401">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="633b4-402">Rozšířené možnosti konfigurace protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="633b4-402">Advanced HTTP configuration options</span></span>

<span data-ttu-id="633b4-403">Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="633b4-403">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="633b4-404">Tyto možnosti jsou nakonfigurovány předáním delegáta [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="633b4-404">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="633b4-405">Následující tabulka popisuje možnosti konfigurace SignalR rozšířených možností protokolu HTTP v ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="633b4-405">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="633b4-406">Možnost</span><span class="sxs-lookup"><span data-stu-id="633b4-406">Option</span></span> | <span data-ttu-id="633b4-407">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-407">Default Value</span></span> | <span data-ttu-id="633b4-408">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-408">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="633b4-409">32 KB</span><span class="sxs-lookup"><span data-stu-id="633b4-409">32 KB</span></span> | <span data-ttu-id="633b4-410">Maximální počet bajtů přijatých od klienta, které jsou ukládány do vyrovnávací paměti serveru před použitím protitlaku.</span><span class="sxs-lookup"><span data-stu-id="633b4-410">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="633b4-411">Zvýšením této hodnoty umožníte, aby server přijímal větší zprávy rychleji bez použití zatížení, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="633b4-411">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="633b4-412">Data se automaticky shromažďují z `Authorize` atributů použitých pro třídu centra.</span><span class="sxs-lookup"><span data-stu-id="633b4-412">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="633b4-413">Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru</span><span class="sxs-lookup"><span data-stu-id="633b4-413">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="633b4-414">32 KB</span><span class="sxs-lookup"><span data-stu-id="633b4-414">32 KB</span></span> | <span data-ttu-id="633b4-415">Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru před pozorováním protitlaku.</span><span class="sxs-lookup"><span data-stu-id="633b4-415">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="633b4-416">Zvýšením této hodnoty umožníte serveru rychleji ukládat větší zprávy bez nutnosti očekávat zatížení, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="633b4-416">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="633b4-417">Všechny přenosy jsou povolené.</span><span class="sxs-lookup"><span data-stu-id="633b4-417">All Transports are enabled.</span></span> | <span data-ttu-id="633b4-418">Bitové příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenos, který může klient použít pro připojení.</span><span class="sxs-lookup"><span data-stu-id="633b4-418">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="633b4-419">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="633b4-419">See below.</span></span> | <span data-ttu-id="633b4-420">Další možnosti specifické pro přenos dlouhého cyklického dotazování.</span><span class="sxs-lookup"><span data-stu-id="633b4-420">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="633b4-421">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="633b4-421">See below.</span></span> | <span data-ttu-id="633b4-422">Další možnosti specifické pro přenos pomocí protokolu WebSockets</span><span class="sxs-lookup"><span data-stu-id="633b4-422">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="633b4-423">0</span><span class="sxs-lookup"><span data-stu-id="633b4-423">0</span></span> | <span data-ttu-id="633b4-424">Zadejte minimální verzi protokolu Negotiate.</span><span class="sxs-lookup"><span data-stu-id="633b4-424">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="633b4-425">Slouží k omezení klientů na novější verze.</span><span class="sxs-lookup"><span data-stu-id="633b4-425">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="633b4-426">Přenos dlouhého cyklického dotazování má další možnosti, které je možné konfigurovat pomocí `LongPolling` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="633b4-426">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="633b4-427">Možnost</span><span class="sxs-lookup"><span data-stu-id="633b4-427">Option</span></span> | <span data-ttu-id="633b4-428">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-428">Default Value</span></span> | <span data-ttu-id="633b4-429">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-429">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="633b4-430">90 sekund</span><span class="sxs-lookup"><span data-stu-id="633b4-430">90 seconds</span></span> | <span data-ttu-id="633b4-431">Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jedné žádosti o cyklické dotazování.</span><span class="sxs-lookup"><span data-stu-id="633b4-431">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="633b4-432">Snížení této hodnoty způsobí, že klient bude vydávat nové požadavky na dotaz častěji.</span><span class="sxs-lookup"><span data-stu-id="633b4-432">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="633b4-433">Přenos protokolu WebSocket má další možnosti, které je možné konfigurovat pomocí `WebSockets` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="633b4-433">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="633b4-434">Možnost</span><span class="sxs-lookup"><span data-stu-id="633b4-434">Option</span></span> | <span data-ttu-id="633b4-435">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-435">Default Value</span></span> | <span data-ttu-id="633b4-436">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-436">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="633b4-437">5 sekund</span><span class="sxs-lookup"><span data-stu-id="633b4-437">5 seconds</span></span> | <span data-ttu-id="633b4-438">Pokud se po ukončení serveru aplikace v tomto časovém intervalu nepovede zavřít, připojení se ukončí.</span><span class="sxs-lookup"><span data-stu-id="633b4-438">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="633b4-439">Delegát, který lze použít k nastavení `Sec-WebSocket-Protocol` záhlaví na vlastní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="633b4-439">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="633b4-440">Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že se vrátí požadovaná hodnota.</span><span class="sxs-lookup"><span data-stu-id="633b4-440">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="633b4-441">Konfigurace možností klienta</span><span class="sxs-lookup"><span data-stu-id="633b4-441">Configure client options</span></span>

<span data-ttu-id="633b4-442">Možnosti klienta lze nakonfigurovat pro `HubConnectionBuilder` typ (k dispozici v klientech rozhraní .NET a v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="633b4-442">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="633b4-443">Je také k dispozici v klientovi Java, ale `HttpHubConnectionBuilder` podtřídou je to, co obsahuje možnosti konfigurace tvůrce a také na `HubConnection` sebe sama.</span><span class="sxs-lookup"><span data-stu-id="633b4-443">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="633b4-444">Konfigurovat protokolování</span><span class="sxs-lookup"><span data-stu-id="633b4-444">Configure logging</span></span>

<span data-ttu-id="633b4-445">Protokolování je konfigurováno v klientovi .NET pomocí `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="633b4-445">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="633b4-446">Zprostředkovatelé protokolování a filtry je možné zaregistrovat stejným způsobem jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="633b4-446">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="633b4-447">Další informace najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="633b4-447">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="633b4-448">Aby bylo možné registrovat poskytovatele protokolování, je nutné nainstalovat potřebné balíčky.</span><span class="sxs-lookup"><span data-stu-id="633b4-448">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="633b4-449">Úplný seznam najdete v části [předdefinovaná zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="633b4-449">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="633b4-450">Chcete-li například povolit protokolování konzoly, nainstalujte `Microsoft.Extensions.Logging.Console` balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="633b4-450">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="633b4-451">Zavolejte `AddConsole` metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="633b4-451">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="633b4-452">V klientovi jazyka JavaScript existuje podobná `configureLogging` metoda.</span><span class="sxs-lookup"><span data-stu-id="633b4-452">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="633b4-453">Zadejte `LogLevel` hodnotu, která určuje minimální úroveň zpráv protokolu, které mají být vyprodukovány.</span><span class="sxs-lookup"><span data-stu-id="633b4-453">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="633b4-454">Protokoly se zapisují do okna konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="633b4-454">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="633b4-455">Místo `LogLevel` hodnoty můžete zadat také `string` hodnotu představující název úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="633b4-455">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="633b4-456">To je užitečné při konfiguraci SignalR protokolování v prostředích, kde nemáte přístup k `LogLevel` konstantám.</span><span class="sxs-lookup"><span data-stu-id="633b4-456">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="633b4-457">V následující tabulce jsou uvedeny dostupné úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="633b4-457">The following table lists the available log levels.</span></span> <span data-ttu-id="633b4-458">Hodnota, kterou zadáte, `configureLogging` nastaví **minimální** úroveň protokolu, která se bude protokolovat.</span><span class="sxs-lookup"><span data-stu-id="633b4-458">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="633b4-459">Zprávy zaznamenané na této úrovni **nebo úrovně uvedené za ní v tabulce**budou protokolovány.</span><span class="sxs-lookup"><span data-stu-id="633b4-459">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="633b4-460">Řetězec</span><span class="sxs-lookup"><span data-stu-id="633b4-460">String</span></span>                      | <span data-ttu-id="633b4-461">LogLevel</span><span class="sxs-lookup"><span data-stu-id="633b4-461">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="633b4-462">`info` **nebo** `information`</span><span class="sxs-lookup"><span data-stu-id="633b4-462">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="633b4-463">`warn` **nebo** `warning`</span><span class="sxs-lookup"><span data-stu-id="633b4-463">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="633b4-464">Chcete-li protokolování zcela zakázat, zadejte `signalR.LogLevel.None` v `configureLogging` metodě.</span><span class="sxs-lookup"><span data-stu-id="633b4-464">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="633b4-465">Další informace o protokolování naleznete v dokumentaci k [ SignalR diagnostice](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="633b4-465">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="633b4-466">SignalRKlient Java používá k protokolování knihovnu [SLF4J](https://www.slf4j.org/) .</span><span class="sxs-lookup"><span data-stu-id="633b4-466">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="633b4-467">Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí.</span><span class="sxs-lookup"><span data-stu-id="633b4-467">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="633b4-468">Následující fragment kódu ukazuje, jak používat `java.util.logging` s SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="633b4-468">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="633b4-469">Pokud ve svých závislostech nenakonfigurujete protokolování, SLF4J načte výchozí protokolovací nástroj No-operation s následující zprávou upozornění:</span><span class="sxs-lookup"><span data-stu-id="633b4-469">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="633b4-470">Tuto možnost lze bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="633b4-470">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="633b4-471">Konfigurace povolených přenosů</span><span class="sxs-lookup"><span data-stu-id="633b4-471">Configure allowed transports</span></span>

<span data-ttu-id="633b4-472">Přenosy používané nástrojem SignalR lze konfigurovat ve `WithUrl` volání ( `withUrl` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="633b4-472">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="633b4-473">Bitové nebo hodnoty `HttpTransportType` lze použít k omezení klienta na používání pouze zadaných přenosů.</span><span class="sxs-lookup"><span data-stu-id="633b4-473">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="633b4-474">Ve výchozím nastavení jsou povolené všechny přenosy.</span><span class="sxs-lookup"><span data-stu-id="633b4-474">All transports are enabled by default.</span></span>

<span data-ttu-id="633b4-475">Pokud třeba chcete zakázat přenos událostí odeslaných serverem, ale povolit WebSockets a dlouhá připojení s dotazem:</span><span class="sxs-lookup"><span data-stu-id="633b4-475">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="633b4-476">V klientu jazyka JavaScript jsou přenosy konfigurovány nastavením `transport` pole v objektu Options, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="633b4-476">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="633b4-477">V této verzi je jediným dostupným přenosem klientský WebSocket v jazyce Java.</span><span class="sxs-lookup"><span data-stu-id="633b4-477">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="633b4-478">V klientovi Java se přenos vybere s `withTransport` metodou na `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="633b4-478">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="633b4-479">Klient Java standardně používá přenos pomocí protokolu WebSockets.</span><span class="sxs-lookup"><span data-stu-id="633b4-479">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="633b4-480">SignalRKlient Java ještě nepodporuje přenosovou zálohu.</span><span class="sxs-lookup"><span data-stu-id="633b4-480">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="633b4-481">Konfigurace ověřování nosiče</span><span class="sxs-lookup"><span data-stu-id="633b4-481">Configure bearer authentication</span></span>

<span data-ttu-id="633b4-482">K poskytnutí ověřovacích dat spolu s SignalR požadavky použijte `AccessTokenProvider` možnost ( `accessTokenFactory` v jazyce JavaScript) k určení funkce, která vrací požadovaný přístupový token.</span><span class="sxs-lookup"><span data-stu-id="633b4-482">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="633b4-483">V klientovi .NET se tento přístupový token předává jako token ověřování HTTP "Bearer" (s použitím `Authorization` hlavičky s typem `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="633b4-483">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="633b4-484">V klientu jazyka JavaScript se přístupový token používá jako nosný token, **s výjimkou** případů, kdy rozhraní API prohlížeče omezuje možnost použít hlavičky (konkrétně v požadavcích na události odeslané serverem a objekty WebSockets).</span><span class="sxs-lookup"><span data-stu-id="633b4-484">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="633b4-485">V těchto případech je přístupový token k dispozici jako hodnota řetězce dotazu `access_token` .</span><span class="sxs-lookup"><span data-stu-id="633b4-485">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="633b4-486">V klientu .NET `AccessTokenProvider` lze možnost zadat pomocí delegáta možnosti v `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="633b4-486">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="633b4-487">V klientovi JavaScriptu je přístupový token nakonfigurovaný nastavením `accessTokenFactory` pole v objektu Options v `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="633b4-487">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="633b4-488">V SignalR klientovi Java můžete nakonfigurovat nosný token, který se bude používat pro ověřování, a to poskytnutím továrny přístupového tokenu [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="633b4-488">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="633b4-489">Pomocí [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) můžete zadat [RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="633b4-489">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="633b4-490">Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.</span><span class="sxs-lookup"><span data-stu-id="633b4-490">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="633b4-491">Konfigurace možností timeout a Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="633b4-491">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="633b4-492">Další možnosti konfigurace časového limitu a chování funkce Keep-Alive jsou k dispozici na `HubConnection` samotném objektu:</span><span class="sxs-lookup"><span data-stu-id="633b4-492">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="633b4-493">.NET</span><span class="sxs-lookup"><span data-stu-id="633b4-493">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="633b4-494">Možnost</span><span class="sxs-lookup"><span data-stu-id="633b4-494">Option</span></span> | <span data-ttu-id="633b4-495">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-495">Default value</span></span> | <span data-ttu-id="633b4-496">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-496">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="633b4-497">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="633b4-497">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="633b4-498">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="633b4-498">Timeout for server activity.</span></span> <span data-ttu-id="633b4-499">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí `Closed` událost ( `onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="633b4-499">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="633b4-500">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="633b4-500">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="633b4-501">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="633b4-501">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="633b4-502">15 sekund</span><span class="sxs-lookup"><span data-stu-id="633b4-502">15 seconds</span></span> | <span data-ttu-id="633b4-503">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="633b4-503">Timeout for initial server handshake.</span></span> <span data-ttu-id="633b4-504">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a spustí `Closed` událost ( `onclose` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="633b4-504">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="633b4-505">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="633b4-505">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="633b4-506">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="633b4-506">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="633b4-507">15 sekund</span><span class="sxs-lookup"><span data-stu-id="633b4-507">15 seconds</span></span> | <span data-ttu-id="633b4-508">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="633b4-508">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="633b4-509">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="633b4-509">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="633b4-510">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="633b4-510">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="633b4-511">V klientovi .NET jsou hodnoty časového limitu zadány jako `TimeSpan` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="633b4-511">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="633b4-512">JavaScript</span><span class="sxs-lookup"><span data-stu-id="633b4-512">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="633b4-513">Možnost</span><span class="sxs-lookup"><span data-stu-id="633b4-513">Option</span></span> | <span data-ttu-id="633b4-514">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-514">Default value</span></span> | <span data-ttu-id="633b4-515">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-515">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="633b4-516">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="633b4-516">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="633b4-517">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="633b4-517">Timeout for server activity.</span></span> <span data-ttu-id="633b4-518">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onclose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="633b4-518">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="633b4-519">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="633b4-519">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="633b4-520">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="633b4-520">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="633b4-521">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="633b4-521">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="633b4-522">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="633b4-522">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="633b4-523">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="633b4-523">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="633b4-524">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="633b4-524">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="633b4-525">Java</span><span class="sxs-lookup"><span data-stu-id="633b4-525">Java</span></span>](#tab/java)

| <span data-ttu-id="633b4-526">Možnost</span><span class="sxs-lookup"><span data-stu-id="633b4-526">Option</span></span> | <span data-ttu-id="633b4-527">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-527">Default value</span></span> | <span data-ttu-id="633b4-528">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-528">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="633b4-529">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="633b4-529">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="633b4-530">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="633b4-530">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="633b4-531">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="633b4-531">Timeout for server activity.</span></span> <span data-ttu-id="633b4-532">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onClose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="633b4-532">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="633b4-533">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="633b4-533">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="633b4-534">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="633b4-534">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="633b4-535">15 sekund</span><span class="sxs-lookup"><span data-stu-id="633b4-535">15 seconds</span></span> | <span data-ttu-id="633b4-536">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="633b4-536">Timeout for initial server handshake.</span></span> <span data-ttu-id="633b4-537">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje `onClose` událost.</span><span class="sxs-lookup"><span data-stu-id="633b4-537">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="633b4-538">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="633b4-538">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="633b4-539">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="633b4-539">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="633b4-540">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="633b4-540">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="633b4-541">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="633b4-541">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="633b4-542">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="633b4-542">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="633b4-543">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="633b4-543">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="633b4-544">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="633b4-544">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="633b4-545">Konfigurace dalších možností</span><span class="sxs-lookup"><span data-stu-id="633b4-545">Configure additional options</span></span>

<span data-ttu-id="633b4-546">Další možnosti lze konfigurovat v `WithUrl` `withUrl` metodě (v jazyce JavaScript) na `HubConnectionBuilder` různých konfiguračních rozhraních API v `HttpHubConnectionBuilder` klientovi Java:</span><span class="sxs-lookup"><span data-stu-id="633b4-546">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="633b4-547">.NET</span><span class="sxs-lookup"><span data-stu-id="633b4-547">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="633b4-548">Možnost .NET</span><span class="sxs-lookup"><span data-stu-id="633b4-548">.NET Option</span></span> |  <span data-ttu-id="633b4-549">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-549">Default value</span></span> | <span data-ttu-id="633b4-550">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-550">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="633b4-551">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="633b4-551">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="633b4-552">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="633b4-552">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="633b4-553">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="633b4-553">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="633b4-554">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="633b4-554">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="633b4-555">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="633b4-555">Empty</span></span> | <span data-ttu-id="633b4-556">Kolekce certifikátů TLS pro odeslání na požadavky na ověření.</span><span class="sxs-lookup"><span data-stu-id="633b4-556">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="633b4-557">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="633b4-557">Empty</span></span> | <span data-ttu-id="633b4-558">Kolekce HTTP cookie s pro odeslání s každou žádostí http</span><span class="sxs-lookup"><span data-stu-id="633b4-558">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="633b4-559">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="633b4-559">Empty</span></span> | <span data-ttu-id="633b4-560">Přihlašovací údaje, které se mají poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="633b4-560">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="633b4-561">5 sekund</span><span class="sxs-lookup"><span data-stu-id="633b4-561">5 seconds</span></span> | <span data-ttu-id="633b4-562">Jenom objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="633b4-562">WebSockets only.</span></span> <span data-ttu-id="633b4-563">Maximální doba, po jejímž uplynutí bude klient čekat na ukončení žádosti o uzavření.</span><span class="sxs-lookup"><span data-stu-id="633b4-563">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="633b4-564">Pokud server nepotvrdí zavření v této době, klient se odpojí.</span><span class="sxs-lookup"><span data-stu-id="633b4-564">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="633b4-565">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="633b4-565">Empty</span></span> | <span data-ttu-id="633b4-566">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="633b4-566">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="633b4-567">Delegát, který lze použít ke konfiguraci nebo nahrazení `HttpMessageHandler` používaného k odeslání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="633b4-567">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="633b4-568">Nepoužívá se pro připojení pomocí protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="633b4-568">Not used for WebSocket connections.</span></span> <span data-ttu-id="633b4-569">Tento delegát musí vracet hodnotu, která není null, a obdrží výchozí hodnotu jako parametr.</span><span class="sxs-lookup"><span data-stu-id="633b4-569">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="633b4-570">Buď změňte nastavení u této výchozí hodnoty a vraťte je, nebo vraťte novou `HttpMessageHandler` instanci.</span><span class="sxs-lookup"><span data-stu-id="633b4-570">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="633b4-571">**Při nahrazování obslužné rutiny Nezapomeňte zkopírovat nastavení, která chcete zachovat od poskytnuté obslužné rutiny, jinak konfigurované možnosti (například Cookie s a záhlaví) nebudou použity pro novou obslužnou rutinu.**</span><span class="sxs-lookup"><span data-stu-id="633b4-571">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="633b4-572">Proxy server HTTP, který se má použít při odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="633b4-572">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="633b4-573">Nastavte tuto logickou hodnotu pro odeslání výchozích přihlašovacích údajů pro požadavky HTTP a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="633b4-573">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="633b4-574">To umožňuje použití ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="633b4-574">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="633b4-575">Delegát, který lze použít ke konfiguraci dalších možností protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="633b4-575">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="633b4-576">Přijímá instanci [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , která se dá použít ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="633b4-576">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="633b4-577">JavaScript</span><span class="sxs-lookup"><span data-stu-id="633b4-577">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="633b4-578">Možnost JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="633b4-578">JavaScript Option</span></span> | <span data-ttu-id="633b4-579">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-579">Default Value</span></span> | <span data-ttu-id="633b4-580">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-580">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="633b4-581">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="633b4-581">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="633b4-582">Nastavte na `true` protokolování bajtů/znaků zpráv odesílaných a přijímaných klientem.</span><span class="sxs-lookup"><span data-stu-id="633b4-582">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="633b4-583">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="633b4-583">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="633b4-584">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="633b4-584">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="633b4-585">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="633b4-585">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="633b4-586">Java</span><span class="sxs-lookup"><span data-stu-id="633b4-586">Java</span></span>](#tab/java)

| <span data-ttu-id="633b4-587">Možnost jazyka Java</span><span class="sxs-lookup"><span data-stu-id="633b4-587">Java Option</span></span> | <span data-ttu-id="633b4-588">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-588">Default Value</span></span> | <span data-ttu-id="633b4-589">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-589">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="633b4-590">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="633b4-590">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="633b4-591">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="633b4-591">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="633b4-592">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="633b4-592">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="633b4-593">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="633b4-593">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="633b4-594">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="633b4-594">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="633b4-595">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="633b4-595">Empty</span></span> | <span data-ttu-id="633b4-596">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="633b4-596">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="633b4-597">V klientu .NET můžete tyto možnosti upravit pomocí delegáta možností, který je k dispozici pro `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="633b4-597">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="633b4-598">V klientovi JavaScriptu lze tyto možnosti poskytnout v objektu jazyka JavaScript, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="633b4-598">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="633b4-599">V klientovi Java lze tyto možnosti nakonfigurovat s metodami `HttpHubConnectionBuilder` vrácenými z `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="633b4-599">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="633b4-600">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="633b4-600">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="633b4-601">Možnosti serializace JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="633b4-601">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="633b4-602">ASP.NET Core SignalR podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="633b4-602">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="633b4-603">Každý protokol má možnosti konfigurace serializace.</span><span class="sxs-lookup"><span data-stu-id="633b4-603">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="633b4-604">Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="633b4-604">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="633b4-605">`AddJsonProtocol`lze přidat po [Přidání SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) do `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="633b4-605">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="633b4-606">`AddJsonProtocol`Metoda přebírá delegáta, který přijímá `options` objekt.</span><span class="sxs-lookup"><span data-stu-id="633b4-606">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="633b4-607">Vlastnost [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) tohoto objektu je `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objekt, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="633b4-607">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="633b4-608">Další informace najdete v [System.Text.Jsv dokumentaci](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="633b4-608">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="633b4-609">Chcete-li například nakonfigurovat serializátor, aby neměnil velká a malá písmena názvů vlastností namísto výchozích názvů "camelCase", použijte následující kód v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="633b4-609">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="633b4-610">V klientovi .NET `AddJsonProtocol` existuje stejná rozšiřující metoda v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="633b4-610">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="633b4-611">`Microsoft.Extensions.DependencyInjection`Obor názvů musí být importován, aby bylo možné přeložit metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="633b4-611">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="633b4-612">V tuto chvíli není možné konfigurovat serializaci JSON v klientovi jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="633b4-612">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="633b4-613">Přepnout na Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="633b4-613">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="633b4-614">Pokud potřebujete funkce nástroje `Newtonsoft.Json` , které nejsou podporované v systému `System.Text.Json` , přečtěte si téma [Přepnutí na Newtonsoft.Jsna](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="633b4-614">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="633b4-615">Možnosti serializace MessagePack</span><span class="sxs-lookup"><span data-stu-id="633b4-615">MessagePack serialization options</span></span>

<span data-ttu-id="633b4-616">Serializaci MessagePack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="633b4-616">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="633b4-617">Další podrobnosti najdete [v SignalR tématu MessagePack](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="633b4-617">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="633b4-618">V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi v jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="633b4-618">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="633b4-619">Konfigurovat možnosti serveru</span><span class="sxs-lookup"><span data-stu-id="633b4-619">Configure server options</span></span>

<span data-ttu-id="633b4-620">Následující tabulka popisuje možnosti konfigurace SignalR Center:</span><span class="sxs-lookup"><span data-stu-id="633b4-620">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="633b4-621">Možnost</span><span class="sxs-lookup"><span data-stu-id="633b4-621">Option</span></span> | <span data-ttu-id="633b4-622">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-622">Default Value</span></span> | <span data-ttu-id="633b4-623">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-623">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="633b4-624">30 sekund</span><span class="sxs-lookup"><span data-stu-id="633b4-624">30 seconds</span></span> | <span data-ttu-id="633b4-625">Server bude předpokládat, že je klient odpojený, pokud v tomto intervalu neobdržel zprávu (včetně Keep-Alive).</span><span class="sxs-lookup"><span data-stu-id="633b4-625">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="633b4-626">Může trvat delší dobu, než je tento časový limit, aby bylo možné klienta ve skutečnosti označit jako odpojený, protože to je implementováno.</span><span class="sxs-lookup"><span data-stu-id="633b4-626">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="633b4-627">Doporučená hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="633b4-627">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="633b4-628">15 sekund</span><span class="sxs-lookup"><span data-stu-id="633b4-628">15 seconds</span></span> | <span data-ttu-id="633b4-629">Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře.</span><span class="sxs-lookup"><span data-stu-id="633b4-629">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="633b4-630">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="633b4-630">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="633b4-631">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="633b4-631">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="633b4-632">15 sekund</span><span class="sxs-lookup"><span data-stu-id="633b4-632">15 seconds</span></span> | <span data-ttu-id="633b4-633">Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="633b4-633">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="633b4-634">Při změně `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení změňte nastavení v klientovi.</span><span class="sxs-lookup"><span data-stu-id="633b4-634">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="633b4-635">Doporučená `ServerTimeout` / `serverTimeoutInMilliseconds` hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="633b4-635">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="633b4-636">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="633b4-636">All installed protocols</span></span> | <span data-ttu-id="633b4-637">Protokoly podporované tímto rozbočovačem</span><span class="sxs-lookup"><span data-stu-id="633b4-637">Protocols supported by this hub.</span></span> <span data-ttu-id="633b4-638">Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly.</span><span class="sxs-lookup"><span data-stu-id="633b4-638">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="633b4-639">Pokud se `true` do klientů vrátí podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="633b4-639">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="633b4-640">Výchozí hodnota je `false` , protože tyto zprávy o výjimce můžou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="633b4-640">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="633b4-641">Maximální počet položek, které lze uložit do vyrovnávací paměti pro datové proudy pro odeslání klienta.</span><span class="sxs-lookup"><span data-stu-id="633b4-641">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="633b4-642">Pokud je dosaženo tohoto limitu, zpracování volání je blokováno, dokud server nezpracovává položky datového proudu.</span><span class="sxs-lookup"><span data-stu-id="633b4-642">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="633b4-643">32 KB</span><span class="sxs-lookup"><span data-stu-id="633b4-643">32 KB</span></span> | <span data-ttu-id="633b4-644">Maximální velikost jedné příchozí zprávy centra</span><span class="sxs-lookup"><span data-stu-id="633b4-644">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="633b4-645">Možnosti lze nakonfigurovat pro všechna centra tím, že poskytnete možnosti delegáta pro `AddSignalR` volání v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="633b4-645">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="633b4-646">Možnosti pro jeden rozbočovač přepíší globální možnosti poskytované v `AddSignalR` a můžou být nakonfigurované pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="633b4-646">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="633b4-647">Rozšířené možnosti konfigurace protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="633b4-647">Advanced HTTP configuration options</span></span>

<span data-ttu-id="633b4-648">Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="633b4-648">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="633b4-649">Tyto možnosti jsou nakonfigurovány předáním delegáta [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="633b4-649">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="633b4-650">Následující tabulka popisuje možnosti konfigurace SignalR rozšířených možností protokolu HTTP v ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="633b4-650">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="633b4-651">Možnost</span><span class="sxs-lookup"><span data-stu-id="633b4-651">Option</span></span> | <span data-ttu-id="633b4-652">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-652">Default Value</span></span> | <span data-ttu-id="633b4-653">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-653">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="633b4-654">32 KB</span><span class="sxs-lookup"><span data-stu-id="633b4-654">32 KB</span></span> | <span data-ttu-id="633b4-655">Maximální počet bajtů přijatých od klienta, které jsou ukládány do vyrovnávací paměti serveru před použitím protitlaku.</span><span class="sxs-lookup"><span data-stu-id="633b4-655">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="633b4-656">Zvýšením této hodnoty umožníte, aby server přijímal větší zprávy rychleji bez použití zatížení, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="633b4-656">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="633b4-657">Data se automaticky shromažďují z `Authorize` atributů použitých pro třídu centra.</span><span class="sxs-lookup"><span data-stu-id="633b4-657">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="633b4-658">Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru</span><span class="sxs-lookup"><span data-stu-id="633b4-658">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="633b4-659">32 KB</span><span class="sxs-lookup"><span data-stu-id="633b4-659">32 KB</span></span> | <span data-ttu-id="633b4-660">Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru před pozorováním protitlaku.</span><span class="sxs-lookup"><span data-stu-id="633b4-660">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="633b4-661">Zvýšením této hodnoty umožníte serveru rychleji ukládat větší zprávy bez nutnosti očekávat zatížení, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="633b4-661">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="633b4-662">Všechny přenosy jsou povolené.</span><span class="sxs-lookup"><span data-stu-id="633b4-662">All Transports are enabled.</span></span> | <span data-ttu-id="633b4-663">Bitové příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenos, který může klient použít pro připojení.</span><span class="sxs-lookup"><span data-stu-id="633b4-663">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="633b4-664">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="633b4-664">See below.</span></span> | <span data-ttu-id="633b4-665">Další možnosti specifické pro přenos dlouhého cyklického dotazování.</span><span class="sxs-lookup"><span data-stu-id="633b4-665">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="633b4-666">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="633b4-666">See below.</span></span> | <span data-ttu-id="633b4-667">Další možnosti specifické pro přenos pomocí protokolu WebSockets</span><span class="sxs-lookup"><span data-stu-id="633b4-667">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="633b4-668">Přenos dlouhého cyklického dotazování má další možnosti, které je možné konfigurovat pomocí `LongPolling` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="633b4-668">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="633b4-669">Možnost</span><span class="sxs-lookup"><span data-stu-id="633b4-669">Option</span></span> | <span data-ttu-id="633b4-670">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-670">Default Value</span></span> | <span data-ttu-id="633b4-671">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-671">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="633b4-672">90 sekund</span><span class="sxs-lookup"><span data-stu-id="633b4-672">90 seconds</span></span> | <span data-ttu-id="633b4-673">Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jedné žádosti o cyklické dotazování.</span><span class="sxs-lookup"><span data-stu-id="633b4-673">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="633b4-674">Snížení této hodnoty způsobí, že klient bude vydávat nové požadavky na dotaz častěji.</span><span class="sxs-lookup"><span data-stu-id="633b4-674">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="633b4-675">Přenos protokolu WebSocket má další možnosti, které je možné konfigurovat pomocí `WebSockets` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="633b4-675">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="633b4-676">Možnost</span><span class="sxs-lookup"><span data-stu-id="633b4-676">Option</span></span> | <span data-ttu-id="633b4-677">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-677">Default Value</span></span> | <span data-ttu-id="633b4-678">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-678">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="633b4-679">5 sekund</span><span class="sxs-lookup"><span data-stu-id="633b4-679">5 seconds</span></span> | <span data-ttu-id="633b4-680">Pokud se po ukončení serveru aplikace v tomto časovém intervalu nepovede zavřít, připojení se ukončí.</span><span class="sxs-lookup"><span data-stu-id="633b4-680">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="633b4-681">Delegát, který lze použít k nastavení `Sec-WebSocket-Protocol` záhlaví na vlastní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="633b4-681">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="633b4-682">Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že se vrátí požadovaná hodnota.</span><span class="sxs-lookup"><span data-stu-id="633b4-682">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="633b4-683">Konfigurace možností klienta</span><span class="sxs-lookup"><span data-stu-id="633b4-683">Configure client options</span></span>

<span data-ttu-id="633b4-684">Možnosti klienta lze nakonfigurovat pro `HubConnectionBuilder` typ (k dispozici v klientech rozhraní .NET a v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="633b4-684">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="633b4-685">Je také k dispozici v klientovi Java, ale `HttpHubConnectionBuilder` podtřídou je to, co obsahuje možnosti konfigurace tvůrce a také na `HubConnection` sebe sama.</span><span class="sxs-lookup"><span data-stu-id="633b4-685">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="633b4-686">Konfigurovat protokolování</span><span class="sxs-lookup"><span data-stu-id="633b4-686">Configure logging</span></span>

<span data-ttu-id="633b4-687">Protokolování je konfigurováno v klientovi .NET pomocí `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="633b4-687">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="633b4-688">Zprostředkovatelé protokolování a filtry je možné zaregistrovat stejným způsobem jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="633b4-688">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="633b4-689">Další informace najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="633b4-689">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="633b4-690">Aby bylo možné registrovat poskytovatele protokolování, je nutné nainstalovat potřebné balíčky.</span><span class="sxs-lookup"><span data-stu-id="633b4-690">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="633b4-691">Úplný seznam najdete v části [předdefinovaná zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="633b4-691">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="633b4-692">Chcete-li například povolit protokolování konzoly, nainstalujte `Microsoft.Extensions.Logging.Console` balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="633b4-692">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="633b4-693">Zavolejte `AddConsole` metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="633b4-693">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="633b4-694">V klientovi jazyka JavaScript existuje podobná `configureLogging` metoda.</span><span class="sxs-lookup"><span data-stu-id="633b4-694">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="633b4-695">Zadejte `LogLevel` hodnotu, která určuje minimální úroveň zpráv protokolu, které mají být vyprodukovány.</span><span class="sxs-lookup"><span data-stu-id="633b4-695">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="633b4-696">Protokoly se zapisují do okna konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="633b4-696">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="633b4-697">Místo `LogLevel` hodnoty můžete zadat také `string` hodnotu představující název úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="633b4-697">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="633b4-698">To je užitečné při konfiguraci SignalR protokolování v prostředích, kde nemáte přístup k `LogLevel` konstantám.</span><span class="sxs-lookup"><span data-stu-id="633b4-698">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="633b4-699">V následující tabulce jsou uvedeny dostupné úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="633b4-699">The following table lists the available log levels.</span></span> <span data-ttu-id="633b4-700">Hodnota, kterou zadáte, `configureLogging` nastaví **minimální** úroveň protokolu, která se bude protokolovat.</span><span class="sxs-lookup"><span data-stu-id="633b4-700">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="633b4-701">Zprávy zaznamenané na této úrovni **nebo úrovně uvedené za ní v tabulce**budou protokolovány.</span><span class="sxs-lookup"><span data-stu-id="633b4-701">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="633b4-702">Řetězec</span><span class="sxs-lookup"><span data-stu-id="633b4-702">String</span></span>                      | <span data-ttu-id="633b4-703">LogLevel</span><span class="sxs-lookup"><span data-stu-id="633b4-703">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="633b4-704">`info` **nebo** `information`</span><span class="sxs-lookup"><span data-stu-id="633b4-704">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="633b4-705">`warn` **nebo** `warning`</span><span class="sxs-lookup"><span data-stu-id="633b4-705">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="633b4-706">Chcete-li protokolování zcela zakázat, zadejte `signalR.LogLevel.None` v `configureLogging` metodě.</span><span class="sxs-lookup"><span data-stu-id="633b4-706">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="633b4-707">Další informace o protokolování naleznete v dokumentaci k [ SignalR diagnostice](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="633b4-707">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="633b4-708">SignalRKlient Java používá k protokolování knihovnu [SLF4J](https://www.slf4j.org/) .</span><span class="sxs-lookup"><span data-stu-id="633b4-708">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="633b4-709">Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí.</span><span class="sxs-lookup"><span data-stu-id="633b4-709">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="633b4-710">Následující fragment kódu ukazuje, jak používat `java.util.logging` s SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="633b4-710">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="633b4-711">Pokud ve svých závislostech nenakonfigurujete protokolování, SLF4J načte výchozí protokolovací nástroj No-operation s následující zprávou upozornění:</span><span class="sxs-lookup"><span data-stu-id="633b4-711">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="633b4-712">Tuto možnost lze bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="633b4-712">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="633b4-713">Konfigurace povolených přenosů</span><span class="sxs-lookup"><span data-stu-id="633b4-713">Configure allowed transports</span></span>

<span data-ttu-id="633b4-714">Přenosy používané nástrojem SignalR lze konfigurovat ve `WithUrl` volání ( `withUrl` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="633b4-714">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="633b4-715">Bitové nebo hodnoty `HttpTransportType` lze použít k omezení klienta na používání pouze zadaných přenosů.</span><span class="sxs-lookup"><span data-stu-id="633b4-715">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="633b4-716">Ve výchozím nastavení jsou povolené všechny přenosy.</span><span class="sxs-lookup"><span data-stu-id="633b4-716">All transports are enabled by default.</span></span>

<span data-ttu-id="633b4-717">Pokud třeba chcete zakázat přenos událostí odeslaných serverem, ale povolit WebSockets a dlouhá připojení s dotazem:</span><span class="sxs-lookup"><span data-stu-id="633b4-717">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="633b4-718">V klientu jazyka JavaScript jsou přenosy konfigurovány nastavením `transport` pole v objektu Options, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="633b4-718">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="633b4-719">V této verzi je jediným dostupným přenosem klientský WebSocket v jazyce Java.</span><span class="sxs-lookup"><span data-stu-id="633b4-719">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="633b4-720">V klientovi Java se přenos vybere s `withTransport` metodou na `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="633b4-720">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="633b4-721">Klient Java standardně používá přenos pomocí protokolu WebSockets.</span><span class="sxs-lookup"><span data-stu-id="633b4-721">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="633b4-722">SignalRKlient Java ještě nepodporuje přenosovou zálohu.</span><span class="sxs-lookup"><span data-stu-id="633b4-722">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="633b4-723">Konfigurace ověřování nosiče</span><span class="sxs-lookup"><span data-stu-id="633b4-723">Configure bearer authentication</span></span>

<span data-ttu-id="633b4-724">K poskytnutí ověřovacích dat spolu s SignalR požadavky použijte `AccessTokenProvider` možnost ( `accessTokenFactory` v jazyce JavaScript) k určení funkce, která vrací požadovaný přístupový token.</span><span class="sxs-lookup"><span data-stu-id="633b4-724">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="633b4-725">V klientovi .NET se tento přístupový token předává jako token ověřování HTTP "Bearer" (s použitím `Authorization` hlavičky s typem `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="633b4-725">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="633b4-726">V klientu jazyka JavaScript se přístupový token používá jako nosný token, **s výjimkou** případů, kdy rozhraní API prohlížeče omezuje možnost použít hlavičky (konkrétně v požadavcích na události odeslané serverem a objekty WebSockets).</span><span class="sxs-lookup"><span data-stu-id="633b4-726">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="633b4-727">V těchto případech je přístupový token k dispozici jako hodnota řetězce dotazu `access_token` .</span><span class="sxs-lookup"><span data-stu-id="633b4-727">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="633b4-728">V klientu .NET `AccessTokenProvider` lze možnost zadat pomocí delegáta možnosti v `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="633b4-728">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="633b4-729">V klientovi JavaScriptu je přístupový token nakonfigurovaný nastavením `accessTokenFactory` pole v objektu Options v `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="633b4-729">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="633b4-730">V SignalR klientovi Java můžete nakonfigurovat nosný token, který se bude používat pro ověřování, a to poskytnutím továrny přístupového tokenu [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="633b4-730">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="633b4-731">Pomocí [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) můžete zadat [RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="633b4-731">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="633b4-732">Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.</span><span class="sxs-lookup"><span data-stu-id="633b4-732">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="633b4-733">Konfigurace možností timeout a Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="633b4-733">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="633b4-734">Další možnosti konfigurace časového limitu a chování funkce Keep-Alive jsou k dispozici na `HubConnection` samotném objektu:</span><span class="sxs-lookup"><span data-stu-id="633b4-734">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="633b4-735">.NET</span><span class="sxs-lookup"><span data-stu-id="633b4-735">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="633b4-736">Možnost</span><span class="sxs-lookup"><span data-stu-id="633b4-736">Option</span></span> | <span data-ttu-id="633b4-737">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-737">Default value</span></span> | <span data-ttu-id="633b4-738">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-738">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="633b4-739">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="633b4-739">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="633b4-740">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="633b4-740">Timeout for server activity.</span></span> <span data-ttu-id="633b4-741">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí `Closed` událost ( `onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="633b4-741">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="633b4-742">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="633b4-742">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="633b4-743">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="633b4-743">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="633b4-744">15 sekund</span><span class="sxs-lookup"><span data-stu-id="633b4-744">15 seconds</span></span> | <span data-ttu-id="633b4-745">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="633b4-745">Timeout for initial server handshake.</span></span> <span data-ttu-id="633b4-746">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a spustí `Closed` událost ( `onclose` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="633b4-746">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="633b4-747">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="633b4-747">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="633b4-748">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="633b4-748">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="633b4-749">15 sekund</span><span class="sxs-lookup"><span data-stu-id="633b4-749">15 seconds</span></span> | <span data-ttu-id="633b4-750">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="633b4-750">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="633b4-751">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="633b4-751">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="633b4-752">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="633b4-752">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="633b4-753">V klientovi .NET jsou hodnoty časového limitu zadány jako `TimeSpan` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="633b4-753">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="633b4-754">JavaScript</span><span class="sxs-lookup"><span data-stu-id="633b4-754">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="633b4-755">Možnost</span><span class="sxs-lookup"><span data-stu-id="633b4-755">Option</span></span> | <span data-ttu-id="633b4-756">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-756">Default value</span></span> | <span data-ttu-id="633b4-757">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-757">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="633b4-758">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="633b4-758">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="633b4-759">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="633b4-759">Timeout for server activity.</span></span> <span data-ttu-id="633b4-760">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onclose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="633b4-760">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="633b4-761">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="633b4-761">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="633b4-762">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="633b4-762">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="633b4-763">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="633b4-763">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="633b4-764">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="633b4-764">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="633b4-765">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="633b4-765">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="633b4-766">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="633b4-766">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="633b4-767">Java</span><span class="sxs-lookup"><span data-stu-id="633b4-767">Java</span></span>](#tab/java)

| <span data-ttu-id="633b4-768">Možnost</span><span class="sxs-lookup"><span data-stu-id="633b4-768">Option</span></span> | <span data-ttu-id="633b4-769">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-769">Default value</span></span> | <span data-ttu-id="633b4-770">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-770">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="633b4-771">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="633b4-771">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="633b4-772">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="633b4-772">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="633b4-773">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="633b4-773">Timeout for server activity.</span></span> <span data-ttu-id="633b4-774">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onClose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="633b4-774">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="633b4-775">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="633b4-775">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="633b4-776">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="633b4-776">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="633b4-777">15 sekund</span><span class="sxs-lookup"><span data-stu-id="633b4-777">15 seconds</span></span> | <span data-ttu-id="633b4-778">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="633b4-778">Timeout for initial server handshake.</span></span> <span data-ttu-id="633b4-779">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje `onClose` událost.</span><span class="sxs-lookup"><span data-stu-id="633b4-779">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="633b4-780">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="633b4-780">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="633b4-781">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="633b4-781">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="633b4-782">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="633b4-782">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="633b4-783">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="633b4-783">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="633b4-784">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="633b4-784">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="633b4-785">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="633b4-785">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="633b4-786">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="633b4-786">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="633b4-787">Konfigurace dalších možností</span><span class="sxs-lookup"><span data-stu-id="633b4-787">Configure additional options</span></span>

<span data-ttu-id="633b4-788">Další možnosti lze konfigurovat v `WithUrl` `withUrl` metodě (v jazyce JavaScript) na `HubConnectionBuilder` různých konfiguračních rozhraních API v `HttpHubConnectionBuilder` klientovi Java:</span><span class="sxs-lookup"><span data-stu-id="633b4-788">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="633b4-789">.NET</span><span class="sxs-lookup"><span data-stu-id="633b4-789">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="633b4-790">Možnost .NET</span><span class="sxs-lookup"><span data-stu-id="633b4-790">.NET Option</span></span> |  <span data-ttu-id="633b4-791">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-791">Default value</span></span> | <span data-ttu-id="633b4-792">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-792">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="633b4-793">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="633b4-793">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="633b4-794">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="633b4-794">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="633b4-795">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="633b4-795">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="633b4-796">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="633b4-796">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="633b4-797">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="633b4-797">Empty</span></span> | <span data-ttu-id="633b4-798">Kolekce certifikátů TLS pro odeslání na požadavky na ověření.</span><span class="sxs-lookup"><span data-stu-id="633b4-798">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="633b4-799">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="633b4-799">Empty</span></span> | <span data-ttu-id="633b4-800">Kolekce HTTP cookie s pro odeslání s každou žádostí http</span><span class="sxs-lookup"><span data-stu-id="633b4-800">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="633b4-801">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="633b4-801">Empty</span></span> | <span data-ttu-id="633b4-802">Přihlašovací údaje, které se mají poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="633b4-802">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="633b4-803">5 sekund</span><span class="sxs-lookup"><span data-stu-id="633b4-803">5 seconds</span></span> | <span data-ttu-id="633b4-804">Jenom objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="633b4-804">WebSockets only.</span></span> <span data-ttu-id="633b4-805">Maximální doba, po jejímž uplynutí bude klient čekat na ukončení žádosti o uzavření.</span><span class="sxs-lookup"><span data-stu-id="633b4-805">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="633b4-806">Pokud server nepotvrdí zavření v této době, klient se odpojí.</span><span class="sxs-lookup"><span data-stu-id="633b4-806">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="633b4-807">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="633b4-807">Empty</span></span> | <span data-ttu-id="633b4-808">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="633b4-808">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="633b4-809">Delegát, který lze použít ke konfiguraci nebo nahrazení `HttpMessageHandler` používaného k odeslání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="633b4-809">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="633b4-810">Nepoužívá se pro připojení pomocí protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="633b4-810">Not used for WebSocket connections.</span></span> <span data-ttu-id="633b4-811">Tento delegát musí vracet hodnotu, která není null, a obdrží výchozí hodnotu jako parametr.</span><span class="sxs-lookup"><span data-stu-id="633b4-811">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="633b4-812">Buď změňte nastavení u této výchozí hodnoty a vraťte je, nebo vraťte novou `HttpMessageHandler` instanci.</span><span class="sxs-lookup"><span data-stu-id="633b4-812">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="633b4-813">**Při nahrazování obslužné rutiny Nezapomeňte zkopírovat nastavení, která chcete zachovat od poskytnuté obslužné rutiny, jinak konfigurované možnosti (například Cookie s a záhlaví) nebudou použity pro novou obslužnou rutinu.**</span><span class="sxs-lookup"><span data-stu-id="633b4-813">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="633b4-814">Proxy server HTTP, který se má použít při odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="633b4-814">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="633b4-815">Nastavte tuto logickou hodnotu pro odeslání výchozích přihlašovacích údajů pro požadavky HTTP a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="633b4-815">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="633b4-816">To umožňuje použití ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="633b4-816">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="633b4-817">Delegát, který lze použít ke konfiguraci dalších možností protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="633b4-817">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="633b4-818">Přijímá instanci [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , která se dá použít ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="633b4-818">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="633b4-819">JavaScript</span><span class="sxs-lookup"><span data-stu-id="633b4-819">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="633b4-820">Možnost JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="633b4-820">JavaScript Option</span></span> | <span data-ttu-id="633b4-821">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-821">Default Value</span></span> | <span data-ttu-id="633b4-822">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-822">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="633b4-823">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="633b4-823">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="633b4-824">Nastavte na `true` protokolování bajtů/znaků zpráv odesílaných a přijímaných klientem.</span><span class="sxs-lookup"><span data-stu-id="633b4-824">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="633b4-825">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="633b4-825">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="633b4-826">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="633b4-826">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="633b4-827">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="633b4-827">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="633b4-828">Java</span><span class="sxs-lookup"><span data-stu-id="633b4-828">Java</span></span>](#tab/java)

| <span data-ttu-id="633b4-829">Možnost jazyka Java</span><span class="sxs-lookup"><span data-stu-id="633b4-829">Java Option</span></span> | <span data-ttu-id="633b4-830">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-830">Default Value</span></span> | <span data-ttu-id="633b4-831">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-831">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="633b4-832">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="633b4-832">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="633b4-833">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="633b4-833">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="633b4-834">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="633b4-834">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="633b4-835">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="633b4-835">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="633b4-836">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="633b4-836">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="633b4-837">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="633b4-837">Empty</span></span> | <span data-ttu-id="633b4-838">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="633b4-838">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="633b4-839">V klientu .NET můžete tyto možnosti upravit pomocí delegáta možností, který je k dispozici pro `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="633b4-839">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="633b4-840">V klientovi JavaScriptu lze tyto možnosti poskytnout v objektu jazyka JavaScript, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="633b4-840">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="633b4-841">V klientovi Java lze tyto možnosti nakonfigurovat s metodami `HttpHubConnectionBuilder` vrácenými z `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="633b4-841">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="633b4-842">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="633b4-842">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="633b4-843">Možnosti serializace JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="633b4-843">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="633b4-844">ASP.NET Core SignalR podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="633b4-844">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="633b4-845">Každý protokol má možnosti konfigurace serializace.</span><span class="sxs-lookup"><span data-stu-id="633b4-845">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="633b4-846">Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , kterou lze přidat po [Přidání SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) do `Startup.ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="633b4-846">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="633b4-847">`AddJsonProtocol`Metoda přebírá delegáta, který přijímá `options` objekt.</span><span class="sxs-lookup"><span data-stu-id="633b4-847">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="633b4-848">Vlastnost [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) tohoto objektu je `JsonSerializerSettings` objekt JSON.NET, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="633b4-848">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="633b4-849">Další informace najdete v [dokumentaci k JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="633b4-849">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="633b4-850">Chcete-li například nakonfigurovat serializátor pro použití názvů vlastností "PascalCase" namísto výchozích názvů "camelCase", použijte následující kód v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="633b4-850">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="633b4-851">V klientovi .NET `AddJsonProtocol` existuje stejná rozšiřující metoda v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="633b4-851">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="633b4-852">`Microsoft.Extensions.DependencyInjection`Obor názvů musí být importován, aby bylo možné přeložit metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="633b4-852">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="633b4-853">V tuto chvíli není možné konfigurovat serializaci JSON v klientovi jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="633b4-853">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="633b4-854">Možnosti serializace MessagePack</span><span class="sxs-lookup"><span data-stu-id="633b4-854">MessagePack serialization options</span></span>

<span data-ttu-id="633b4-855">Serializaci MessagePack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="633b4-855">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="633b4-856">Další podrobnosti najdete [v SignalR tématu MessagePack](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="633b4-856">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="633b4-857">V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi v jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="633b4-857">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="633b4-858">Konfigurovat možnosti serveru</span><span class="sxs-lookup"><span data-stu-id="633b4-858">Configure server options</span></span>

<span data-ttu-id="633b4-859">Následující tabulka popisuje možnosti konfigurace SignalR Center:</span><span class="sxs-lookup"><span data-stu-id="633b4-859">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="633b4-860">Možnost</span><span class="sxs-lookup"><span data-stu-id="633b4-860">Option</span></span> | <span data-ttu-id="633b4-861">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-861">Default Value</span></span> | <span data-ttu-id="633b4-862">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-862">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="633b4-863">30 sekund</span><span class="sxs-lookup"><span data-stu-id="633b4-863">30 seconds</span></span> | <span data-ttu-id="633b4-864">Server bude předpokládat, že je klient odpojený, pokud v tomto intervalu neobdržel zprávu (včetně Keep-Alive).</span><span class="sxs-lookup"><span data-stu-id="633b4-864">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="633b4-865">Může trvat delší dobu, než je tento časový limit, aby bylo možné klienta ve skutečnosti označit jako odpojený, protože to je implementováno.</span><span class="sxs-lookup"><span data-stu-id="633b4-865">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="633b4-866">Doporučená hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="633b4-866">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="633b4-867">15 sekund</span><span class="sxs-lookup"><span data-stu-id="633b4-867">15 seconds</span></span> | <span data-ttu-id="633b4-868">Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře.</span><span class="sxs-lookup"><span data-stu-id="633b4-868">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="633b4-869">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="633b4-869">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="633b4-870">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="633b4-870">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="633b4-871">15 sekund</span><span class="sxs-lookup"><span data-stu-id="633b4-871">15 seconds</span></span> | <span data-ttu-id="633b4-872">Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="633b4-872">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="633b4-873">Při změně `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení změňte nastavení v klientovi.</span><span class="sxs-lookup"><span data-stu-id="633b4-873">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="633b4-874">Doporučená `ServerTimeout` / `serverTimeoutInMilliseconds` hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="633b4-874">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="633b4-875">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="633b4-875">All installed protocols</span></span> | <span data-ttu-id="633b4-876">Protokoly podporované tímto rozbočovačem</span><span class="sxs-lookup"><span data-stu-id="633b4-876">Protocols supported by this hub.</span></span> <span data-ttu-id="633b4-877">Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly.</span><span class="sxs-lookup"><span data-stu-id="633b4-877">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="633b4-878">Pokud se `true` do klientů vrátí podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="633b4-878">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="633b4-879">Výchozí hodnota je `false` , protože tyto zprávy o výjimce můžou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="633b4-879">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="633b4-880">Možnosti lze nakonfigurovat pro všechna centra tím, že poskytnete možnosti delegáta pro `AddSignalR` volání v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="633b4-880">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="633b4-881">Možnosti pro jeden rozbočovač přepíší globální možnosti poskytované v `AddSignalR` a můžou být nakonfigurované pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="633b4-881">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="633b4-882">Rozšířené možnosti konfigurace protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="633b4-882">Advanced HTTP configuration options</span></span>

<span data-ttu-id="633b4-883">Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="633b4-883">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="633b4-884">Tyto možnosti jsou nakonfigurovány předáním delegáta [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="633b4-884">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="633b4-885">Následující tabulka popisuje možnosti konfigurace SignalR rozšířených možností protokolu HTTP v ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="633b4-885">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="633b4-886">Možnost</span><span class="sxs-lookup"><span data-stu-id="633b4-886">Option</span></span> | <span data-ttu-id="633b4-887">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-887">Default Value</span></span> | <span data-ttu-id="633b4-888">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-888">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="633b4-889">32 KB</span><span class="sxs-lookup"><span data-stu-id="633b4-889">32 KB</span></span> | <span data-ttu-id="633b4-890">Maximální počet bajtů přijatých od klienta, které jsou vyrovnávací paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="633b4-890">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="633b4-891">Zvýšením této hodnoty umožníte serveru přijímat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="633b4-891">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="633b4-892">Data se automaticky shromažďují z `Authorize` atributů použitých pro třídu centra.</span><span class="sxs-lookup"><span data-stu-id="633b4-892">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="633b4-893">Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru</span><span class="sxs-lookup"><span data-stu-id="633b4-893">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="633b4-894">32 KB</span><span class="sxs-lookup"><span data-stu-id="633b4-894">32 KB</span></span> | <span data-ttu-id="633b4-895">Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="633b4-895">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="633b4-896">Zvýšením této hodnoty umožníte serveru odesílat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="633b4-896">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="633b4-897">Všechny přenosy jsou povolené.</span><span class="sxs-lookup"><span data-stu-id="633b4-897">All Transports are enabled.</span></span> | <span data-ttu-id="633b4-898">Bitové příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenos, který může klient použít pro připojení.</span><span class="sxs-lookup"><span data-stu-id="633b4-898">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="633b4-899">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="633b4-899">See below.</span></span> | <span data-ttu-id="633b4-900">Další možnosti specifické pro přenos dlouhého cyklického dotazování.</span><span class="sxs-lookup"><span data-stu-id="633b4-900">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="633b4-901">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="633b4-901">See below.</span></span> | <span data-ttu-id="633b4-902">Další možnosti specifické pro přenos pomocí protokolu WebSockets</span><span class="sxs-lookup"><span data-stu-id="633b4-902">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="633b4-903">Přenos dlouhého cyklického dotazování má další možnosti, které je možné konfigurovat pomocí `LongPolling` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="633b4-903">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="633b4-904">Možnost</span><span class="sxs-lookup"><span data-stu-id="633b4-904">Option</span></span> | <span data-ttu-id="633b4-905">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-905">Default Value</span></span> | <span data-ttu-id="633b4-906">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-906">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="633b4-907">90 sekund</span><span class="sxs-lookup"><span data-stu-id="633b4-907">90 seconds</span></span> | <span data-ttu-id="633b4-908">Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jedné žádosti o cyklické dotazování.</span><span class="sxs-lookup"><span data-stu-id="633b4-908">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="633b4-909">Snížení této hodnoty způsobí, že klient bude vydávat nové požadavky na dotaz častěji.</span><span class="sxs-lookup"><span data-stu-id="633b4-909">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="633b4-910">Přenos protokolu WebSocket má další možnosti, které je možné konfigurovat pomocí `WebSockets` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="633b4-910">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="633b4-911">Možnost</span><span class="sxs-lookup"><span data-stu-id="633b4-911">Option</span></span> | <span data-ttu-id="633b4-912">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-912">Default Value</span></span> | <span data-ttu-id="633b4-913">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-913">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="633b4-914">5 sekund</span><span class="sxs-lookup"><span data-stu-id="633b4-914">5 seconds</span></span> | <span data-ttu-id="633b4-915">Pokud se po ukončení serveru aplikace v tomto časovém intervalu nepovede zavřít, připojení se ukončí.</span><span class="sxs-lookup"><span data-stu-id="633b4-915">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="633b4-916">Delegát, který lze použít k nastavení `Sec-WebSocket-Protocol` záhlaví na vlastní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="633b4-916">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="633b4-917">Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že se vrátí požadovaná hodnota.</span><span class="sxs-lookup"><span data-stu-id="633b4-917">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="633b4-918">Konfigurace možností klienta</span><span class="sxs-lookup"><span data-stu-id="633b4-918">Configure client options</span></span>

<span data-ttu-id="633b4-919">Možnosti klienta lze nakonfigurovat pro `HubConnectionBuilder` typ (k dispozici v klientech rozhraní .NET a v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="633b4-919">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="633b4-920">Je také k dispozici v klientovi Java, ale `HttpHubConnectionBuilder` podtřídou je to, co obsahuje možnosti konfigurace tvůrce a také na `HubConnection` sebe sama.</span><span class="sxs-lookup"><span data-stu-id="633b4-920">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="633b4-921">Konfigurovat protokolování</span><span class="sxs-lookup"><span data-stu-id="633b4-921">Configure logging</span></span>

<span data-ttu-id="633b4-922">Protokolování je konfigurováno v klientovi .NET pomocí `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="633b4-922">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="633b4-923">Zprostředkovatelé protokolování a filtry je možné zaregistrovat stejným způsobem jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="633b4-923">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="633b4-924">Další informace najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="633b4-924">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="633b4-925">Aby bylo možné registrovat poskytovatele protokolování, je nutné nainstalovat potřebné balíčky.</span><span class="sxs-lookup"><span data-stu-id="633b4-925">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="633b4-926">Úplný seznam najdete v části [předdefinovaná zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="633b4-926">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="633b4-927">Chcete-li například povolit protokolování konzoly, nainstalujte `Microsoft.Extensions.Logging.Console` balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="633b4-927">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="633b4-928">Zavolejte `AddConsole` metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="633b4-928">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="633b4-929">V klientovi jazyka JavaScript existuje podobná `configureLogging` metoda.</span><span class="sxs-lookup"><span data-stu-id="633b4-929">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="633b4-930">Zadejte `LogLevel` hodnotu, která určuje minimální úroveň zpráv protokolu, které mají být vyprodukovány.</span><span class="sxs-lookup"><span data-stu-id="633b4-930">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="633b4-931">Protokoly se zapisují do okna konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="633b4-931">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="633b4-932">Chcete-li protokolování zcela zakázat, zadejte `signalR.LogLevel.None` v `configureLogging` metodě.</span><span class="sxs-lookup"><span data-stu-id="633b4-932">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="633b4-933">Další informace o protokolování naleznete v dokumentaci k [ SignalR diagnostice](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="633b4-933">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="633b4-934">SignalRKlient Java používá k protokolování knihovnu [SLF4J](https://www.slf4j.org/) .</span><span class="sxs-lookup"><span data-stu-id="633b4-934">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="633b4-935">Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí.</span><span class="sxs-lookup"><span data-stu-id="633b4-935">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="633b4-936">Následující fragment kódu ukazuje, jak používat `java.util.logging` s SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="633b4-936">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="633b4-937">Pokud ve svých závislostech nenakonfigurujete protokolování, SLF4J načte výchozí protokolovací nástroj No-operation s následující zprávou upozornění:</span><span class="sxs-lookup"><span data-stu-id="633b4-937">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="633b4-938">Tuto možnost lze bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="633b4-938">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="633b4-939">Konfigurace povolených přenosů</span><span class="sxs-lookup"><span data-stu-id="633b4-939">Configure allowed transports</span></span>

<span data-ttu-id="633b4-940">Přenosy používané nástrojem SignalR lze konfigurovat ve `WithUrl` volání ( `withUrl` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="633b4-940">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="633b4-941">Bitové nebo hodnoty `HttpTransportType` lze použít k omezení klienta na používání pouze zadaných přenosů.</span><span class="sxs-lookup"><span data-stu-id="633b4-941">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="633b4-942">Ve výchozím nastavení jsou povolené všechny přenosy.</span><span class="sxs-lookup"><span data-stu-id="633b4-942">All transports are enabled by default.</span></span>

<span data-ttu-id="633b4-943">Pokud třeba chcete zakázat přenos událostí odeslaných serverem, ale povolit WebSockets a dlouhá připojení s dotazem:</span><span class="sxs-lookup"><span data-stu-id="633b4-943">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="633b4-944">V klientu jazyka JavaScript jsou přenosy konfigurovány nastavením `transport` pole v objektu Options, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="633b4-944">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="633b4-945">V této verzi je jediným dostupným přenosem klientský WebSocket v jazyce Java.</span><span class="sxs-lookup"><span data-stu-id="633b4-945">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="633b4-946">Konfigurace ověřování nosiče</span><span class="sxs-lookup"><span data-stu-id="633b4-946">Configure bearer authentication</span></span>

<span data-ttu-id="633b4-947">K poskytnutí ověřovacích dat spolu s SignalR požadavky použijte `AccessTokenProvider` možnost ( `accessTokenFactory` v jazyce JavaScript) k určení funkce, která vrací požadovaný přístupový token.</span><span class="sxs-lookup"><span data-stu-id="633b4-947">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="633b4-948">V klientovi .NET se tento přístupový token předává jako token ověřování HTTP "Bearer" (s použitím `Authorization` hlavičky s typem `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="633b4-948">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="633b4-949">V klientu jazyka JavaScript se přístupový token používá jako nosný token, **s výjimkou** případů, kdy rozhraní API prohlížeče omezuje možnost použít hlavičky (konkrétně v požadavcích na události odeslané serverem a objekty WebSockets).</span><span class="sxs-lookup"><span data-stu-id="633b4-949">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="633b4-950">V těchto případech je přístupový token k dispozici jako hodnota řetězce dotazu `access_token` .</span><span class="sxs-lookup"><span data-stu-id="633b4-950">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="633b4-951">V klientu .NET `AccessTokenProvider` lze možnost zadat pomocí delegáta možnosti v `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="633b4-951">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="633b4-952">V klientovi JavaScriptu je přístupový token nakonfigurovaný nastavením `accessTokenFactory` pole v objektu Options v `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="633b4-952">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="633b4-953">V SignalR klientovi Java můžete nakonfigurovat nosný token, který se bude používat pro ověřování, a to poskytnutím továrny přístupového tokenu [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="633b4-953">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="633b4-954">Pomocí [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) můžete zadat [RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="633b4-954">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="633b4-955">Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.</span><span class="sxs-lookup"><span data-stu-id="633b4-955">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="633b4-956">Konfigurace možností timeout a Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="633b4-956">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="633b4-957">Další možnosti konfigurace časového limitu a chování funkce Keep-Alive jsou k dispozici na `HubConnection` samotném objektu:</span><span class="sxs-lookup"><span data-stu-id="633b4-957">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="633b4-958">.NET</span><span class="sxs-lookup"><span data-stu-id="633b4-958">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="633b4-959">Možnost</span><span class="sxs-lookup"><span data-stu-id="633b4-959">Option</span></span> | <span data-ttu-id="633b4-960">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-960">Default value</span></span> | <span data-ttu-id="633b4-961">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-961">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="633b4-962">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="633b4-962">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="633b4-963">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="633b4-963">Timeout for server activity.</span></span> <span data-ttu-id="633b4-964">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí `Closed` událost ( `onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="633b4-964">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="633b4-965">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="633b4-965">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="633b4-966">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="633b4-966">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="633b4-967">15 sekund</span><span class="sxs-lookup"><span data-stu-id="633b4-967">15 seconds</span></span> | <span data-ttu-id="633b4-968">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="633b4-968">Timeout for initial server handshake.</span></span> <span data-ttu-id="633b4-969">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a spustí `Closed` událost ( `onclose` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="633b4-969">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="633b4-970">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="633b4-970">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="633b4-971">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="633b4-971">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="633b4-972">15 sekund</span><span class="sxs-lookup"><span data-stu-id="633b4-972">15 seconds</span></span> | <span data-ttu-id="633b4-973">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="633b4-973">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="633b4-974">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="633b4-974">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="633b4-975">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="633b4-975">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="633b4-976">V klientovi .NET jsou hodnoty časového limitu zadány jako `TimeSpan` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="633b4-976">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="633b4-977">JavaScript</span><span class="sxs-lookup"><span data-stu-id="633b4-977">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="633b4-978">Možnost</span><span class="sxs-lookup"><span data-stu-id="633b4-978">Option</span></span> | <span data-ttu-id="633b4-979">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-979">Default value</span></span> | <span data-ttu-id="633b4-980">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-980">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="633b4-981">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="633b4-981">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="633b4-982">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="633b4-982">Timeout for server activity.</span></span> <span data-ttu-id="633b4-983">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onclose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="633b4-983">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="633b4-984">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="633b4-984">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="633b4-985">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="633b4-985">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="633b4-986">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="633b4-986">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="633b4-987">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="633b4-987">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="633b4-988">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="633b4-988">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="633b4-989">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="633b4-989">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="633b4-990">Java</span><span class="sxs-lookup"><span data-stu-id="633b4-990">Java</span></span>](#tab/java)

| <span data-ttu-id="633b4-991">Možnost</span><span class="sxs-lookup"><span data-stu-id="633b4-991">Option</span></span> | <span data-ttu-id="633b4-992">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-992">Default value</span></span> | <span data-ttu-id="633b4-993">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-993">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="633b4-994">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="633b4-994">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="633b4-995">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="633b4-995">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="633b4-996">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="633b4-996">Timeout for server activity.</span></span> <span data-ttu-id="633b4-997">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onClose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="633b4-997">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="633b4-998">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="633b4-998">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="633b4-999">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="633b4-999">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="633b4-1000">15 sekund</span><span class="sxs-lookup"><span data-stu-id="633b4-1000">15 seconds</span></span> | <span data-ttu-id="633b4-1001">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="633b4-1001">Timeout for initial server handshake.</span></span> <span data-ttu-id="633b4-1002">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje `onClose` událost.</span><span class="sxs-lookup"><span data-stu-id="633b4-1002">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="633b4-1003">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="633b4-1003">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="633b4-1004">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="633b4-1004">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="633b4-1005">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="633b4-1005">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="633b4-1006">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="633b4-1006">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="633b4-1007">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="633b4-1007">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="633b4-1008">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="633b4-1008">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="633b4-1009">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="633b4-1009">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="633b4-1010">Konfigurace dalších možností</span><span class="sxs-lookup"><span data-stu-id="633b4-1010">Configure additional options</span></span>

<span data-ttu-id="633b4-1011">Další možnosti lze konfigurovat v `WithUrl` `withUrl` metodě (v jazyce JavaScript) na `HubConnectionBuilder` různých konfiguračních rozhraních API v `HttpHubConnectionBuilder` klientovi Java:</span><span class="sxs-lookup"><span data-stu-id="633b4-1011">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="633b4-1012">.NET</span><span class="sxs-lookup"><span data-stu-id="633b4-1012">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="633b4-1013">Možnost .NET</span><span class="sxs-lookup"><span data-stu-id="633b4-1013">.NET Option</span></span> |  <span data-ttu-id="633b4-1014">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-1014">Default value</span></span> | <span data-ttu-id="633b4-1015">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-1015">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="633b4-1016">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="633b4-1016">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="633b4-1017">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="633b4-1017">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="633b4-1018">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="633b4-1018">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="633b4-1019">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="633b4-1019">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="633b4-1020">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="633b4-1020">Empty</span></span> | <span data-ttu-id="633b4-1021">Kolekce certifikátů TLS pro odeslání na požadavky na ověření.</span><span class="sxs-lookup"><span data-stu-id="633b4-1021">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="633b4-1022">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="633b4-1022">Empty</span></span> | <span data-ttu-id="633b4-1023">Kolekce HTTP cookie s pro odeslání s každou žádostí http</span><span class="sxs-lookup"><span data-stu-id="633b4-1023">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="633b4-1024">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="633b4-1024">Empty</span></span> | <span data-ttu-id="633b4-1025">Přihlašovací údaje, které se mají poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="633b4-1025">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="633b4-1026">5 sekund</span><span class="sxs-lookup"><span data-stu-id="633b4-1026">5 seconds</span></span> | <span data-ttu-id="633b4-1027">Jenom objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="633b4-1027">WebSockets only.</span></span> <span data-ttu-id="633b4-1028">Maximální doba, po jejímž uplynutí bude klient čekat na ukončení žádosti o uzavření.</span><span class="sxs-lookup"><span data-stu-id="633b4-1028">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="633b4-1029">Pokud server nepotvrdí zavření v této době, klient se odpojí.</span><span class="sxs-lookup"><span data-stu-id="633b4-1029">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="633b4-1030">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="633b4-1030">Empty</span></span> | <span data-ttu-id="633b4-1031">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="633b4-1031">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="633b4-1032">Delegát, který lze použít ke konfiguraci nebo nahrazení `HttpMessageHandler` používaného k odeslání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="633b4-1032">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="633b4-1033">Nepoužívá se pro připojení pomocí protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="633b4-1033">Not used for WebSocket connections.</span></span> <span data-ttu-id="633b4-1034">Tento delegát musí vracet hodnotu, která není null, a obdrží výchozí hodnotu jako parametr.</span><span class="sxs-lookup"><span data-stu-id="633b4-1034">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="633b4-1035">Buď změňte nastavení u této výchozí hodnoty a vraťte je, nebo vraťte novou `HttpMessageHandler` instanci.</span><span class="sxs-lookup"><span data-stu-id="633b4-1035">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="633b4-1036">**Při nahrazování obslužné rutiny Nezapomeňte zkopírovat nastavení, která chcete zachovat od poskytnuté obslužné rutiny, jinak konfigurované možnosti (například Cookie s a záhlaví) nebudou použity pro novou obslužnou rutinu.**</span><span class="sxs-lookup"><span data-stu-id="633b4-1036">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="633b4-1037">Proxy server HTTP, který se má použít při odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="633b4-1037">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="633b4-1038">Nastavte tuto logickou hodnotu pro odeslání výchozích přihlašovacích údajů pro požadavky HTTP a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="633b4-1038">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="633b4-1039">To umožňuje použití ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="633b4-1039">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="633b4-1040">Delegát, který lze použít ke konfiguraci dalších možností protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="633b4-1040">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="633b4-1041">Přijímá instanci [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , která se dá použít ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="633b4-1041">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="633b4-1042">JavaScript</span><span class="sxs-lookup"><span data-stu-id="633b4-1042">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="633b4-1043">Možnost JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="633b4-1043">JavaScript Option</span></span> | <span data-ttu-id="633b4-1044">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-1044">Default Value</span></span> | <span data-ttu-id="633b4-1045">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-1045">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="633b4-1046">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="633b4-1046">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="633b4-1047">Nastavte na `true` protokolování bajtů/znaků zpráv odesílaných a přijímaných klientem.</span><span class="sxs-lookup"><span data-stu-id="633b4-1047">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="633b4-1048">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="633b4-1048">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="633b4-1049">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="633b4-1049">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="633b4-1050">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="633b4-1050">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="633b4-1051">Java</span><span class="sxs-lookup"><span data-stu-id="633b4-1051">Java</span></span>](#tab/java)

| <span data-ttu-id="633b4-1052">Možnost jazyka Java</span><span class="sxs-lookup"><span data-stu-id="633b4-1052">Java Option</span></span> | <span data-ttu-id="633b4-1053">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-1053">Default Value</span></span> | <span data-ttu-id="633b4-1054">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-1054">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="633b4-1055">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="633b4-1055">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="633b4-1056">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="633b4-1056">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="633b4-1057">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="633b4-1057">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="633b4-1058">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="633b4-1058">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="633b4-1059">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="633b4-1059">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="633b4-1060">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="633b4-1060">Empty</span></span> | <span data-ttu-id="633b4-1061">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="633b4-1061">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="633b4-1062">V klientu .NET můžete tyto možnosti upravit pomocí delegáta možností, který je k dispozici pro `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="633b4-1062">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="633b4-1063">V klientovi JavaScriptu lze tyto možnosti poskytnout v objektu jazyka JavaScript, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="633b4-1063">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="633b4-1064">V klientovi Java lze tyto možnosti nakonfigurovat s metodami `HttpHubConnectionBuilder` vrácenými z `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="633b4-1064">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="633b4-1065">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="633b4-1065">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="633b4-1066">Možnosti serializace JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="633b4-1066">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="633b4-1067">ASP.NET Core SignalR podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="633b4-1067">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="633b4-1068">Každý protokol má možnosti konfigurace serializace.</span><span class="sxs-lookup"><span data-stu-id="633b4-1068">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="633b4-1069">Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , kterou lze přidat po [Přidání SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) do `Startup.ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="633b4-1069">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="633b4-1070">`AddJsonProtocol`Metoda přebírá delegáta, který přijímá `options` objekt.</span><span class="sxs-lookup"><span data-stu-id="633b4-1070">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="633b4-1071">Vlastnost [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) tohoto objektu je `JsonSerializerSettings` objekt JSON.NET, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="633b4-1071">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="633b4-1072">Další informace najdete v [dokumentaci k JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="633b4-1072">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="633b4-1073">Chcete-li například nakonfigurovat serializátor pro použití názvů vlastností "PascalCase" namísto výchozích názvů "camelCase", použijte následující kód v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="633b4-1073">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="633b4-1074">V klientovi .NET `AddJsonProtocol` existuje stejná rozšiřující metoda v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="633b4-1074">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="633b4-1075">`Microsoft.Extensions.DependencyInjection`Obor názvů musí být importován, aby bylo možné přeložit metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="633b4-1075">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="633b4-1076">V tuto chvíli není možné konfigurovat serializaci JSON v klientovi jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="633b4-1076">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="633b4-1077">Možnosti serializace MessagePack</span><span class="sxs-lookup"><span data-stu-id="633b4-1077">MessagePack serialization options</span></span>

<span data-ttu-id="633b4-1078">Serializaci MessagePack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="633b4-1078">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="633b4-1079">Další podrobnosti najdete [v SignalR tématu MessagePack](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="633b4-1079">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="633b4-1080">V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi v jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="633b4-1080">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="633b4-1081">Konfigurovat možnosti serveru</span><span class="sxs-lookup"><span data-stu-id="633b4-1081">Configure server options</span></span>

<span data-ttu-id="633b4-1082">Následující tabulka popisuje možnosti konfigurace SignalR Center:</span><span class="sxs-lookup"><span data-stu-id="633b4-1082">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="633b4-1083">Možnost</span><span class="sxs-lookup"><span data-stu-id="633b4-1083">Option</span></span> | <span data-ttu-id="633b4-1084">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-1084">Default Value</span></span> | <span data-ttu-id="633b4-1085">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-1085">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="633b4-1086">15 sekund</span><span class="sxs-lookup"><span data-stu-id="633b4-1086">15 seconds</span></span> | <span data-ttu-id="633b4-1087">Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře.</span><span class="sxs-lookup"><span data-stu-id="633b4-1087">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="633b4-1088">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="633b4-1088">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="633b4-1089">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="633b4-1089">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="633b4-1090">15 sekund</span><span class="sxs-lookup"><span data-stu-id="633b4-1090">15 seconds</span></span> | <span data-ttu-id="633b4-1091">Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="633b4-1091">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="633b4-1092">Při změně `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení změňte nastavení v klientovi.</span><span class="sxs-lookup"><span data-stu-id="633b4-1092">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="633b4-1093">Doporučená `ServerTimeout` / `serverTimeoutInMilliseconds` hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="633b4-1093">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="633b4-1094">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="633b4-1094">All installed protocols</span></span> | <span data-ttu-id="633b4-1095">Protokoly podporované tímto rozbočovačem</span><span class="sxs-lookup"><span data-stu-id="633b4-1095">Protocols supported by this hub.</span></span> <span data-ttu-id="633b4-1096">Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly.</span><span class="sxs-lookup"><span data-stu-id="633b4-1096">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="633b4-1097">Pokud se `true` do klientů vrátí podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="633b4-1097">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="633b4-1098">Výchozí hodnota je `false` , protože tyto zprávy o výjimce můžou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="633b4-1098">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="633b4-1099">Možnosti lze nakonfigurovat pro všechna centra tím, že poskytnete možnosti delegáta pro `AddSignalR` volání v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="633b4-1099">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="633b4-1100">Možnosti pro jeden rozbočovač přepíší globální možnosti poskytované v `AddSignalR` a můžou být nakonfigurované pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="633b4-1100">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="633b4-1101">Rozšířené možnosti konfigurace protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="633b4-1101">Advanced HTTP configuration options</span></span>

<span data-ttu-id="633b4-1102">Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="633b4-1102">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="633b4-1103">Tyto možnosti jsou nakonfigurovány předáním delegáta [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="633b4-1103">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="633b4-1104">Následující tabulka popisuje možnosti konfigurace SignalR rozšířených možností protokolu HTTP v ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="633b4-1104">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="633b4-1105">Možnost</span><span class="sxs-lookup"><span data-stu-id="633b4-1105">Option</span></span> | <span data-ttu-id="633b4-1106">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-1106">Default Value</span></span> | <span data-ttu-id="633b4-1107">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-1107">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="633b4-1108">32 KB</span><span class="sxs-lookup"><span data-stu-id="633b4-1108">32 KB</span></span> | <span data-ttu-id="633b4-1109">Maximální počet bajtů přijatých od klienta, které jsou vyrovnávací paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="633b4-1109">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="633b4-1110">Zvýšením této hodnoty umožníte serveru přijímat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="633b4-1110">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="633b4-1111">Data se automaticky shromažďují z `Authorize` atributů použitých pro třídu centra.</span><span class="sxs-lookup"><span data-stu-id="633b4-1111">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="633b4-1112">Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru</span><span class="sxs-lookup"><span data-stu-id="633b4-1112">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="633b4-1113">32 KB</span><span class="sxs-lookup"><span data-stu-id="633b4-1113">32 KB</span></span> | <span data-ttu-id="633b4-1114">Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="633b4-1114">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="633b4-1115">Zvýšením této hodnoty umožníte serveru odesílat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="633b4-1115">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="633b4-1116">Všechny přenosy jsou povolené.</span><span class="sxs-lookup"><span data-stu-id="633b4-1116">All Transports are enabled.</span></span> | <span data-ttu-id="633b4-1117">Bitové příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenos, který může klient použít pro připojení.</span><span class="sxs-lookup"><span data-stu-id="633b4-1117">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="633b4-1118">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="633b4-1118">See below.</span></span> | <span data-ttu-id="633b4-1119">Další možnosti specifické pro přenos dlouhého cyklického dotazování.</span><span class="sxs-lookup"><span data-stu-id="633b4-1119">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="633b4-1120">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="633b4-1120">See below.</span></span> | <span data-ttu-id="633b4-1121">Další možnosti specifické pro přenos pomocí protokolu WebSockets</span><span class="sxs-lookup"><span data-stu-id="633b4-1121">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="633b4-1122">Přenos dlouhého cyklického dotazování má další možnosti, které je možné konfigurovat pomocí `LongPolling` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="633b4-1122">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="633b4-1123">Možnost</span><span class="sxs-lookup"><span data-stu-id="633b4-1123">Option</span></span> | <span data-ttu-id="633b4-1124">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-1124">Default Value</span></span> | <span data-ttu-id="633b4-1125">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-1125">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="633b4-1126">90 sekund</span><span class="sxs-lookup"><span data-stu-id="633b4-1126">90 seconds</span></span> | <span data-ttu-id="633b4-1127">Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jedné žádosti o cyklické dotazování.</span><span class="sxs-lookup"><span data-stu-id="633b4-1127">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="633b4-1128">Snížení této hodnoty způsobí, že klient bude vydávat nové požadavky na dotaz častěji.</span><span class="sxs-lookup"><span data-stu-id="633b4-1128">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="633b4-1129">Přenos protokolu WebSocket má další možnosti, které je možné konfigurovat pomocí `WebSockets` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="633b4-1129">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="633b4-1130">Možnost</span><span class="sxs-lookup"><span data-stu-id="633b4-1130">Option</span></span> | <span data-ttu-id="633b4-1131">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-1131">Default Value</span></span> | <span data-ttu-id="633b4-1132">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-1132">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="633b4-1133">5 sekund</span><span class="sxs-lookup"><span data-stu-id="633b4-1133">5 seconds</span></span> | <span data-ttu-id="633b4-1134">Pokud se po ukončení serveru aplikace v tomto časovém intervalu nepovede zavřít, připojení se ukončí.</span><span class="sxs-lookup"><span data-stu-id="633b4-1134">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="633b4-1135">Delegát, který lze použít k nastavení `Sec-WebSocket-Protocol` záhlaví na vlastní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="633b4-1135">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="633b4-1136">Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že se vrátí požadovaná hodnota.</span><span class="sxs-lookup"><span data-stu-id="633b4-1136">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="633b4-1137">Konfigurace možností klienta</span><span class="sxs-lookup"><span data-stu-id="633b4-1137">Configure client options</span></span>

<span data-ttu-id="633b4-1138">Možnosti klienta lze nakonfigurovat pro `HubConnectionBuilder` typ (k dispozici v klientech rozhraní .NET a v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="633b4-1138">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="633b4-1139">Je také k dispozici v klientovi Java, ale `HttpHubConnectionBuilder` podtřídou je to, co obsahuje možnosti konfigurace tvůrce a také na `HubConnection` sebe sama.</span><span class="sxs-lookup"><span data-stu-id="633b4-1139">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="633b4-1140">Konfigurovat protokolování</span><span class="sxs-lookup"><span data-stu-id="633b4-1140">Configure logging</span></span>

<span data-ttu-id="633b4-1141">Protokolování je konfigurováno v klientovi .NET pomocí `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="633b4-1141">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="633b4-1142">Zprostředkovatelé protokolování a filtry je možné zaregistrovat stejným způsobem jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="633b4-1142">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="633b4-1143">Další informace najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="633b4-1143">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="633b4-1144">Aby bylo možné registrovat poskytovatele protokolování, je nutné nainstalovat potřebné balíčky.</span><span class="sxs-lookup"><span data-stu-id="633b4-1144">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="633b4-1145">Úplný seznam najdete v části [předdefinovaná zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="633b4-1145">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="633b4-1146">Chcete-li například povolit protokolování konzoly, nainstalujte `Microsoft.Extensions.Logging.Console` balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="633b4-1146">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="633b4-1147">Zavolejte `AddConsole` metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="633b4-1147">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="633b4-1148">V klientovi jazyka JavaScript existuje podobná `configureLogging` metoda.</span><span class="sxs-lookup"><span data-stu-id="633b4-1148">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="633b4-1149">Zadejte `LogLevel` hodnotu, která určuje minimální úroveň zpráv protokolu, které mají být vyprodukovány.</span><span class="sxs-lookup"><span data-stu-id="633b4-1149">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="633b4-1150">Protokoly se zapisují do okna konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="633b4-1150">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="633b4-1151">Chcete-li protokolování zcela zakázat, zadejte `signalR.LogLevel.None` v `configureLogging` metodě.</span><span class="sxs-lookup"><span data-stu-id="633b4-1151">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="633b4-1152">Další informace o protokolování naleznete v dokumentaci k [ SignalR diagnostice](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="633b4-1152">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="633b4-1153">SignalRKlient Java používá k protokolování knihovnu [SLF4J](https://www.slf4j.org/) .</span><span class="sxs-lookup"><span data-stu-id="633b4-1153">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="633b4-1154">Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí.</span><span class="sxs-lookup"><span data-stu-id="633b4-1154">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="633b4-1155">Následující fragment kódu ukazuje, jak používat `java.util.logging` s SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="633b4-1155">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="633b4-1156">Pokud ve svých závislostech nenakonfigurujete protokolování, SLF4J načte výchozí protokolovací nástroj No-operation s následující zprávou upozornění:</span><span class="sxs-lookup"><span data-stu-id="633b4-1156">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="633b4-1157">Tuto možnost lze bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="633b4-1157">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="633b4-1158">Konfigurace povolených přenosů</span><span class="sxs-lookup"><span data-stu-id="633b4-1158">Configure allowed transports</span></span>

<span data-ttu-id="633b4-1159">Přenosy používané nástrojem SignalR lze konfigurovat ve `WithUrl` volání ( `withUrl` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="633b4-1159">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="633b4-1160">Bitové nebo hodnoty `HttpTransportType` lze použít k omezení klienta na používání pouze zadaných přenosů.</span><span class="sxs-lookup"><span data-stu-id="633b4-1160">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="633b4-1161">Ve výchozím nastavení jsou povolené všechny přenosy.</span><span class="sxs-lookup"><span data-stu-id="633b4-1161">All transports are enabled by default.</span></span>

<span data-ttu-id="633b4-1162">Pokud třeba chcete zakázat přenos událostí odeslaných serverem, ale povolit WebSockets a dlouhá připojení s dotazem:</span><span class="sxs-lookup"><span data-stu-id="633b4-1162">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="633b4-1163">V klientu jazyka JavaScript jsou přenosy konfigurovány nastavením `transport` pole v objektu Options, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="633b4-1163">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="633b4-1164">Konfigurace ověřování nosiče</span><span class="sxs-lookup"><span data-stu-id="633b4-1164">Configure bearer authentication</span></span>

<span data-ttu-id="633b4-1165">K poskytnutí ověřovacích dat spolu s SignalR požadavky použijte `AccessTokenProvider` možnost ( `accessTokenFactory` v jazyce JavaScript) k určení funkce, která vrací požadovaný přístupový token.</span><span class="sxs-lookup"><span data-stu-id="633b4-1165">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="633b4-1166">V klientovi .NET se tento přístupový token předává jako token ověřování HTTP "Bearer" (s použitím `Authorization` hlavičky s typem `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="633b4-1166">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="633b4-1167">V klientu jazyka JavaScript se přístupový token používá jako nosný token, **s výjimkou** případů, kdy rozhraní API prohlížeče omezuje možnost použít hlavičky (konkrétně v požadavcích na události odeslané serverem a objekty WebSockets).</span><span class="sxs-lookup"><span data-stu-id="633b4-1167">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="633b4-1168">V těchto případech je přístupový token k dispozici jako hodnota řetězce dotazu `access_token` .</span><span class="sxs-lookup"><span data-stu-id="633b4-1168">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="633b4-1169">V klientu .NET `AccessTokenProvider` lze možnost zadat pomocí delegáta možnosti v `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="633b4-1169">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="633b4-1170">V klientovi JavaScriptu je přístupový token nakonfigurovaný nastavením `accessTokenFactory` pole v objektu Options v `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="633b4-1170">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="633b4-1171">V SignalR klientovi Java můžete nakonfigurovat nosný token, který se bude používat pro ověřování, a to poskytnutím továrny přístupového tokenu [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="633b4-1171">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="633b4-1172">Pomocí [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) můžete zadat [RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="633b4-1172">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="633b4-1173">Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.</span><span class="sxs-lookup"><span data-stu-id="633b4-1173">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="633b4-1174">Konfigurace možností timeout a Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="633b4-1174">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="633b4-1175">Další možnosti konfigurace časového limitu a chování funkce Keep-Alive jsou k dispozici na `HubConnection` samotném objektu:</span><span class="sxs-lookup"><span data-stu-id="633b4-1175">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="633b4-1176">.NET</span><span class="sxs-lookup"><span data-stu-id="633b4-1176">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="633b4-1177">Možnost</span><span class="sxs-lookup"><span data-stu-id="633b4-1177">Option</span></span> | <span data-ttu-id="633b4-1178">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-1178">Default value</span></span> | <span data-ttu-id="633b4-1179">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-1179">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="633b4-1180">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="633b4-1180">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="633b4-1181">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="633b4-1181">Timeout for server activity.</span></span> <span data-ttu-id="633b4-1182">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí `Closed` událost ( `onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="633b4-1182">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="633b4-1183">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="633b4-1183">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="633b4-1184">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="633b4-1184">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="633b4-1185">15 sekund</span><span class="sxs-lookup"><span data-stu-id="633b4-1185">15 seconds</span></span> | <span data-ttu-id="633b4-1186">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="633b4-1186">Timeout for initial server handshake.</span></span> <span data-ttu-id="633b4-1187">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a spustí `Closed` událost ( `onclose` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="633b4-1187">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="633b4-1188">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="633b4-1188">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="633b4-1189">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="633b4-1189">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="633b4-1190">V klientovi .NET jsou hodnoty časového limitu zadány jako `TimeSpan` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="633b4-1190">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="633b4-1191">JavaScript</span><span class="sxs-lookup"><span data-stu-id="633b4-1191">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="633b4-1192">Možnost</span><span class="sxs-lookup"><span data-stu-id="633b4-1192">Option</span></span> | <span data-ttu-id="633b4-1193">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-1193">Default value</span></span> | <span data-ttu-id="633b4-1194">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-1194">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="633b4-1195">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="633b4-1195">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="633b4-1196">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="633b4-1196">Timeout for server activity.</span></span> <span data-ttu-id="633b4-1197">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onclose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="633b4-1197">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="633b4-1198">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="633b4-1198">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="633b4-1199">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="633b4-1199">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="633b4-1200">Java</span><span class="sxs-lookup"><span data-stu-id="633b4-1200">Java</span></span>](#tab/java)

| <span data-ttu-id="633b4-1201">Možnost</span><span class="sxs-lookup"><span data-stu-id="633b4-1201">Option</span></span> | <span data-ttu-id="633b4-1202">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-1202">Default value</span></span> | <span data-ttu-id="633b4-1203">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-1203">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="633b4-1204">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="633b4-1204">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="633b4-1205">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="633b4-1205">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="633b4-1206">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="633b4-1206">Timeout for server activity.</span></span> <span data-ttu-id="633b4-1207">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onClose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="633b4-1207">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="633b4-1208">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="633b4-1208">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="633b4-1209">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit k příkazům k zadání času.</span><span class="sxs-lookup"><span data-stu-id="633b4-1209">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="633b4-1210">15 sekund</span><span class="sxs-lookup"><span data-stu-id="633b4-1210">15 seconds</span></span> | <span data-ttu-id="633b4-1211">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="633b4-1211">Timeout for initial server handshake.</span></span> <span data-ttu-id="633b4-1212">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje `onClose` událost.</span><span class="sxs-lookup"><span data-stu-id="633b4-1212">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="633b4-1213">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="633b4-1213">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="633b4-1214">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="633b4-1214">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="633b4-1215">Konfigurace dalších možností</span><span class="sxs-lookup"><span data-stu-id="633b4-1215">Configure additional options</span></span>

<span data-ttu-id="633b4-1216">Další možnosti lze konfigurovat v `WithUrl` `withUrl` metodě (v jazyce JavaScript) na `HubConnectionBuilder` různých konfiguračních rozhraních API v `HttpHubConnectionBuilder` klientovi Java:</span><span class="sxs-lookup"><span data-stu-id="633b4-1216">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="633b4-1217">.NET</span><span class="sxs-lookup"><span data-stu-id="633b4-1217">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="633b4-1218">Možnost .NET</span><span class="sxs-lookup"><span data-stu-id="633b4-1218">.NET Option</span></span> |  <span data-ttu-id="633b4-1219">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-1219">Default value</span></span> | <span data-ttu-id="633b4-1220">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-1220">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="633b4-1221">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="633b4-1221">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="633b4-1222">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="633b4-1222">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="633b4-1223">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="633b4-1223">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="633b4-1224">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="633b4-1224">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="633b4-1225">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="633b4-1225">Empty</span></span> | <span data-ttu-id="633b4-1226">Kolekce certifikátů TLS pro odeslání na požadavky na ověření.</span><span class="sxs-lookup"><span data-stu-id="633b4-1226">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="633b4-1227">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="633b4-1227">Empty</span></span> | <span data-ttu-id="633b4-1228">Kolekce HTTP cookie s pro odeslání s každou žádostí http</span><span class="sxs-lookup"><span data-stu-id="633b4-1228">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="633b4-1229">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="633b4-1229">Empty</span></span> | <span data-ttu-id="633b4-1230">Přihlašovací údaje, které se mají poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="633b4-1230">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="633b4-1231">5 sekund</span><span class="sxs-lookup"><span data-stu-id="633b4-1231">5 seconds</span></span> | <span data-ttu-id="633b4-1232">Jenom objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="633b4-1232">WebSockets only.</span></span> <span data-ttu-id="633b4-1233">Maximální doba, po jejímž uplynutí bude klient čekat na ukončení žádosti o uzavření.</span><span class="sxs-lookup"><span data-stu-id="633b4-1233">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="633b4-1234">Pokud server nepotvrdí zavření v této době, klient se odpojí.</span><span class="sxs-lookup"><span data-stu-id="633b4-1234">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="633b4-1235">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="633b4-1235">Empty</span></span> | <span data-ttu-id="633b4-1236">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="633b4-1236">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="633b4-1237">Delegát, který lze použít ke konfiguraci nebo nahrazení `HttpMessageHandler` používaného k odeslání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="633b4-1237">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="633b4-1238">Nepoužívá se pro připojení pomocí protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="633b4-1238">Not used for WebSocket connections.</span></span> <span data-ttu-id="633b4-1239">Tento delegát musí vracet hodnotu, která není null, a obdrží výchozí hodnotu jako parametr.</span><span class="sxs-lookup"><span data-stu-id="633b4-1239">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="633b4-1240">Buď změňte nastavení u této výchozí hodnoty a vraťte je, nebo vraťte novou `HttpMessageHandler` instanci.</span><span class="sxs-lookup"><span data-stu-id="633b4-1240">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="633b4-1241">**Při nahrazování obslužné rutiny Nezapomeňte zkopírovat nastavení, která chcete zachovat od poskytnuté obslužné rutiny, jinak konfigurované možnosti (například Cookie s a záhlaví) nebudou použity pro novou obslužnou rutinu.**</span><span class="sxs-lookup"><span data-stu-id="633b4-1241">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="633b4-1242">Proxy server HTTP, který se má použít při odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="633b4-1242">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="633b4-1243">Nastavte tuto logickou hodnotu pro odeslání výchozích přihlašovacích údajů pro požadavky HTTP a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="633b4-1243">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="633b4-1244">To umožňuje použití ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="633b4-1244">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="633b4-1245">Delegát, který lze použít ke konfiguraci dalších možností protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="633b4-1245">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="633b4-1246">Přijímá instanci [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , která se dá použít ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="633b4-1246">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="633b4-1247">JavaScript</span><span class="sxs-lookup"><span data-stu-id="633b4-1247">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="633b4-1248">Možnost JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="633b4-1248">JavaScript Option</span></span> | <span data-ttu-id="633b4-1249">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-1249">Default Value</span></span> | <span data-ttu-id="633b4-1250">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-1250">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="633b4-1251">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="633b4-1251">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="633b4-1252">Nastavte na `true` protokolování bajtů/znaků zpráv odesílaných a přijímaných klientem.</span><span class="sxs-lookup"><span data-stu-id="633b4-1252">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="633b4-1253">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="633b4-1253">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="633b4-1254">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="633b4-1254">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="633b4-1255">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="633b4-1255">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="633b4-1256">Java</span><span class="sxs-lookup"><span data-stu-id="633b4-1256">Java</span></span>](#tab/java)

| <span data-ttu-id="633b4-1257">Možnost jazyka Java</span><span class="sxs-lookup"><span data-stu-id="633b4-1257">Java Option</span></span> | <span data-ttu-id="633b4-1258">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="633b4-1258">Default Value</span></span> | <span data-ttu-id="633b4-1259">Description</span><span class="sxs-lookup"><span data-stu-id="633b4-1259">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="633b4-1260">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="633b4-1260">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="633b4-1261">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="633b4-1261">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="633b4-1262">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="633b4-1262">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="633b4-1263">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="633b4-1263">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="633b4-1264">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="633b4-1264">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="633b4-1265">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="633b4-1265">Empty</span></span> | <span data-ttu-id="633b4-1266">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="633b4-1266">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="633b4-1267">V klientu .NET můžete tyto možnosti upravit pomocí delegáta možností, který je k dispozici pro `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="633b4-1267">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="633b4-1268">V klientovi JavaScriptu lze tyto možnosti poskytnout v objektu jazyka JavaScript, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="633b4-1268">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="633b4-1269">V klientovi Java lze tyto možnosti nakonfigurovat s metodami `HttpHubConnectionBuilder` vrácenými z `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="633b4-1269">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="633b4-1270">Další materiály</span><span class="sxs-lookup"><span data-stu-id="633b4-1270">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
