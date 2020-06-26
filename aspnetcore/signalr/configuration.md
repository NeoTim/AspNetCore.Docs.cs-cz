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
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/configuration
ms.openlocfilehash: c711c2163908e3fdd20e3bb497f333ebd495d921
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2020
ms.locfileid: "85406833"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="ee79f-103">SignalRKonfigurace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ee79f-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="ee79f-104">Možnosti serializace JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="ee79f-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="ee79f-105">ASP.NET Core SignalR podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="ee79f-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="ee79f-106">Každý protokol má možnosti konfigurace serializace.</span><span class="sxs-lookup"><span data-stu-id="ee79f-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="ee79f-107">Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="ee79f-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="ee79f-108">`AddJsonProtocol`lze přidat po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="ee79f-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ee79f-109">`AddJsonProtocol`Metoda přebírá delegáta, který přijímá `options` objekt.</span><span class="sxs-lookup"><span data-stu-id="ee79f-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="ee79f-110">Vlastnost [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) tohoto objektu je `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objekt, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="ee79f-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="ee79f-111">Další informace najdete v [System.Text.Jsv dokumentaci](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="ee79f-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="ee79f-112">Chcete-li například nakonfigurovat serializátor, aby neměnil velká a malá písmena názvů vlastností namísto výchozích názvů "camelCase", použijte následující kód v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ee79f-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="ee79f-113">V klientovi .NET `AddJsonProtocol` existuje stejná rozšiřující metoda v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="ee79f-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="ee79f-114">`Microsoft.Extensions.DependencyInjection`Obor názvů musí být importován, aby bylo možné přeložit metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="ee79f-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="ee79f-115">V tuto chvíli není možné konfigurovat serializaci JSON v klientovi jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ee79f-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="ee79f-116">Přepnout na Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="ee79f-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="ee79f-117">Pokud potřebujete funkce nástroje `Newtonsoft.Json` , které nejsou podporované v systému `System.Text.Json` , přečtěte si téma [Přepnutí na Newtonsoft.Jsna](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="ee79f-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="ee79f-118">Možnosti serializace MessagePack</span><span class="sxs-lookup"><span data-stu-id="ee79f-118">MessagePack serialization options</span></span>

<span data-ttu-id="ee79f-119">Serializaci MessagePack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="ee79f-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="ee79f-120">Další podrobnosti najdete [v SignalR tématu MessagePack](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="ee79f-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="ee79f-121">V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi v jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ee79f-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="ee79f-122">Konfigurovat možnosti serveru</span><span class="sxs-lookup"><span data-stu-id="ee79f-122">Configure server options</span></span>

<span data-ttu-id="ee79f-123">Následující tabulka popisuje možnosti konfigurace SignalR Center:</span><span class="sxs-lookup"><span data-stu-id="ee79f-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="ee79f-124">Možnost</span><span class="sxs-lookup"><span data-stu-id="ee79f-124">Option</span></span> | <span data-ttu-id="ee79f-125">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-125">Default Value</span></span> | <span data-ttu-id="ee79f-126">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="ee79f-127">30 sekund</span><span class="sxs-lookup"><span data-stu-id="ee79f-127">30 seconds</span></span> | <span data-ttu-id="ee79f-128">Server bude předpokládat, že je klient odpojený, pokud v tomto intervalu neobdržel zprávu (včetně Keep-Alive).</span><span class="sxs-lookup"><span data-stu-id="ee79f-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="ee79f-129">Může trvat delší dobu, než je tento časový limit, aby bylo možné klienta ve skutečnosti označit jako odpojený, protože to je implementováno.</span><span class="sxs-lookup"><span data-stu-id="ee79f-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="ee79f-130">Doporučená hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="ee79f-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="ee79f-131">15 sekund</span><span class="sxs-lookup"><span data-stu-id="ee79f-131">15 seconds</span></span> | <span data-ttu-id="ee79f-132">Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře.</span><span class="sxs-lookup"><span data-stu-id="ee79f-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="ee79f-133">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="ee79f-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="ee79f-134">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="ee79f-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="ee79f-135">15 sekund</span><span class="sxs-lookup"><span data-stu-id="ee79f-135">15 seconds</span></span> | <span data-ttu-id="ee79f-136">Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="ee79f-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="ee79f-137">Při změně `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení změňte nastavení v klientovi.</span><span class="sxs-lookup"><span data-stu-id="ee79f-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="ee79f-138">Doporučená `ServerTimeout` / `serverTimeoutInMilliseconds` hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="ee79f-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="ee79f-139">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="ee79f-139">All installed protocols</span></span> | <span data-ttu-id="ee79f-140">Protokoly podporované tímto rozbočovačem</span><span class="sxs-lookup"><span data-stu-id="ee79f-140">Protocols supported by this hub.</span></span> <span data-ttu-id="ee79f-141">Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly.</span><span class="sxs-lookup"><span data-stu-id="ee79f-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="ee79f-142">Pokud se `true` do klientů vrátí podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="ee79f-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="ee79f-143">Výchozí hodnota je `false` , protože tyto zprávy o výjimce můžou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="ee79f-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="ee79f-144">Maximální počet položek, které lze uložit do vyrovnávací paměti pro datové proudy pro odeslání klienta.</span><span class="sxs-lookup"><span data-stu-id="ee79f-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="ee79f-145">Pokud je dosaženo tohoto limitu, zpracování volání je blokováno, dokud server nezpracovává položky datového proudu.</span><span class="sxs-lookup"><span data-stu-id="ee79f-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="ee79f-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="ee79f-146">32 KB</span></span> | <span data-ttu-id="ee79f-147">Maximální velikost jedné příchozí zprávy centra</span><span class="sxs-lookup"><span data-stu-id="ee79f-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="ee79f-148">Možnosti lze nakonfigurovat pro všechna centra tím, že poskytnete možnosti delegáta pro `AddSignalR` volání v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="ee79f-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="ee79f-149">Možnosti pro jeden rozbočovač přepíší globální možnosti poskytované v `AddSignalR` a můžou být nakonfigurované pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="ee79f-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="ee79f-150">Rozšířené možnosti konfigurace protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="ee79f-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="ee79f-151">Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="ee79f-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="ee79f-152">Tyto možnosti jsou nakonfigurovány předáním delegáta [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="ee79f-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="ee79f-153">Následující tabulka popisuje možnosti konfigurace SignalR rozšířených možností protokolu HTTP v ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="ee79f-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="ee79f-154">Možnost</span><span class="sxs-lookup"><span data-stu-id="ee79f-154">Option</span></span> | <span data-ttu-id="ee79f-155">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-155">Default Value</span></span> | <span data-ttu-id="ee79f-156">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="ee79f-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="ee79f-157">32 KB</span></span> | <span data-ttu-id="ee79f-158">Maximální počet bajtů přijatých od klienta, které jsou ukládány do vyrovnávací paměti serveru před použitím protitlaku.</span><span class="sxs-lookup"><span data-stu-id="ee79f-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="ee79f-159">Zvýšením této hodnoty umožníte, aby server přijímal větší zprávy rychleji bez použití zatížení, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="ee79f-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="ee79f-160">Data se automaticky shromažďují z `Authorize` atributů použitých pro třídu centra.</span><span class="sxs-lookup"><span data-stu-id="ee79f-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="ee79f-161">Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru</span><span class="sxs-lookup"><span data-stu-id="ee79f-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="ee79f-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="ee79f-162">32 KB</span></span> | <span data-ttu-id="ee79f-163">Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru před pozorováním protitlaku.</span><span class="sxs-lookup"><span data-stu-id="ee79f-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="ee79f-164">Zvýšením této hodnoty umožníte serveru rychleji ukládat větší zprávy bez nutnosti očekávat zatížení, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="ee79f-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="ee79f-165">Všechny přenosy jsou povolené.</span><span class="sxs-lookup"><span data-stu-id="ee79f-165">All Transports are enabled.</span></span> | <span data-ttu-id="ee79f-166">Bitové příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenos, který může klient použít pro připojení.</span><span class="sxs-lookup"><span data-stu-id="ee79f-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="ee79f-167">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="ee79f-167">See below.</span></span> | <span data-ttu-id="ee79f-168">Další možnosti specifické pro přenos dlouhého cyklického dotazování.</span><span class="sxs-lookup"><span data-stu-id="ee79f-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="ee79f-169">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="ee79f-169">See below.</span></span> | <span data-ttu-id="ee79f-170">Další možnosti specifické pro přenos pomocí protokolu WebSockets</span><span class="sxs-lookup"><span data-stu-id="ee79f-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="ee79f-171">0</span><span class="sxs-lookup"><span data-stu-id="ee79f-171">0</span></span> | <span data-ttu-id="ee79f-172">Zadejte minimální verzi protokolu Negotiate.</span><span class="sxs-lookup"><span data-stu-id="ee79f-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="ee79f-173">Slouží k omezení klientů na novější verze.</span><span class="sxs-lookup"><span data-stu-id="ee79f-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="ee79f-174">Přenos dlouhého cyklického dotazování má další možnosti, které je možné konfigurovat pomocí `LongPolling` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="ee79f-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="ee79f-175">Možnost</span><span class="sxs-lookup"><span data-stu-id="ee79f-175">Option</span></span> | <span data-ttu-id="ee79f-176">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-176">Default Value</span></span> | <span data-ttu-id="ee79f-177">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="ee79f-178">90 sekund</span><span class="sxs-lookup"><span data-stu-id="ee79f-178">90 seconds</span></span> | <span data-ttu-id="ee79f-179">Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jedné žádosti o cyklické dotazování.</span><span class="sxs-lookup"><span data-stu-id="ee79f-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="ee79f-180">Snížení této hodnoty způsobí, že klient bude vydávat nové požadavky na dotaz častěji.</span><span class="sxs-lookup"><span data-stu-id="ee79f-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="ee79f-181">Přenos protokolu WebSocket má další možnosti, které je možné konfigurovat pomocí `WebSockets` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="ee79f-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="ee79f-182">Možnost</span><span class="sxs-lookup"><span data-stu-id="ee79f-182">Option</span></span> | <span data-ttu-id="ee79f-183">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-183">Default Value</span></span> | <span data-ttu-id="ee79f-184">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="ee79f-185">5 sekund</span><span class="sxs-lookup"><span data-stu-id="ee79f-185">5 seconds</span></span> | <span data-ttu-id="ee79f-186">Pokud se po ukončení serveru aplikace v tomto časovém intervalu nepovede zavřít, připojení se ukončí.</span><span class="sxs-lookup"><span data-stu-id="ee79f-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="ee79f-187">Delegát, který lze použít k nastavení `Sec-WebSocket-Protocol` záhlaví na vlastní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="ee79f-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="ee79f-188">Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že se vrátí požadovaná hodnota.</span><span class="sxs-lookup"><span data-stu-id="ee79f-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="ee79f-189">Konfigurace možností klienta</span><span class="sxs-lookup"><span data-stu-id="ee79f-189">Configure client options</span></span>

<span data-ttu-id="ee79f-190">Možnosti klienta lze nakonfigurovat pro `HubConnectionBuilder` typ (k dispozici v klientech rozhraní .NET a v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="ee79f-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="ee79f-191">Je také k dispozici v klientovi Java, ale `HttpHubConnectionBuilder` podtřídou je to, co obsahuje možnosti konfigurace tvůrce a také na `HubConnection` sebe sama.</span><span class="sxs-lookup"><span data-stu-id="ee79f-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="ee79f-192">Konfigurovat protokolování</span><span class="sxs-lookup"><span data-stu-id="ee79f-192">Configure logging</span></span>

<span data-ttu-id="ee79f-193">Protokolování je konfigurováno v klientovi .NET pomocí `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="ee79f-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="ee79f-194">Zprostředkovatelé protokolování a filtry je možné zaregistrovat stejným způsobem jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="ee79f-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="ee79f-195">Další informace najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="ee79f-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="ee79f-196">Aby bylo možné registrovat poskytovatele protokolování, je nutné nainstalovat potřebné balíčky.</span><span class="sxs-lookup"><span data-stu-id="ee79f-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="ee79f-197">Úplný seznam najdete v části [předdefinovaná zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="ee79f-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="ee79f-198">Chcete-li například povolit protokolování konzoly, nainstalujte `Microsoft.Extensions.Logging.Console` balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="ee79f-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="ee79f-199">Zavolejte `AddConsole` metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="ee79f-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="ee79f-200">V klientovi jazyka JavaScript existuje podobná `configureLogging` metoda.</span><span class="sxs-lookup"><span data-stu-id="ee79f-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="ee79f-201">Zadejte `LogLevel` hodnotu, která určuje minimální úroveň zpráv protokolu, které mají být vyprodukovány.</span><span class="sxs-lookup"><span data-stu-id="ee79f-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="ee79f-202">Protokoly se zapisují do okna konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="ee79f-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="ee79f-203">Místo `LogLevel` hodnoty můžete zadat také `string` hodnotu představující název úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="ee79f-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="ee79f-204">To je užitečné při konfiguraci SignalR protokolování v prostředích, kde nemáte přístup k `LogLevel` konstantám.</span><span class="sxs-lookup"><span data-stu-id="ee79f-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="ee79f-205">V následující tabulce jsou uvedeny dostupné úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="ee79f-205">The following table lists the available log levels.</span></span> <span data-ttu-id="ee79f-206">Hodnota, kterou zadáte, `configureLogging` nastaví **minimální** úroveň protokolu, která se bude protokolovat.</span><span class="sxs-lookup"><span data-stu-id="ee79f-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="ee79f-207">Zprávy zaznamenané na této úrovni **nebo úrovně uvedené za ní v tabulce**budou protokolovány.</span><span class="sxs-lookup"><span data-stu-id="ee79f-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="ee79f-208">Řetězec</span><span class="sxs-lookup"><span data-stu-id="ee79f-208">String</span></span>                      | <span data-ttu-id="ee79f-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="ee79f-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="ee79f-210">`info` **nebo** `information`</span><span class="sxs-lookup"><span data-stu-id="ee79f-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="ee79f-211">`warn` **nebo** `warning`</span><span class="sxs-lookup"><span data-stu-id="ee79f-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="ee79f-212">Chcete-li protokolování zcela zakázat, zadejte `signalR.LogLevel.None` v `configureLogging` metodě.</span><span class="sxs-lookup"><span data-stu-id="ee79f-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="ee79f-213">Další informace o protokolování naleznete v dokumentaci k [ SignalR diagnostice](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="ee79f-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="ee79f-214">SignalRKlient Java používá k protokolování knihovnu [SLF4J](https://www.slf4j.org/) .</span><span class="sxs-lookup"><span data-stu-id="ee79f-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="ee79f-215">Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí.</span><span class="sxs-lookup"><span data-stu-id="ee79f-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="ee79f-216">Následující fragment kódu ukazuje, jak používat `java.util.logging` s SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="ee79f-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="ee79f-217">Pokud ve svých závislostech nenakonfigurujete protokolování, SLF4J načte výchozí protokolovací nástroj No-operation s následující zprávou upozornění:</span><span class="sxs-lookup"><span data-stu-id="ee79f-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="ee79f-218">Tuto možnost lze bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="ee79f-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="ee79f-219">Konfigurace povolených přenosů</span><span class="sxs-lookup"><span data-stu-id="ee79f-219">Configure allowed transports</span></span>

<span data-ttu-id="ee79f-220">Přenosy používané nástrojem SignalR lze konfigurovat ve `WithUrl` volání ( `withUrl` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="ee79f-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="ee79f-221">Bitové nebo hodnoty `HttpTransportType` lze použít k omezení klienta na používání pouze zadaných přenosů.</span><span class="sxs-lookup"><span data-stu-id="ee79f-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="ee79f-222">Ve výchozím nastavení jsou povolené všechny přenosy.</span><span class="sxs-lookup"><span data-stu-id="ee79f-222">All transports are enabled by default.</span></span>

<span data-ttu-id="ee79f-223">Pokud třeba chcete zakázat přenos událostí odeslaných serverem, ale povolit WebSockets a dlouhá připojení s dotazem:</span><span class="sxs-lookup"><span data-stu-id="ee79f-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="ee79f-224">V klientu jazyka JavaScript jsou přenosy konfigurovány nastavením `transport` pole v objektu Options, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="ee79f-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="ee79f-225">V této verzi je jediným dostupným přenosem klientský WebSocket v jazyce Java.</span><span class="sxs-lookup"><span data-stu-id="ee79f-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="ee79f-226">V klientovi Java se přenos vybere s `withTransport` metodou na `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="ee79f-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="ee79f-227">Klient Java standardně používá přenos pomocí protokolu WebSockets.</span><span class="sxs-lookup"><span data-stu-id="ee79f-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="ee79f-228">SignalRKlient Java ještě nepodporuje přenosovou zálohu.</span><span class="sxs-lookup"><span data-stu-id="ee79f-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="ee79f-229">Konfigurace ověřování nosiče</span><span class="sxs-lookup"><span data-stu-id="ee79f-229">Configure bearer authentication</span></span>

<span data-ttu-id="ee79f-230">K poskytnutí ověřovacích dat spolu s SignalR požadavky použijte `AccessTokenProvider` možnost ( `accessTokenFactory` v jazyce JavaScript) k určení funkce, která vrací požadovaný přístupový token.</span><span class="sxs-lookup"><span data-stu-id="ee79f-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="ee79f-231">V klientovi .NET se tento přístupový token předává jako token ověřování HTTP "Bearer" (s použitím `Authorization` hlavičky s typem `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="ee79f-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="ee79f-232">V klientu jazyka JavaScript se přístupový token používá jako nosný token, **s výjimkou** případů, kdy rozhraní API prohlížeče omezuje možnost použít hlavičky (konkrétně v požadavcích na události odeslané serverem a objekty WebSockets).</span><span class="sxs-lookup"><span data-stu-id="ee79f-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="ee79f-233">V těchto případech je přístupový token k dispozici jako hodnota řetězce dotazu `access_token` .</span><span class="sxs-lookup"><span data-stu-id="ee79f-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="ee79f-234">V klientu .NET `AccessTokenProvider` lze možnost zadat pomocí delegáta možnosti v `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="ee79f-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="ee79f-235">V klientovi JavaScriptu je přístupový token nakonfigurovaný nastavením `accessTokenFactory` pole v objektu Options v `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="ee79f-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="ee79f-236">V SignalR klientovi Java můžete nakonfigurovat nosný token, který se bude používat pro ověřování, a to poskytnutím továrny přístupového tokenu [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="ee79f-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="ee79f-237">Pomocí [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) můžete zadat [RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="ee79f-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="ee79f-238">Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.</span><span class="sxs-lookup"><span data-stu-id="ee79f-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="ee79f-239">Konfigurace možností timeout a Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="ee79f-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="ee79f-240">Další možnosti konfigurace časového limitu a chování funkce Keep-Alive jsou k dispozici na `HubConnection` samotném objektu:</span><span class="sxs-lookup"><span data-stu-id="ee79f-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="ee79f-241">.NET</span><span class="sxs-lookup"><span data-stu-id="ee79f-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="ee79f-242">Možnost</span><span class="sxs-lookup"><span data-stu-id="ee79f-242">Option</span></span> | <span data-ttu-id="ee79f-243">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-243">Default value</span></span> | <span data-ttu-id="ee79f-244">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="ee79f-245">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="ee79f-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="ee79f-246">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="ee79f-246">Timeout for server activity.</span></span> <span data-ttu-id="ee79f-247">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí `Closed` událost ( `onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="ee79f-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="ee79f-248">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="ee79f-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="ee79f-249">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="ee79f-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="ee79f-250">15 sekund</span><span class="sxs-lookup"><span data-stu-id="ee79f-250">15 seconds</span></span> | <span data-ttu-id="ee79f-251">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="ee79f-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="ee79f-252">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a spustí `Closed` událost ( `onclose` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="ee79f-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="ee79f-253">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="ee79f-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="ee79f-254">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="ee79f-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="ee79f-255">15 sekund</span><span class="sxs-lookup"><span data-stu-id="ee79f-255">15 seconds</span></span> | <span data-ttu-id="ee79f-256">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="ee79f-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="ee79f-257">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="ee79f-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="ee79f-258">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="ee79f-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="ee79f-259">V klientovi .NET jsou hodnoty časového limitu zadány jako `TimeSpan` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="ee79f-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="ee79f-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="ee79f-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="ee79f-261">Možnost</span><span class="sxs-lookup"><span data-stu-id="ee79f-261">Option</span></span> | <span data-ttu-id="ee79f-262">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-262">Default value</span></span> | <span data-ttu-id="ee79f-263">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="ee79f-264">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="ee79f-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="ee79f-265">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="ee79f-265">Timeout for server activity.</span></span> <span data-ttu-id="ee79f-266">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onclose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="ee79f-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="ee79f-267">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="ee79f-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="ee79f-268">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="ee79f-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="ee79f-269">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="ee79f-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="ee79f-270">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="ee79f-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="ee79f-271">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="ee79f-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="ee79f-272">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="ee79f-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="ee79f-273">Java</span><span class="sxs-lookup"><span data-stu-id="ee79f-273">Java</span></span>](#tab/java)

| <span data-ttu-id="ee79f-274">Možnost</span><span class="sxs-lookup"><span data-stu-id="ee79f-274">Option</span></span> | <span data-ttu-id="ee79f-275">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-275">Default value</span></span> | <span data-ttu-id="ee79f-276">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="ee79f-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="ee79f-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="ee79f-278">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="ee79f-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="ee79f-279">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="ee79f-279">Timeout for server activity.</span></span> <span data-ttu-id="ee79f-280">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onClose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="ee79f-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="ee79f-281">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="ee79f-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="ee79f-282">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="ee79f-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="ee79f-283">15 sekund</span><span class="sxs-lookup"><span data-stu-id="ee79f-283">15 seconds</span></span> | <span data-ttu-id="ee79f-284">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="ee79f-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="ee79f-285">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje `onClose` událost.</span><span class="sxs-lookup"><span data-stu-id="ee79f-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="ee79f-286">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="ee79f-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="ee79f-287">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="ee79f-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="ee79f-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="ee79f-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="ee79f-289">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="ee79f-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="ee79f-290">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="ee79f-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="ee79f-291">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="ee79f-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="ee79f-292">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="ee79f-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="ee79f-293">Konfigurace dalších možností</span><span class="sxs-lookup"><span data-stu-id="ee79f-293">Configure additional options</span></span>

<span data-ttu-id="ee79f-294">Další možnosti lze konfigurovat v `WithUrl` `withUrl` metodě (v jazyce JavaScript) na `HubConnectionBuilder` různých konfiguračních rozhraních API v `HttpHubConnectionBuilder` klientovi Java:</span><span class="sxs-lookup"><span data-stu-id="ee79f-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="ee79f-295">.NET</span><span class="sxs-lookup"><span data-stu-id="ee79f-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="ee79f-296">Možnost .NET</span><span class="sxs-lookup"><span data-stu-id="ee79f-296">.NET Option</span></span> |  <span data-ttu-id="ee79f-297">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-297">Default value</span></span> | <span data-ttu-id="ee79f-298">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="ee79f-299">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="ee79f-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="ee79f-300">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="ee79f-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="ee79f-301">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="ee79f-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="ee79f-302">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="ee79f-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="ee79f-303">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="ee79f-303">Empty</span></span> | <span data-ttu-id="ee79f-304">Kolekce certifikátů TLS pro odeslání na požadavky na ověření.</span><span class="sxs-lookup"><span data-stu-id="ee79f-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="ee79f-305">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="ee79f-305">Empty</span></span> | <span data-ttu-id="ee79f-306">Kolekce souborů cookie protokolu HTTP, které se mají odeslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="ee79f-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="ee79f-307">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="ee79f-307">Empty</span></span> | <span data-ttu-id="ee79f-308">Přihlašovací údaje, které se mají poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="ee79f-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="ee79f-309">5 sekund</span><span class="sxs-lookup"><span data-stu-id="ee79f-309">5 seconds</span></span> | <span data-ttu-id="ee79f-310">Jenom objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="ee79f-310">WebSockets only.</span></span> <span data-ttu-id="ee79f-311">Maximální doba, po jejímž uplynutí bude klient čekat na ukončení žádosti o uzavření.</span><span class="sxs-lookup"><span data-stu-id="ee79f-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="ee79f-312">Pokud server nepotvrdí zavření v této době, klient se odpojí.</span><span class="sxs-lookup"><span data-stu-id="ee79f-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="ee79f-313">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="ee79f-313">Empty</span></span> | <span data-ttu-id="ee79f-314">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="ee79f-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="ee79f-315">Delegát, který lze použít ke konfiguraci nebo nahrazení `HttpMessageHandler` používaného k odeslání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="ee79f-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="ee79f-316">Nepoužívá se pro připojení pomocí protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="ee79f-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="ee79f-317">Tento delegát musí vracet hodnotu, která není null, a obdrží výchozí hodnotu jako parametr.</span><span class="sxs-lookup"><span data-stu-id="ee79f-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="ee79f-318">Buď změňte nastavení u této výchozí hodnoty a vraťte je, nebo vraťte novou `HttpMessageHandler` instanci.</span><span class="sxs-lookup"><span data-stu-id="ee79f-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="ee79f-319">**Při nahrazování obslužné rutiny Nezapomeňte zkopírovat nastavení, která chcete zachovat, od poskytnuté obslužné rutiny, jinak konfigurované možnosti (například soubory cookie a záhlaví) nebudou použity pro novou obslužnou rutinu.**</span><span class="sxs-lookup"><span data-stu-id="ee79f-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="ee79f-320">Proxy server HTTP, který se má použít při odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="ee79f-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="ee79f-321">Nastavte tuto logickou hodnotu pro odeslání výchozích přihlašovacích údajů pro požadavky HTTP a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="ee79f-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="ee79f-322">To umožňuje použití ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="ee79f-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="ee79f-323">Delegát, který lze použít ke konfiguraci dalších možností protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="ee79f-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="ee79f-324">Přijímá instanci [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , která se dá použít ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="ee79f-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="ee79f-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="ee79f-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="ee79f-326">Možnost JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="ee79f-326">JavaScript Option</span></span> | <span data-ttu-id="ee79f-327">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-327">Default Value</span></span> | <span data-ttu-id="ee79f-328">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="ee79f-329">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="ee79f-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `headers` | `null` | <span data-ttu-id="ee79f-330">Slovník hlaviček odeslaných s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="ee79f-330">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="ee79f-331">Odesílání hlaviček v prohlížeči nefunguje pro objekty WebSockets ani <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> Stream.</span><span class="sxs-lookup"><span data-stu-id="ee79f-331">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="ee79f-332">Nastavte na `true` protokolování bajtů/znaků zpráv odesílaných a přijímaných klientem.</span><span class="sxs-lookup"><span data-stu-id="ee79f-332">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="ee79f-333">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="ee79f-333">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="ee79f-334">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="ee79f-334">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="ee79f-335">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="ee79f-335">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="ee79f-336">Určuje, jestli se budou odesílat přihlašovací údaje s požadavkem CORS.</span><span class="sxs-lookup"><span data-stu-id="ee79f-336">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="ee79f-337">Azure App Service používá pro rychlé relace soubory cookie a vyžaduje, aby tato možnost fungovala správně.</span><span class="sxs-lookup"><span data-stu-id="ee79f-337">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="ee79f-338">Další informace o CORS s SignalR najdete v tématu <xref:signalr/security#cross-origin-resource-sharing> .</span><span class="sxs-lookup"><span data-stu-id="ee79f-338">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="ee79f-339">Java</span><span class="sxs-lookup"><span data-stu-id="ee79f-339">Java</span></span>](#tab/java)

| <span data-ttu-id="ee79f-340">Možnost jazyka Java</span><span class="sxs-lookup"><span data-stu-id="ee79f-340">Java Option</span></span> | <span data-ttu-id="ee79f-341">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-341">Default Value</span></span> | <span data-ttu-id="ee79f-342">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-342">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="ee79f-343">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="ee79f-343">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="ee79f-344">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="ee79f-344">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="ee79f-345">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="ee79f-345">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="ee79f-346">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="ee79f-346">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="ee79f-347">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="ee79f-347">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="ee79f-348">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="ee79f-348">Empty</span></span> | <span data-ttu-id="ee79f-349">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="ee79f-349">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="ee79f-350">V klientu .NET můžete tyto možnosti upravit pomocí delegáta možností, který je k dispozici pro `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="ee79f-350">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="ee79f-351">V klientovi JavaScriptu lze tyto možnosti poskytnout v objektu jazyka JavaScript, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="ee79f-351">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="ee79f-352">V klientovi Java lze tyto možnosti nakonfigurovat s metodami `HttpHubConnectionBuilder` vrácenými z`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="ee79f-352">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="ee79f-353">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="ee79f-353">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="ee79f-354">Možnosti serializace JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="ee79f-354">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="ee79f-355">ASP.NET Core SignalR podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="ee79f-355">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="ee79f-356">Každý protokol má možnosti konfigurace serializace.</span><span class="sxs-lookup"><span data-stu-id="ee79f-356">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="ee79f-357">Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="ee79f-357">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="ee79f-358">`AddJsonProtocol`lze přidat po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="ee79f-358">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ee79f-359">`AddJsonProtocol`Metoda přebírá delegáta, který přijímá `options` objekt.</span><span class="sxs-lookup"><span data-stu-id="ee79f-359">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="ee79f-360">Vlastnost [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) tohoto objektu je `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objekt, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="ee79f-360">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="ee79f-361">Další informace najdete v [System.Text.Jsv dokumentaci](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="ee79f-361">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="ee79f-362">Chcete-li například nakonfigurovat serializátor, aby neměnil velká a malá písmena názvů vlastností namísto výchozích názvů "camelCase", použijte následující kód v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ee79f-362">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="ee79f-363">V klientovi .NET `AddJsonProtocol` existuje stejná rozšiřující metoda v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="ee79f-363">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="ee79f-364">`Microsoft.Extensions.DependencyInjection`Obor názvů musí být importován, aby bylo možné přeložit metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="ee79f-364">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="ee79f-365">V tuto chvíli není možné konfigurovat serializaci JSON v klientovi jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ee79f-365">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="ee79f-366">Přepnout na Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="ee79f-366">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="ee79f-367">Pokud potřebujete funkce nástroje `Newtonsoft.Json` , které nejsou podporované v systému `System.Text.Json` , přečtěte si téma [Přepnutí na Newtonsoft.Jsna](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="ee79f-367">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="ee79f-368">Možnosti serializace MessagePack</span><span class="sxs-lookup"><span data-stu-id="ee79f-368">MessagePack serialization options</span></span>

<span data-ttu-id="ee79f-369">Serializaci MessagePack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="ee79f-369">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="ee79f-370">Další podrobnosti najdete [v SignalR tématu MessagePack](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="ee79f-370">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="ee79f-371">V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi v jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ee79f-371">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="ee79f-372">Konfigurovat možnosti serveru</span><span class="sxs-lookup"><span data-stu-id="ee79f-372">Configure server options</span></span>

<span data-ttu-id="ee79f-373">Následující tabulka popisuje možnosti konfigurace SignalR Center:</span><span class="sxs-lookup"><span data-stu-id="ee79f-373">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="ee79f-374">Možnost</span><span class="sxs-lookup"><span data-stu-id="ee79f-374">Option</span></span> | <span data-ttu-id="ee79f-375">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-375">Default Value</span></span> | <span data-ttu-id="ee79f-376">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-376">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="ee79f-377">30 sekund</span><span class="sxs-lookup"><span data-stu-id="ee79f-377">30 seconds</span></span> | <span data-ttu-id="ee79f-378">Server bude předpokládat, že je klient odpojený, pokud v tomto intervalu neobdržel zprávu (včetně Keep-Alive).</span><span class="sxs-lookup"><span data-stu-id="ee79f-378">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="ee79f-379">Může trvat delší dobu, než je tento časový limit, aby bylo možné klienta ve skutečnosti označit jako odpojený, protože to je implementováno.</span><span class="sxs-lookup"><span data-stu-id="ee79f-379">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="ee79f-380">Doporučená hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="ee79f-380">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="ee79f-381">15 sekund</span><span class="sxs-lookup"><span data-stu-id="ee79f-381">15 seconds</span></span> | <span data-ttu-id="ee79f-382">Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře.</span><span class="sxs-lookup"><span data-stu-id="ee79f-382">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="ee79f-383">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="ee79f-383">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="ee79f-384">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="ee79f-384">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="ee79f-385">15 sekund</span><span class="sxs-lookup"><span data-stu-id="ee79f-385">15 seconds</span></span> | <span data-ttu-id="ee79f-386">Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="ee79f-386">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="ee79f-387">Při změně `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení změňte nastavení v klientovi.</span><span class="sxs-lookup"><span data-stu-id="ee79f-387">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="ee79f-388">Doporučená `ServerTimeout` / `serverTimeoutInMilliseconds` hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="ee79f-388">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="ee79f-389">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="ee79f-389">All installed protocols</span></span> | <span data-ttu-id="ee79f-390">Protokoly podporované tímto rozbočovačem</span><span class="sxs-lookup"><span data-stu-id="ee79f-390">Protocols supported by this hub.</span></span> <span data-ttu-id="ee79f-391">Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly.</span><span class="sxs-lookup"><span data-stu-id="ee79f-391">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="ee79f-392">Pokud se `true` do klientů vrátí podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="ee79f-392">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="ee79f-393">Výchozí hodnota je `false` , protože tyto zprávy o výjimce můžou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="ee79f-393">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="ee79f-394">Maximální počet položek, které lze uložit do vyrovnávací paměti pro datové proudy pro odeslání klienta.</span><span class="sxs-lookup"><span data-stu-id="ee79f-394">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="ee79f-395">Pokud je dosaženo tohoto limitu, zpracování volání je blokováno, dokud server nezpracovává položky datového proudu.</span><span class="sxs-lookup"><span data-stu-id="ee79f-395">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="ee79f-396">32 KB</span><span class="sxs-lookup"><span data-stu-id="ee79f-396">32 KB</span></span> | <span data-ttu-id="ee79f-397">Maximální velikost jedné příchozí zprávy centra</span><span class="sxs-lookup"><span data-stu-id="ee79f-397">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="ee79f-398">Možnosti lze nakonfigurovat pro všechna centra tím, že poskytnete možnosti delegáta pro `AddSignalR` volání v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="ee79f-398">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="ee79f-399">Možnosti pro jeden rozbočovač přepíší globální možnosti poskytované v `AddSignalR` a můžou být nakonfigurované pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="ee79f-399">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="ee79f-400">Rozšířené možnosti konfigurace protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="ee79f-400">Advanced HTTP configuration options</span></span>

<span data-ttu-id="ee79f-401">Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="ee79f-401">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="ee79f-402">Tyto možnosti jsou nakonfigurovány předáním delegáta [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="ee79f-402">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="ee79f-403">Následující tabulka popisuje možnosti konfigurace SignalR rozšířených možností protokolu HTTP v ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="ee79f-403">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="ee79f-404">Možnost</span><span class="sxs-lookup"><span data-stu-id="ee79f-404">Option</span></span> | <span data-ttu-id="ee79f-405">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-405">Default Value</span></span> | <span data-ttu-id="ee79f-406">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-406">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="ee79f-407">32 KB</span><span class="sxs-lookup"><span data-stu-id="ee79f-407">32 KB</span></span> | <span data-ttu-id="ee79f-408">Maximální počet bajtů přijatých od klienta, které jsou ukládány do vyrovnávací paměti serveru před použitím protitlaku.</span><span class="sxs-lookup"><span data-stu-id="ee79f-408">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="ee79f-409">Zvýšením této hodnoty umožníte, aby server přijímal větší zprávy rychleji bez použití zatížení, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="ee79f-409">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="ee79f-410">Data se automaticky shromažďují z `Authorize` atributů použitých pro třídu centra.</span><span class="sxs-lookup"><span data-stu-id="ee79f-410">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="ee79f-411">Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru</span><span class="sxs-lookup"><span data-stu-id="ee79f-411">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="ee79f-412">32 KB</span><span class="sxs-lookup"><span data-stu-id="ee79f-412">32 KB</span></span> | <span data-ttu-id="ee79f-413">Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru před pozorováním protitlaku.</span><span class="sxs-lookup"><span data-stu-id="ee79f-413">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="ee79f-414">Zvýšením této hodnoty umožníte serveru rychleji ukládat větší zprávy bez nutnosti očekávat zatížení, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="ee79f-414">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="ee79f-415">Všechny přenosy jsou povolené.</span><span class="sxs-lookup"><span data-stu-id="ee79f-415">All Transports are enabled.</span></span> | <span data-ttu-id="ee79f-416">Bitové příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenos, který může klient použít pro připojení.</span><span class="sxs-lookup"><span data-stu-id="ee79f-416">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="ee79f-417">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="ee79f-417">See below.</span></span> | <span data-ttu-id="ee79f-418">Další možnosti specifické pro přenos dlouhého cyklického dotazování.</span><span class="sxs-lookup"><span data-stu-id="ee79f-418">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="ee79f-419">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="ee79f-419">See below.</span></span> | <span data-ttu-id="ee79f-420">Další možnosti specifické pro přenos pomocí protokolu WebSockets</span><span class="sxs-lookup"><span data-stu-id="ee79f-420">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="ee79f-421">0</span><span class="sxs-lookup"><span data-stu-id="ee79f-421">0</span></span> | <span data-ttu-id="ee79f-422">Zadejte minimální verzi protokolu Negotiate.</span><span class="sxs-lookup"><span data-stu-id="ee79f-422">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="ee79f-423">Slouží k omezení klientů na novější verze.</span><span class="sxs-lookup"><span data-stu-id="ee79f-423">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="ee79f-424">Přenos dlouhého cyklického dotazování má další možnosti, které je možné konfigurovat pomocí `LongPolling` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="ee79f-424">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="ee79f-425">Možnost</span><span class="sxs-lookup"><span data-stu-id="ee79f-425">Option</span></span> | <span data-ttu-id="ee79f-426">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-426">Default Value</span></span> | <span data-ttu-id="ee79f-427">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-427">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="ee79f-428">90 sekund</span><span class="sxs-lookup"><span data-stu-id="ee79f-428">90 seconds</span></span> | <span data-ttu-id="ee79f-429">Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jedné žádosti o cyklické dotazování.</span><span class="sxs-lookup"><span data-stu-id="ee79f-429">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="ee79f-430">Snížení této hodnoty způsobí, že klient bude vydávat nové požadavky na dotaz častěji.</span><span class="sxs-lookup"><span data-stu-id="ee79f-430">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="ee79f-431">Přenos protokolu WebSocket má další možnosti, které je možné konfigurovat pomocí `WebSockets` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="ee79f-431">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="ee79f-432">Možnost</span><span class="sxs-lookup"><span data-stu-id="ee79f-432">Option</span></span> | <span data-ttu-id="ee79f-433">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-433">Default Value</span></span> | <span data-ttu-id="ee79f-434">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-434">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="ee79f-435">5 sekund</span><span class="sxs-lookup"><span data-stu-id="ee79f-435">5 seconds</span></span> | <span data-ttu-id="ee79f-436">Pokud se po ukončení serveru aplikace v tomto časovém intervalu nepovede zavřít, připojení se ukončí.</span><span class="sxs-lookup"><span data-stu-id="ee79f-436">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="ee79f-437">Delegát, který lze použít k nastavení `Sec-WebSocket-Protocol` záhlaví na vlastní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="ee79f-437">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="ee79f-438">Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že se vrátí požadovaná hodnota.</span><span class="sxs-lookup"><span data-stu-id="ee79f-438">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="ee79f-439">Konfigurace možností klienta</span><span class="sxs-lookup"><span data-stu-id="ee79f-439">Configure client options</span></span>

<span data-ttu-id="ee79f-440">Možnosti klienta lze nakonfigurovat pro `HubConnectionBuilder` typ (k dispozici v klientech rozhraní .NET a v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="ee79f-440">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="ee79f-441">Je také k dispozici v klientovi Java, ale `HttpHubConnectionBuilder` podtřídou je to, co obsahuje možnosti konfigurace tvůrce a také na `HubConnection` sebe sama.</span><span class="sxs-lookup"><span data-stu-id="ee79f-441">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="ee79f-442">Konfigurovat protokolování</span><span class="sxs-lookup"><span data-stu-id="ee79f-442">Configure logging</span></span>

<span data-ttu-id="ee79f-443">Protokolování je konfigurováno v klientovi .NET pomocí `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="ee79f-443">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="ee79f-444">Zprostředkovatelé protokolování a filtry je možné zaregistrovat stejným způsobem jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="ee79f-444">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="ee79f-445">Další informace najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="ee79f-445">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="ee79f-446">Aby bylo možné registrovat poskytovatele protokolování, je nutné nainstalovat potřebné balíčky.</span><span class="sxs-lookup"><span data-stu-id="ee79f-446">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="ee79f-447">Úplný seznam najdete v části [předdefinovaná zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="ee79f-447">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="ee79f-448">Chcete-li například povolit protokolování konzoly, nainstalujte `Microsoft.Extensions.Logging.Console` balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="ee79f-448">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="ee79f-449">Zavolejte `AddConsole` metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="ee79f-449">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="ee79f-450">V klientovi jazyka JavaScript existuje podobná `configureLogging` metoda.</span><span class="sxs-lookup"><span data-stu-id="ee79f-450">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="ee79f-451">Zadejte `LogLevel` hodnotu, která určuje minimální úroveň zpráv protokolu, které mají být vyprodukovány.</span><span class="sxs-lookup"><span data-stu-id="ee79f-451">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="ee79f-452">Protokoly se zapisují do okna konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="ee79f-452">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="ee79f-453">Místo `LogLevel` hodnoty můžete zadat také `string` hodnotu představující název úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="ee79f-453">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="ee79f-454">To je užitečné při konfiguraci SignalR protokolování v prostředích, kde nemáte přístup k `LogLevel` konstantám.</span><span class="sxs-lookup"><span data-stu-id="ee79f-454">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="ee79f-455">V následující tabulce jsou uvedeny dostupné úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="ee79f-455">The following table lists the available log levels.</span></span> <span data-ttu-id="ee79f-456">Hodnota, kterou zadáte, `configureLogging` nastaví **minimální** úroveň protokolu, která se bude protokolovat.</span><span class="sxs-lookup"><span data-stu-id="ee79f-456">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="ee79f-457">Zprávy zaznamenané na této úrovni **nebo úrovně uvedené za ní v tabulce**budou protokolovány.</span><span class="sxs-lookup"><span data-stu-id="ee79f-457">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="ee79f-458">Řetězec</span><span class="sxs-lookup"><span data-stu-id="ee79f-458">String</span></span>                      | <span data-ttu-id="ee79f-459">LogLevel</span><span class="sxs-lookup"><span data-stu-id="ee79f-459">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="ee79f-460">`info` **nebo** `information`</span><span class="sxs-lookup"><span data-stu-id="ee79f-460">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="ee79f-461">`warn` **nebo** `warning`</span><span class="sxs-lookup"><span data-stu-id="ee79f-461">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="ee79f-462">Chcete-li protokolování zcela zakázat, zadejte `signalR.LogLevel.None` v `configureLogging` metodě.</span><span class="sxs-lookup"><span data-stu-id="ee79f-462">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="ee79f-463">Další informace o protokolování naleznete v dokumentaci k [ SignalR diagnostice](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="ee79f-463">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="ee79f-464">SignalRKlient Java používá k protokolování knihovnu [SLF4J](https://www.slf4j.org/) .</span><span class="sxs-lookup"><span data-stu-id="ee79f-464">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="ee79f-465">Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí.</span><span class="sxs-lookup"><span data-stu-id="ee79f-465">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="ee79f-466">Následující fragment kódu ukazuje, jak používat `java.util.logging` s SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="ee79f-466">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="ee79f-467">Pokud ve svých závislostech nenakonfigurujete protokolování, SLF4J načte výchozí protokolovací nástroj No-operation s následující zprávou upozornění:</span><span class="sxs-lookup"><span data-stu-id="ee79f-467">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="ee79f-468">Tuto možnost lze bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="ee79f-468">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="ee79f-469">Konfigurace povolených přenosů</span><span class="sxs-lookup"><span data-stu-id="ee79f-469">Configure allowed transports</span></span>

<span data-ttu-id="ee79f-470">Přenosy používané nástrojem SignalR lze konfigurovat ve `WithUrl` volání ( `withUrl` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="ee79f-470">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="ee79f-471">Bitové nebo hodnoty `HttpTransportType` lze použít k omezení klienta na používání pouze zadaných přenosů.</span><span class="sxs-lookup"><span data-stu-id="ee79f-471">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="ee79f-472">Ve výchozím nastavení jsou povolené všechny přenosy.</span><span class="sxs-lookup"><span data-stu-id="ee79f-472">All transports are enabled by default.</span></span>

<span data-ttu-id="ee79f-473">Pokud třeba chcete zakázat přenos událostí odeslaných serverem, ale povolit WebSockets a dlouhá připojení s dotazem:</span><span class="sxs-lookup"><span data-stu-id="ee79f-473">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="ee79f-474">V klientu jazyka JavaScript jsou přenosy konfigurovány nastavením `transport` pole v objektu Options, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="ee79f-474">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="ee79f-475">V této verzi je jediným dostupným přenosem klientský WebSocket v jazyce Java.</span><span class="sxs-lookup"><span data-stu-id="ee79f-475">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="ee79f-476">V klientovi Java se přenos vybere s `withTransport` metodou na `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="ee79f-476">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="ee79f-477">Klient Java standardně používá přenos pomocí protokolu WebSockets.</span><span class="sxs-lookup"><span data-stu-id="ee79f-477">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="ee79f-478">SignalRKlient Java ještě nepodporuje přenosovou zálohu.</span><span class="sxs-lookup"><span data-stu-id="ee79f-478">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="ee79f-479">Konfigurace ověřování nosiče</span><span class="sxs-lookup"><span data-stu-id="ee79f-479">Configure bearer authentication</span></span>

<span data-ttu-id="ee79f-480">K poskytnutí ověřovacích dat spolu s SignalR požadavky použijte `AccessTokenProvider` možnost ( `accessTokenFactory` v jazyce JavaScript) k určení funkce, která vrací požadovaný přístupový token.</span><span class="sxs-lookup"><span data-stu-id="ee79f-480">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="ee79f-481">V klientovi .NET se tento přístupový token předává jako token ověřování HTTP "Bearer" (s použitím `Authorization` hlavičky s typem `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="ee79f-481">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="ee79f-482">V klientu jazyka JavaScript se přístupový token používá jako nosný token, **s výjimkou** případů, kdy rozhraní API prohlížeče omezuje možnost použít hlavičky (konkrétně v požadavcích na události odeslané serverem a objekty WebSockets).</span><span class="sxs-lookup"><span data-stu-id="ee79f-482">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="ee79f-483">V těchto případech je přístupový token k dispozici jako hodnota řetězce dotazu `access_token` .</span><span class="sxs-lookup"><span data-stu-id="ee79f-483">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="ee79f-484">V klientu .NET `AccessTokenProvider` lze možnost zadat pomocí delegáta možnosti v `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="ee79f-484">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="ee79f-485">V klientovi JavaScriptu je přístupový token nakonfigurovaný nastavením `accessTokenFactory` pole v objektu Options v `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="ee79f-485">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="ee79f-486">V SignalR klientovi Java můžete nakonfigurovat nosný token, který se bude používat pro ověřování, a to poskytnutím továrny přístupového tokenu [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="ee79f-486">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="ee79f-487">Pomocí [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) můžete zadat [RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="ee79f-487">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="ee79f-488">Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.</span><span class="sxs-lookup"><span data-stu-id="ee79f-488">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="ee79f-489">Konfigurace možností timeout a Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="ee79f-489">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="ee79f-490">Další možnosti konfigurace časového limitu a chování funkce Keep-Alive jsou k dispozici na `HubConnection` samotném objektu:</span><span class="sxs-lookup"><span data-stu-id="ee79f-490">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="ee79f-491">.NET</span><span class="sxs-lookup"><span data-stu-id="ee79f-491">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="ee79f-492">Možnost</span><span class="sxs-lookup"><span data-stu-id="ee79f-492">Option</span></span> | <span data-ttu-id="ee79f-493">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-493">Default value</span></span> | <span data-ttu-id="ee79f-494">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-494">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="ee79f-495">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="ee79f-495">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="ee79f-496">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="ee79f-496">Timeout for server activity.</span></span> <span data-ttu-id="ee79f-497">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí `Closed` událost ( `onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="ee79f-497">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="ee79f-498">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="ee79f-498">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="ee79f-499">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="ee79f-499">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="ee79f-500">15 sekund</span><span class="sxs-lookup"><span data-stu-id="ee79f-500">15 seconds</span></span> | <span data-ttu-id="ee79f-501">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="ee79f-501">Timeout for initial server handshake.</span></span> <span data-ttu-id="ee79f-502">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a spustí `Closed` událost ( `onclose` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="ee79f-502">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="ee79f-503">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="ee79f-503">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="ee79f-504">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="ee79f-504">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="ee79f-505">15 sekund</span><span class="sxs-lookup"><span data-stu-id="ee79f-505">15 seconds</span></span> | <span data-ttu-id="ee79f-506">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="ee79f-506">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="ee79f-507">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="ee79f-507">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="ee79f-508">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="ee79f-508">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="ee79f-509">V klientovi .NET jsou hodnoty časového limitu zadány jako `TimeSpan` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="ee79f-509">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="ee79f-510">JavaScript</span><span class="sxs-lookup"><span data-stu-id="ee79f-510">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="ee79f-511">Možnost</span><span class="sxs-lookup"><span data-stu-id="ee79f-511">Option</span></span> | <span data-ttu-id="ee79f-512">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-512">Default value</span></span> | <span data-ttu-id="ee79f-513">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-513">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="ee79f-514">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="ee79f-514">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="ee79f-515">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="ee79f-515">Timeout for server activity.</span></span> <span data-ttu-id="ee79f-516">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onclose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="ee79f-516">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="ee79f-517">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="ee79f-517">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="ee79f-518">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="ee79f-518">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="ee79f-519">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="ee79f-519">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="ee79f-520">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="ee79f-520">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="ee79f-521">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="ee79f-521">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="ee79f-522">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="ee79f-522">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="ee79f-523">Java</span><span class="sxs-lookup"><span data-stu-id="ee79f-523">Java</span></span>](#tab/java)

| <span data-ttu-id="ee79f-524">Možnost</span><span class="sxs-lookup"><span data-stu-id="ee79f-524">Option</span></span> | <span data-ttu-id="ee79f-525">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-525">Default value</span></span> | <span data-ttu-id="ee79f-526">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-526">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="ee79f-527">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="ee79f-527">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="ee79f-528">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="ee79f-528">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="ee79f-529">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="ee79f-529">Timeout for server activity.</span></span> <span data-ttu-id="ee79f-530">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onClose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="ee79f-530">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="ee79f-531">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="ee79f-531">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="ee79f-532">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="ee79f-532">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="ee79f-533">15 sekund</span><span class="sxs-lookup"><span data-stu-id="ee79f-533">15 seconds</span></span> | <span data-ttu-id="ee79f-534">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="ee79f-534">Timeout for initial server handshake.</span></span> <span data-ttu-id="ee79f-535">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje `onClose` událost.</span><span class="sxs-lookup"><span data-stu-id="ee79f-535">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="ee79f-536">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="ee79f-536">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="ee79f-537">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="ee79f-537">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="ee79f-538">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="ee79f-538">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="ee79f-539">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="ee79f-539">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="ee79f-540">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="ee79f-540">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="ee79f-541">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="ee79f-541">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="ee79f-542">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="ee79f-542">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="ee79f-543">Konfigurace dalších možností</span><span class="sxs-lookup"><span data-stu-id="ee79f-543">Configure additional options</span></span>

<span data-ttu-id="ee79f-544">Další možnosti lze konfigurovat v `WithUrl` `withUrl` metodě (v jazyce JavaScript) na `HubConnectionBuilder` různých konfiguračních rozhraních API v `HttpHubConnectionBuilder` klientovi Java:</span><span class="sxs-lookup"><span data-stu-id="ee79f-544">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="ee79f-545">.NET</span><span class="sxs-lookup"><span data-stu-id="ee79f-545">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="ee79f-546">Možnost .NET</span><span class="sxs-lookup"><span data-stu-id="ee79f-546">.NET Option</span></span> |  <span data-ttu-id="ee79f-547">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-547">Default value</span></span> | <span data-ttu-id="ee79f-548">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-548">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="ee79f-549">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="ee79f-549">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="ee79f-550">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="ee79f-550">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="ee79f-551">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="ee79f-551">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="ee79f-552">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="ee79f-552">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="ee79f-553">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="ee79f-553">Empty</span></span> | <span data-ttu-id="ee79f-554">Kolekce certifikátů TLS pro odeslání na požadavky na ověření.</span><span class="sxs-lookup"><span data-stu-id="ee79f-554">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="ee79f-555">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="ee79f-555">Empty</span></span> | <span data-ttu-id="ee79f-556">Kolekce souborů cookie protokolu HTTP, které se mají odeslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="ee79f-556">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="ee79f-557">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="ee79f-557">Empty</span></span> | <span data-ttu-id="ee79f-558">Přihlašovací údaje, které se mají poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="ee79f-558">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="ee79f-559">5 sekund</span><span class="sxs-lookup"><span data-stu-id="ee79f-559">5 seconds</span></span> | <span data-ttu-id="ee79f-560">Jenom objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="ee79f-560">WebSockets only.</span></span> <span data-ttu-id="ee79f-561">Maximální doba, po jejímž uplynutí bude klient čekat na ukončení žádosti o uzavření.</span><span class="sxs-lookup"><span data-stu-id="ee79f-561">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="ee79f-562">Pokud server nepotvrdí zavření v této době, klient se odpojí.</span><span class="sxs-lookup"><span data-stu-id="ee79f-562">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="ee79f-563">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="ee79f-563">Empty</span></span> | <span data-ttu-id="ee79f-564">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="ee79f-564">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="ee79f-565">Delegát, který lze použít ke konfiguraci nebo nahrazení `HttpMessageHandler` používaného k odeslání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="ee79f-565">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="ee79f-566">Nepoužívá se pro připojení pomocí protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="ee79f-566">Not used for WebSocket connections.</span></span> <span data-ttu-id="ee79f-567">Tento delegát musí vracet hodnotu, která není null, a obdrží výchozí hodnotu jako parametr.</span><span class="sxs-lookup"><span data-stu-id="ee79f-567">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="ee79f-568">Buď změňte nastavení u této výchozí hodnoty a vraťte je, nebo vraťte novou `HttpMessageHandler` instanci.</span><span class="sxs-lookup"><span data-stu-id="ee79f-568">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="ee79f-569">**Při nahrazování obslužné rutiny Nezapomeňte zkopírovat nastavení, která chcete zachovat, od poskytnuté obslužné rutiny, jinak konfigurované možnosti (například soubory cookie a záhlaví) nebudou použity pro novou obslužnou rutinu.**</span><span class="sxs-lookup"><span data-stu-id="ee79f-569">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="ee79f-570">Proxy server HTTP, který se má použít při odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="ee79f-570">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="ee79f-571">Nastavte tuto logickou hodnotu pro odeslání výchozích přihlašovacích údajů pro požadavky HTTP a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="ee79f-571">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="ee79f-572">To umožňuje použití ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="ee79f-572">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="ee79f-573">Delegát, který lze použít ke konfiguraci dalších možností protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="ee79f-573">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="ee79f-574">Přijímá instanci [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , která se dá použít ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="ee79f-574">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="ee79f-575">JavaScript</span><span class="sxs-lookup"><span data-stu-id="ee79f-575">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="ee79f-576">Možnost JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="ee79f-576">JavaScript Option</span></span> | <span data-ttu-id="ee79f-577">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-577">Default Value</span></span> | <span data-ttu-id="ee79f-578">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-578">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="ee79f-579">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="ee79f-579">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="ee79f-580">Nastavte na `true` protokolování bajtů/znaků zpráv odesílaných a přijímaných klientem.</span><span class="sxs-lookup"><span data-stu-id="ee79f-580">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="ee79f-581">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="ee79f-581">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="ee79f-582">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="ee79f-582">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="ee79f-583">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="ee79f-583">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="ee79f-584">Java</span><span class="sxs-lookup"><span data-stu-id="ee79f-584">Java</span></span>](#tab/java)

| <span data-ttu-id="ee79f-585">Možnost jazyka Java</span><span class="sxs-lookup"><span data-stu-id="ee79f-585">Java Option</span></span> | <span data-ttu-id="ee79f-586">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-586">Default Value</span></span> | <span data-ttu-id="ee79f-587">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-587">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="ee79f-588">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="ee79f-588">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="ee79f-589">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="ee79f-589">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="ee79f-590">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="ee79f-590">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="ee79f-591">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="ee79f-591">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="ee79f-592">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="ee79f-592">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="ee79f-593">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="ee79f-593">Empty</span></span> | <span data-ttu-id="ee79f-594">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="ee79f-594">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="ee79f-595">V klientu .NET můžete tyto možnosti upravit pomocí delegáta možností, který je k dispozici pro `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="ee79f-595">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="ee79f-596">V klientovi JavaScriptu lze tyto možnosti poskytnout v objektu jazyka JavaScript, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="ee79f-596">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="ee79f-597">V klientovi Java lze tyto možnosti nakonfigurovat s metodami `HttpHubConnectionBuilder` vrácenými z`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="ee79f-597">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="ee79f-598">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="ee79f-598">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="ee79f-599">Možnosti serializace JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="ee79f-599">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="ee79f-600">ASP.NET Core SignalR podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="ee79f-600">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="ee79f-601">Každý protokol má možnosti konfigurace serializace.</span><span class="sxs-lookup"><span data-stu-id="ee79f-601">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="ee79f-602">Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="ee79f-602">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="ee79f-603">`AddJsonProtocol`lze přidat po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="ee79f-603">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="ee79f-604">`AddJsonProtocol`Metoda přebírá delegáta, který přijímá `options` objekt.</span><span class="sxs-lookup"><span data-stu-id="ee79f-604">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="ee79f-605">Vlastnost [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) tohoto objektu je `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objekt, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="ee79f-605">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="ee79f-606">Další informace najdete v [System.Text.Jsv dokumentaci](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="ee79f-606">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="ee79f-607">Chcete-li například nakonfigurovat serializátor, aby neměnil velká a malá písmena názvů vlastností namísto výchozích názvů "camelCase", použijte následující kód v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ee79f-607">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="ee79f-608">V klientovi .NET `AddJsonProtocol` existuje stejná rozšiřující metoda v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="ee79f-608">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="ee79f-609">`Microsoft.Extensions.DependencyInjection`Obor názvů musí být importován, aby bylo možné přeložit metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="ee79f-609">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="ee79f-610">V tuto chvíli není možné konfigurovat serializaci JSON v klientovi jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ee79f-610">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="ee79f-611">Přepnout na Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="ee79f-611">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="ee79f-612">Pokud potřebujete funkce nástroje `Newtonsoft.Json` , které nejsou podporované v systému `System.Text.Json` , přečtěte si téma [Přepnutí na Newtonsoft.Jsna](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="ee79f-612">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="ee79f-613">Možnosti serializace MessagePack</span><span class="sxs-lookup"><span data-stu-id="ee79f-613">MessagePack serialization options</span></span>

<span data-ttu-id="ee79f-614">Serializaci MessagePack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="ee79f-614">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="ee79f-615">Další podrobnosti najdete [v SignalR tématu MessagePack](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="ee79f-615">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="ee79f-616">V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi v jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ee79f-616">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="ee79f-617">Konfigurovat možnosti serveru</span><span class="sxs-lookup"><span data-stu-id="ee79f-617">Configure server options</span></span>

<span data-ttu-id="ee79f-618">Následující tabulka popisuje možnosti konfigurace SignalR Center:</span><span class="sxs-lookup"><span data-stu-id="ee79f-618">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="ee79f-619">Možnost</span><span class="sxs-lookup"><span data-stu-id="ee79f-619">Option</span></span> | <span data-ttu-id="ee79f-620">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-620">Default Value</span></span> | <span data-ttu-id="ee79f-621">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-621">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="ee79f-622">30 sekund</span><span class="sxs-lookup"><span data-stu-id="ee79f-622">30 seconds</span></span> | <span data-ttu-id="ee79f-623">Server bude předpokládat, že je klient odpojený, pokud v tomto intervalu neobdržel zprávu (včetně Keep-Alive).</span><span class="sxs-lookup"><span data-stu-id="ee79f-623">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="ee79f-624">Může trvat delší dobu, než je tento časový limit, aby bylo možné klienta ve skutečnosti označit jako odpojený, protože to je implementováno.</span><span class="sxs-lookup"><span data-stu-id="ee79f-624">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="ee79f-625">Doporučená hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="ee79f-625">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="ee79f-626">15 sekund</span><span class="sxs-lookup"><span data-stu-id="ee79f-626">15 seconds</span></span> | <span data-ttu-id="ee79f-627">Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře.</span><span class="sxs-lookup"><span data-stu-id="ee79f-627">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="ee79f-628">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="ee79f-628">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="ee79f-629">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="ee79f-629">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="ee79f-630">15 sekund</span><span class="sxs-lookup"><span data-stu-id="ee79f-630">15 seconds</span></span> | <span data-ttu-id="ee79f-631">Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="ee79f-631">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="ee79f-632">Při změně `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení změňte nastavení v klientovi.</span><span class="sxs-lookup"><span data-stu-id="ee79f-632">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="ee79f-633">Doporučená `ServerTimeout` / `serverTimeoutInMilliseconds` hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="ee79f-633">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="ee79f-634">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="ee79f-634">All installed protocols</span></span> | <span data-ttu-id="ee79f-635">Protokoly podporované tímto rozbočovačem</span><span class="sxs-lookup"><span data-stu-id="ee79f-635">Protocols supported by this hub.</span></span> <span data-ttu-id="ee79f-636">Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly.</span><span class="sxs-lookup"><span data-stu-id="ee79f-636">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="ee79f-637">Pokud se `true` do klientů vrátí podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="ee79f-637">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="ee79f-638">Výchozí hodnota je `false` , protože tyto zprávy o výjimce můžou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="ee79f-638">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="ee79f-639">Maximální počet položek, které lze uložit do vyrovnávací paměti pro datové proudy pro odeslání klienta.</span><span class="sxs-lookup"><span data-stu-id="ee79f-639">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="ee79f-640">Pokud je dosaženo tohoto limitu, zpracování volání je blokováno, dokud server nezpracovává položky datového proudu.</span><span class="sxs-lookup"><span data-stu-id="ee79f-640">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="ee79f-641">32 KB</span><span class="sxs-lookup"><span data-stu-id="ee79f-641">32 KB</span></span> | <span data-ttu-id="ee79f-642">Maximální velikost jedné příchozí zprávy centra</span><span class="sxs-lookup"><span data-stu-id="ee79f-642">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="ee79f-643">Možnosti lze nakonfigurovat pro všechna centra tím, že poskytnete možnosti delegáta pro `AddSignalR` volání v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="ee79f-643">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="ee79f-644">Možnosti pro jeden rozbočovač přepíší globální možnosti poskytované v `AddSignalR` a můžou být nakonfigurované pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="ee79f-644">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="ee79f-645">Rozšířené možnosti konfigurace protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="ee79f-645">Advanced HTTP configuration options</span></span>

<span data-ttu-id="ee79f-646">Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="ee79f-646">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="ee79f-647">Tyto možnosti jsou nakonfigurovány předáním delegáta [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="ee79f-647">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="ee79f-648">Následující tabulka popisuje možnosti konfigurace SignalR rozšířených možností protokolu HTTP v ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="ee79f-648">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="ee79f-649">Možnost</span><span class="sxs-lookup"><span data-stu-id="ee79f-649">Option</span></span> | <span data-ttu-id="ee79f-650">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-650">Default Value</span></span> | <span data-ttu-id="ee79f-651">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-651">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="ee79f-652">32 KB</span><span class="sxs-lookup"><span data-stu-id="ee79f-652">32 KB</span></span> | <span data-ttu-id="ee79f-653">Maximální počet bajtů přijatých od klienta, které jsou ukládány do vyrovnávací paměti serveru před použitím protitlaku.</span><span class="sxs-lookup"><span data-stu-id="ee79f-653">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="ee79f-654">Zvýšením této hodnoty umožníte, aby server přijímal větší zprávy rychleji bez použití zatížení, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="ee79f-654">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="ee79f-655">Data se automaticky shromažďují z `Authorize` atributů použitých pro třídu centra.</span><span class="sxs-lookup"><span data-stu-id="ee79f-655">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="ee79f-656">Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru</span><span class="sxs-lookup"><span data-stu-id="ee79f-656">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="ee79f-657">32 KB</span><span class="sxs-lookup"><span data-stu-id="ee79f-657">32 KB</span></span> | <span data-ttu-id="ee79f-658">Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru před pozorováním protitlaku.</span><span class="sxs-lookup"><span data-stu-id="ee79f-658">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="ee79f-659">Zvýšením této hodnoty umožníte serveru rychleji ukládat větší zprávy bez nutnosti očekávat zatížení, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="ee79f-659">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="ee79f-660">Všechny přenosy jsou povolené.</span><span class="sxs-lookup"><span data-stu-id="ee79f-660">All Transports are enabled.</span></span> | <span data-ttu-id="ee79f-661">Bitové příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenos, který může klient použít pro připojení.</span><span class="sxs-lookup"><span data-stu-id="ee79f-661">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="ee79f-662">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="ee79f-662">See below.</span></span> | <span data-ttu-id="ee79f-663">Další možnosti specifické pro přenos dlouhého cyklického dotazování.</span><span class="sxs-lookup"><span data-stu-id="ee79f-663">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="ee79f-664">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="ee79f-664">See below.</span></span> | <span data-ttu-id="ee79f-665">Další možnosti specifické pro přenos pomocí protokolu WebSockets</span><span class="sxs-lookup"><span data-stu-id="ee79f-665">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="ee79f-666">Přenos dlouhého cyklického dotazování má další možnosti, které je možné konfigurovat pomocí `LongPolling` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="ee79f-666">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="ee79f-667">Možnost</span><span class="sxs-lookup"><span data-stu-id="ee79f-667">Option</span></span> | <span data-ttu-id="ee79f-668">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-668">Default Value</span></span> | <span data-ttu-id="ee79f-669">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-669">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="ee79f-670">90 sekund</span><span class="sxs-lookup"><span data-stu-id="ee79f-670">90 seconds</span></span> | <span data-ttu-id="ee79f-671">Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jedné žádosti o cyklické dotazování.</span><span class="sxs-lookup"><span data-stu-id="ee79f-671">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="ee79f-672">Snížení této hodnoty způsobí, že klient bude vydávat nové požadavky na dotaz častěji.</span><span class="sxs-lookup"><span data-stu-id="ee79f-672">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="ee79f-673">Přenos protokolu WebSocket má další možnosti, které je možné konfigurovat pomocí `WebSockets` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="ee79f-673">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="ee79f-674">Možnost</span><span class="sxs-lookup"><span data-stu-id="ee79f-674">Option</span></span> | <span data-ttu-id="ee79f-675">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-675">Default Value</span></span> | <span data-ttu-id="ee79f-676">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-676">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="ee79f-677">5 sekund</span><span class="sxs-lookup"><span data-stu-id="ee79f-677">5 seconds</span></span> | <span data-ttu-id="ee79f-678">Pokud se po ukončení serveru aplikace v tomto časovém intervalu nepovede zavřít, připojení se ukončí.</span><span class="sxs-lookup"><span data-stu-id="ee79f-678">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="ee79f-679">Delegát, který lze použít k nastavení `Sec-WebSocket-Protocol` záhlaví na vlastní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="ee79f-679">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="ee79f-680">Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že se vrátí požadovaná hodnota.</span><span class="sxs-lookup"><span data-stu-id="ee79f-680">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="ee79f-681">Konfigurace možností klienta</span><span class="sxs-lookup"><span data-stu-id="ee79f-681">Configure client options</span></span>

<span data-ttu-id="ee79f-682">Možnosti klienta lze nakonfigurovat pro `HubConnectionBuilder` typ (k dispozici v klientech rozhraní .NET a v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="ee79f-682">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="ee79f-683">Je také k dispozici v klientovi Java, ale `HttpHubConnectionBuilder` podtřídou je to, co obsahuje možnosti konfigurace tvůrce a také na `HubConnection` sebe sama.</span><span class="sxs-lookup"><span data-stu-id="ee79f-683">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="ee79f-684">Konfigurovat protokolování</span><span class="sxs-lookup"><span data-stu-id="ee79f-684">Configure logging</span></span>

<span data-ttu-id="ee79f-685">Protokolování je konfigurováno v klientovi .NET pomocí `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="ee79f-685">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="ee79f-686">Zprostředkovatelé protokolování a filtry je možné zaregistrovat stejným způsobem jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="ee79f-686">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="ee79f-687">Další informace najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="ee79f-687">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="ee79f-688">Aby bylo možné registrovat poskytovatele protokolování, je nutné nainstalovat potřebné balíčky.</span><span class="sxs-lookup"><span data-stu-id="ee79f-688">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="ee79f-689">Úplný seznam najdete v části [předdefinovaná zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="ee79f-689">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="ee79f-690">Chcete-li například povolit protokolování konzoly, nainstalujte `Microsoft.Extensions.Logging.Console` balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="ee79f-690">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="ee79f-691">Zavolejte `AddConsole` metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="ee79f-691">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="ee79f-692">V klientovi jazyka JavaScript existuje podobná `configureLogging` metoda.</span><span class="sxs-lookup"><span data-stu-id="ee79f-692">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="ee79f-693">Zadejte `LogLevel` hodnotu, která určuje minimální úroveň zpráv protokolu, které mají být vyprodukovány.</span><span class="sxs-lookup"><span data-stu-id="ee79f-693">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="ee79f-694">Protokoly se zapisují do okna konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="ee79f-694">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="ee79f-695">Místo `LogLevel` hodnoty můžete zadat také `string` hodnotu představující název úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="ee79f-695">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="ee79f-696">To je užitečné při konfiguraci SignalR protokolování v prostředích, kde nemáte přístup k `LogLevel` konstantám.</span><span class="sxs-lookup"><span data-stu-id="ee79f-696">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="ee79f-697">V následující tabulce jsou uvedeny dostupné úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="ee79f-697">The following table lists the available log levels.</span></span> <span data-ttu-id="ee79f-698">Hodnota, kterou zadáte, `configureLogging` nastaví **minimální** úroveň protokolu, která se bude protokolovat.</span><span class="sxs-lookup"><span data-stu-id="ee79f-698">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="ee79f-699">Zprávy zaznamenané na této úrovni **nebo úrovně uvedené za ní v tabulce**budou protokolovány.</span><span class="sxs-lookup"><span data-stu-id="ee79f-699">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="ee79f-700">Řetězec</span><span class="sxs-lookup"><span data-stu-id="ee79f-700">String</span></span>                      | <span data-ttu-id="ee79f-701">LogLevel</span><span class="sxs-lookup"><span data-stu-id="ee79f-701">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="ee79f-702">`info` **nebo** `information`</span><span class="sxs-lookup"><span data-stu-id="ee79f-702">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="ee79f-703">`warn` **nebo** `warning`</span><span class="sxs-lookup"><span data-stu-id="ee79f-703">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="ee79f-704">Chcete-li protokolování zcela zakázat, zadejte `signalR.LogLevel.None` v `configureLogging` metodě.</span><span class="sxs-lookup"><span data-stu-id="ee79f-704">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="ee79f-705">Další informace o protokolování naleznete v dokumentaci k [ SignalR diagnostice](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="ee79f-705">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="ee79f-706">SignalRKlient Java používá k protokolování knihovnu [SLF4J](https://www.slf4j.org/) .</span><span class="sxs-lookup"><span data-stu-id="ee79f-706">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="ee79f-707">Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí.</span><span class="sxs-lookup"><span data-stu-id="ee79f-707">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="ee79f-708">Následující fragment kódu ukazuje, jak používat `java.util.logging` s SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="ee79f-708">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="ee79f-709">Pokud ve svých závislostech nenakonfigurujete protokolování, SLF4J načte výchozí protokolovací nástroj No-operation s následující zprávou upozornění:</span><span class="sxs-lookup"><span data-stu-id="ee79f-709">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="ee79f-710">Tuto možnost lze bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="ee79f-710">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="ee79f-711">Konfigurace povolených přenosů</span><span class="sxs-lookup"><span data-stu-id="ee79f-711">Configure allowed transports</span></span>

<span data-ttu-id="ee79f-712">Přenosy používané nástrojem SignalR lze konfigurovat ve `WithUrl` volání ( `withUrl` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="ee79f-712">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="ee79f-713">Bitové nebo hodnoty `HttpTransportType` lze použít k omezení klienta na používání pouze zadaných přenosů.</span><span class="sxs-lookup"><span data-stu-id="ee79f-713">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="ee79f-714">Ve výchozím nastavení jsou povolené všechny přenosy.</span><span class="sxs-lookup"><span data-stu-id="ee79f-714">All transports are enabled by default.</span></span>

<span data-ttu-id="ee79f-715">Pokud třeba chcete zakázat přenos událostí odeslaných serverem, ale povolit WebSockets a dlouhá připojení s dotazem:</span><span class="sxs-lookup"><span data-stu-id="ee79f-715">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="ee79f-716">V klientu jazyka JavaScript jsou přenosy konfigurovány nastavením `transport` pole v objektu Options, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="ee79f-716">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="ee79f-717">V této verzi je jediným dostupným přenosem klientský WebSocket v jazyce Java.</span><span class="sxs-lookup"><span data-stu-id="ee79f-717">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="ee79f-718">V klientovi Java se přenos vybere s `withTransport` metodou na `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="ee79f-718">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="ee79f-719">Klient Java standardně používá přenos pomocí protokolu WebSockets.</span><span class="sxs-lookup"><span data-stu-id="ee79f-719">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="ee79f-720">SignalRKlient Java ještě nepodporuje přenosovou zálohu.</span><span class="sxs-lookup"><span data-stu-id="ee79f-720">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="ee79f-721">Konfigurace ověřování nosiče</span><span class="sxs-lookup"><span data-stu-id="ee79f-721">Configure bearer authentication</span></span>

<span data-ttu-id="ee79f-722">K poskytnutí ověřovacích dat spolu s SignalR požadavky použijte `AccessTokenProvider` možnost ( `accessTokenFactory` v jazyce JavaScript) k určení funkce, která vrací požadovaný přístupový token.</span><span class="sxs-lookup"><span data-stu-id="ee79f-722">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="ee79f-723">V klientovi .NET se tento přístupový token předává jako token ověřování HTTP "Bearer" (s použitím `Authorization` hlavičky s typem `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="ee79f-723">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="ee79f-724">V klientu jazyka JavaScript se přístupový token používá jako nosný token, **s výjimkou** případů, kdy rozhraní API prohlížeče omezuje možnost použít hlavičky (konkrétně v požadavcích na události odeslané serverem a objekty WebSockets).</span><span class="sxs-lookup"><span data-stu-id="ee79f-724">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="ee79f-725">V těchto případech je přístupový token k dispozici jako hodnota řetězce dotazu `access_token` .</span><span class="sxs-lookup"><span data-stu-id="ee79f-725">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="ee79f-726">V klientu .NET `AccessTokenProvider` lze možnost zadat pomocí delegáta možnosti v `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="ee79f-726">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="ee79f-727">V klientovi JavaScriptu je přístupový token nakonfigurovaný nastavením `accessTokenFactory` pole v objektu Options v `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="ee79f-727">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="ee79f-728">V SignalR klientovi Java můžete nakonfigurovat nosný token, který se bude používat pro ověřování, a to poskytnutím továrny přístupového tokenu [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="ee79f-728">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="ee79f-729">Pomocí [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) můžete zadat [RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="ee79f-729">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="ee79f-730">Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.</span><span class="sxs-lookup"><span data-stu-id="ee79f-730">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="ee79f-731">Konfigurace možností timeout a Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="ee79f-731">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="ee79f-732">Další možnosti konfigurace časového limitu a chování funkce Keep-Alive jsou k dispozici na `HubConnection` samotném objektu:</span><span class="sxs-lookup"><span data-stu-id="ee79f-732">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="ee79f-733">.NET</span><span class="sxs-lookup"><span data-stu-id="ee79f-733">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="ee79f-734">Možnost</span><span class="sxs-lookup"><span data-stu-id="ee79f-734">Option</span></span> | <span data-ttu-id="ee79f-735">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-735">Default value</span></span> | <span data-ttu-id="ee79f-736">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-736">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="ee79f-737">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="ee79f-737">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="ee79f-738">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="ee79f-738">Timeout for server activity.</span></span> <span data-ttu-id="ee79f-739">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí `Closed` událost ( `onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="ee79f-739">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="ee79f-740">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="ee79f-740">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="ee79f-741">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="ee79f-741">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="ee79f-742">15 sekund</span><span class="sxs-lookup"><span data-stu-id="ee79f-742">15 seconds</span></span> | <span data-ttu-id="ee79f-743">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="ee79f-743">Timeout for initial server handshake.</span></span> <span data-ttu-id="ee79f-744">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a spustí `Closed` událost ( `onclose` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="ee79f-744">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="ee79f-745">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="ee79f-745">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="ee79f-746">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="ee79f-746">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="ee79f-747">15 sekund</span><span class="sxs-lookup"><span data-stu-id="ee79f-747">15 seconds</span></span> | <span data-ttu-id="ee79f-748">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="ee79f-748">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="ee79f-749">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="ee79f-749">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="ee79f-750">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="ee79f-750">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="ee79f-751">V klientovi .NET jsou hodnoty časového limitu zadány jako `TimeSpan` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="ee79f-751">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="ee79f-752">JavaScript</span><span class="sxs-lookup"><span data-stu-id="ee79f-752">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="ee79f-753">Možnost</span><span class="sxs-lookup"><span data-stu-id="ee79f-753">Option</span></span> | <span data-ttu-id="ee79f-754">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-754">Default value</span></span> | <span data-ttu-id="ee79f-755">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-755">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="ee79f-756">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="ee79f-756">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="ee79f-757">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="ee79f-757">Timeout for server activity.</span></span> <span data-ttu-id="ee79f-758">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onclose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="ee79f-758">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="ee79f-759">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="ee79f-759">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="ee79f-760">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="ee79f-760">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="ee79f-761">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="ee79f-761">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="ee79f-762">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="ee79f-762">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="ee79f-763">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="ee79f-763">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="ee79f-764">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="ee79f-764">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="ee79f-765">Java</span><span class="sxs-lookup"><span data-stu-id="ee79f-765">Java</span></span>](#tab/java)

| <span data-ttu-id="ee79f-766">Možnost</span><span class="sxs-lookup"><span data-stu-id="ee79f-766">Option</span></span> | <span data-ttu-id="ee79f-767">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-767">Default value</span></span> | <span data-ttu-id="ee79f-768">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-768">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="ee79f-769">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="ee79f-769">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="ee79f-770">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="ee79f-770">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="ee79f-771">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="ee79f-771">Timeout for server activity.</span></span> <span data-ttu-id="ee79f-772">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onClose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="ee79f-772">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="ee79f-773">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="ee79f-773">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="ee79f-774">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="ee79f-774">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="ee79f-775">15 sekund</span><span class="sxs-lookup"><span data-stu-id="ee79f-775">15 seconds</span></span> | <span data-ttu-id="ee79f-776">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="ee79f-776">Timeout for initial server handshake.</span></span> <span data-ttu-id="ee79f-777">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje `onClose` událost.</span><span class="sxs-lookup"><span data-stu-id="ee79f-777">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="ee79f-778">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="ee79f-778">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="ee79f-779">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="ee79f-779">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="ee79f-780">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="ee79f-780">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="ee79f-781">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="ee79f-781">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="ee79f-782">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="ee79f-782">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="ee79f-783">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="ee79f-783">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="ee79f-784">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="ee79f-784">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="ee79f-785">Konfigurace dalších možností</span><span class="sxs-lookup"><span data-stu-id="ee79f-785">Configure additional options</span></span>

<span data-ttu-id="ee79f-786">Další možnosti lze konfigurovat v `WithUrl` `withUrl` metodě (v jazyce JavaScript) na `HubConnectionBuilder` různých konfiguračních rozhraních API v `HttpHubConnectionBuilder` klientovi Java:</span><span class="sxs-lookup"><span data-stu-id="ee79f-786">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="ee79f-787">.NET</span><span class="sxs-lookup"><span data-stu-id="ee79f-787">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="ee79f-788">Možnost .NET</span><span class="sxs-lookup"><span data-stu-id="ee79f-788">.NET Option</span></span> |  <span data-ttu-id="ee79f-789">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-789">Default value</span></span> | <span data-ttu-id="ee79f-790">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-790">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="ee79f-791">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="ee79f-791">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="ee79f-792">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="ee79f-792">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="ee79f-793">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="ee79f-793">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="ee79f-794">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="ee79f-794">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="ee79f-795">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="ee79f-795">Empty</span></span> | <span data-ttu-id="ee79f-796">Kolekce certifikátů TLS pro odeslání na požadavky na ověření.</span><span class="sxs-lookup"><span data-stu-id="ee79f-796">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="ee79f-797">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="ee79f-797">Empty</span></span> | <span data-ttu-id="ee79f-798">Kolekce souborů cookie protokolu HTTP, které se mají odeslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="ee79f-798">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="ee79f-799">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="ee79f-799">Empty</span></span> | <span data-ttu-id="ee79f-800">Přihlašovací údaje, které se mají poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="ee79f-800">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="ee79f-801">5 sekund</span><span class="sxs-lookup"><span data-stu-id="ee79f-801">5 seconds</span></span> | <span data-ttu-id="ee79f-802">Jenom objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="ee79f-802">WebSockets only.</span></span> <span data-ttu-id="ee79f-803">Maximální doba, po jejímž uplynutí bude klient čekat na ukončení žádosti o uzavření.</span><span class="sxs-lookup"><span data-stu-id="ee79f-803">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="ee79f-804">Pokud server nepotvrdí zavření v této době, klient se odpojí.</span><span class="sxs-lookup"><span data-stu-id="ee79f-804">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="ee79f-805">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="ee79f-805">Empty</span></span> | <span data-ttu-id="ee79f-806">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="ee79f-806">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="ee79f-807">Delegát, který lze použít ke konfiguraci nebo nahrazení `HttpMessageHandler` používaného k odeslání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="ee79f-807">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="ee79f-808">Nepoužívá se pro připojení pomocí protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="ee79f-808">Not used for WebSocket connections.</span></span> <span data-ttu-id="ee79f-809">Tento delegát musí vracet hodnotu, která není null, a obdrží výchozí hodnotu jako parametr.</span><span class="sxs-lookup"><span data-stu-id="ee79f-809">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="ee79f-810">Buď změňte nastavení u této výchozí hodnoty a vraťte je, nebo vraťte novou `HttpMessageHandler` instanci.</span><span class="sxs-lookup"><span data-stu-id="ee79f-810">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="ee79f-811">**Při nahrazování obslužné rutiny Nezapomeňte zkopírovat nastavení, která chcete zachovat, od poskytnuté obslužné rutiny, jinak konfigurované možnosti (například soubory cookie a záhlaví) nebudou použity pro novou obslužnou rutinu.**</span><span class="sxs-lookup"><span data-stu-id="ee79f-811">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="ee79f-812">Proxy server HTTP, který se má použít při odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="ee79f-812">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="ee79f-813">Nastavte tuto logickou hodnotu pro odeslání výchozích přihlašovacích údajů pro požadavky HTTP a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="ee79f-813">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="ee79f-814">To umožňuje použití ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="ee79f-814">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="ee79f-815">Delegát, který lze použít ke konfiguraci dalších možností protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="ee79f-815">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="ee79f-816">Přijímá instanci [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , která se dá použít ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="ee79f-816">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="ee79f-817">JavaScript</span><span class="sxs-lookup"><span data-stu-id="ee79f-817">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="ee79f-818">Možnost JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="ee79f-818">JavaScript Option</span></span> | <span data-ttu-id="ee79f-819">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-819">Default Value</span></span> | <span data-ttu-id="ee79f-820">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-820">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="ee79f-821">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="ee79f-821">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="ee79f-822">Nastavte na `true` protokolování bajtů/znaků zpráv odesílaných a přijímaných klientem.</span><span class="sxs-lookup"><span data-stu-id="ee79f-822">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="ee79f-823">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="ee79f-823">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="ee79f-824">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="ee79f-824">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="ee79f-825">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="ee79f-825">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="ee79f-826">Java</span><span class="sxs-lookup"><span data-stu-id="ee79f-826">Java</span></span>](#tab/java)

| <span data-ttu-id="ee79f-827">Možnost jazyka Java</span><span class="sxs-lookup"><span data-stu-id="ee79f-827">Java Option</span></span> | <span data-ttu-id="ee79f-828">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-828">Default Value</span></span> | <span data-ttu-id="ee79f-829">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-829">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="ee79f-830">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="ee79f-830">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="ee79f-831">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="ee79f-831">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="ee79f-832">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="ee79f-832">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="ee79f-833">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="ee79f-833">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="ee79f-834">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="ee79f-834">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="ee79f-835">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="ee79f-835">Empty</span></span> | <span data-ttu-id="ee79f-836">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="ee79f-836">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="ee79f-837">V klientu .NET můžete tyto možnosti upravit pomocí delegáta možností, který je k dispozici pro `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="ee79f-837">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="ee79f-838">V klientovi JavaScriptu lze tyto možnosti poskytnout v objektu jazyka JavaScript, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="ee79f-838">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="ee79f-839">V klientovi Java lze tyto možnosti nakonfigurovat s metodami `HttpHubConnectionBuilder` vrácenými z`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="ee79f-839">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="ee79f-840">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="ee79f-840">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="ee79f-841">Možnosti serializace JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="ee79f-841">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="ee79f-842">ASP.NET Core SignalR podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="ee79f-842">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="ee79f-843">Každý protokol má možnosti konfigurace serializace.</span><span class="sxs-lookup"><span data-stu-id="ee79f-843">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="ee79f-844">Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , kterou lze přidat po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v `Startup.ConfigureServices` metodě.</span><span class="sxs-lookup"><span data-stu-id="ee79f-844">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="ee79f-845">`AddJsonProtocol`Metoda přebírá delegáta, který přijímá `options` objekt.</span><span class="sxs-lookup"><span data-stu-id="ee79f-845">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="ee79f-846">Vlastnost [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) tohoto objektu je `JsonSerializerSettings` objekt JSON.NET, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="ee79f-846">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="ee79f-847">Další informace najdete v [dokumentaci k JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="ee79f-847">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="ee79f-848">Chcete-li například nakonfigurovat serializátor pro použití názvů vlastností "PascalCase" namísto výchozích názvů "camelCase", použijte následující kód v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ee79f-848">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="ee79f-849">V klientovi .NET `AddJsonProtocol` existuje stejná rozšiřující metoda v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="ee79f-849">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="ee79f-850">`Microsoft.Extensions.DependencyInjection`Obor názvů musí být importován, aby bylo možné přeložit metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="ee79f-850">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="ee79f-851">V tuto chvíli není možné konfigurovat serializaci JSON v klientovi jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ee79f-851">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="ee79f-852">Možnosti serializace MessagePack</span><span class="sxs-lookup"><span data-stu-id="ee79f-852">MessagePack serialization options</span></span>

<span data-ttu-id="ee79f-853">Serializaci MessagePack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="ee79f-853">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="ee79f-854">Další podrobnosti najdete [v SignalR tématu MessagePack](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="ee79f-854">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="ee79f-855">V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi v jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ee79f-855">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="ee79f-856">Konfigurovat možnosti serveru</span><span class="sxs-lookup"><span data-stu-id="ee79f-856">Configure server options</span></span>

<span data-ttu-id="ee79f-857">Následující tabulka popisuje možnosti konfigurace SignalR Center:</span><span class="sxs-lookup"><span data-stu-id="ee79f-857">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="ee79f-858">Možnost</span><span class="sxs-lookup"><span data-stu-id="ee79f-858">Option</span></span> | <span data-ttu-id="ee79f-859">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-859">Default Value</span></span> | <span data-ttu-id="ee79f-860">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-860">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="ee79f-861">30 sekund</span><span class="sxs-lookup"><span data-stu-id="ee79f-861">30 seconds</span></span> | <span data-ttu-id="ee79f-862">Server bude předpokládat, že je klient odpojený, pokud v tomto intervalu neobdržel zprávu (včetně Keep-Alive).</span><span class="sxs-lookup"><span data-stu-id="ee79f-862">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="ee79f-863">Může trvat delší dobu, než je tento časový limit, aby bylo možné klienta ve skutečnosti označit jako odpojený, protože to je implementováno.</span><span class="sxs-lookup"><span data-stu-id="ee79f-863">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="ee79f-864">Doporučená hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="ee79f-864">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="ee79f-865">15 sekund</span><span class="sxs-lookup"><span data-stu-id="ee79f-865">15 seconds</span></span> | <span data-ttu-id="ee79f-866">Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře.</span><span class="sxs-lookup"><span data-stu-id="ee79f-866">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="ee79f-867">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="ee79f-867">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="ee79f-868">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="ee79f-868">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="ee79f-869">15 sekund</span><span class="sxs-lookup"><span data-stu-id="ee79f-869">15 seconds</span></span> | <span data-ttu-id="ee79f-870">Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="ee79f-870">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="ee79f-871">Při změně `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení změňte nastavení v klientovi.</span><span class="sxs-lookup"><span data-stu-id="ee79f-871">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="ee79f-872">Doporučená `ServerTimeout` / `serverTimeoutInMilliseconds` hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="ee79f-872">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="ee79f-873">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="ee79f-873">All installed protocols</span></span> | <span data-ttu-id="ee79f-874">Protokoly podporované tímto rozbočovačem</span><span class="sxs-lookup"><span data-stu-id="ee79f-874">Protocols supported by this hub.</span></span> <span data-ttu-id="ee79f-875">Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly.</span><span class="sxs-lookup"><span data-stu-id="ee79f-875">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="ee79f-876">Pokud se `true` do klientů vrátí podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="ee79f-876">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="ee79f-877">Výchozí hodnota je `false` , protože tyto zprávy o výjimce můžou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="ee79f-877">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="ee79f-878">Možnosti lze nakonfigurovat pro všechna centra tím, že poskytnete možnosti delegáta pro `AddSignalR` volání v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="ee79f-878">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="ee79f-879">Možnosti pro jeden rozbočovač přepíší globální možnosti poskytované v `AddSignalR` a můžou být nakonfigurované pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="ee79f-879">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="ee79f-880">Rozšířené možnosti konfigurace protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="ee79f-880">Advanced HTTP configuration options</span></span>

<span data-ttu-id="ee79f-881">Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="ee79f-881">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="ee79f-882">Tyto možnosti jsou nakonfigurovány předáním delegáta [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="ee79f-882">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="ee79f-883">Následující tabulka popisuje možnosti konfigurace SignalR rozšířených možností protokolu HTTP v ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="ee79f-883">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="ee79f-884">Možnost</span><span class="sxs-lookup"><span data-stu-id="ee79f-884">Option</span></span> | <span data-ttu-id="ee79f-885">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-885">Default Value</span></span> | <span data-ttu-id="ee79f-886">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-886">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="ee79f-887">32 KB</span><span class="sxs-lookup"><span data-stu-id="ee79f-887">32 KB</span></span> | <span data-ttu-id="ee79f-888">Maximální počet bajtů přijatých od klienta, které jsou vyrovnávací paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="ee79f-888">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="ee79f-889">Zvýšením této hodnoty umožníte serveru přijímat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="ee79f-889">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="ee79f-890">Data se automaticky shromažďují z `Authorize` atributů použitých pro třídu centra.</span><span class="sxs-lookup"><span data-stu-id="ee79f-890">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="ee79f-891">Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru</span><span class="sxs-lookup"><span data-stu-id="ee79f-891">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="ee79f-892">32 KB</span><span class="sxs-lookup"><span data-stu-id="ee79f-892">32 KB</span></span> | <span data-ttu-id="ee79f-893">Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="ee79f-893">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="ee79f-894">Zvýšením této hodnoty umožníte serveru odesílat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="ee79f-894">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="ee79f-895">Všechny přenosy jsou povolené.</span><span class="sxs-lookup"><span data-stu-id="ee79f-895">All Transports are enabled.</span></span> | <span data-ttu-id="ee79f-896">Bitové příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenos, který může klient použít pro připojení.</span><span class="sxs-lookup"><span data-stu-id="ee79f-896">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="ee79f-897">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="ee79f-897">See below.</span></span> | <span data-ttu-id="ee79f-898">Další možnosti specifické pro přenos dlouhého cyklického dotazování.</span><span class="sxs-lookup"><span data-stu-id="ee79f-898">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="ee79f-899">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="ee79f-899">See below.</span></span> | <span data-ttu-id="ee79f-900">Další možnosti specifické pro přenos pomocí protokolu WebSockets</span><span class="sxs-lookup"><span data-stu-id="ee79f-900">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="ee79f-901">Přenos dlouhého cyklického dotazování má další možnosti, které je možné konfigurovat pomocí `LongPolling` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="ee79f-901">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="ee79f-902">Možnost</span><span class="sxs-lookup"><span data-stu-id="ee79f-902">Option</span></span> | <span data-ttu-id="ee79f-903">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-903">Default Value</span></span> | <span data-ttu-id="ee79f-904">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-904">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="ee79f-905">90 sekund</span><span class="sxs-lookup"><span data-stu-id="ee79f-905">90 seconds</span></span> | <span data-ttu-id="ee79f-906">Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jedné žádosti o cyklické dotazování.</span><span class="sxs-lookup"><span data-stu-id="ee79f-906">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="ee79f-907">Snížení této hodnoty způsobí, že klient bude vydávat nové požadavky na dotaz častěji.</span><span class="sxs-lookup"><span data-stu-id="ee79f-907">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="ee79f-908">Přenos protokolu WebSocket má další možnosti, které je možné konfigurovat pomocí `WebSockets` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="ee79f-908">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="ee79f-909">Možnost</span><span class="sxs-lookup"><span data-stu-id="ee79f-909">Option</span></span> | <span data-ttu-id="ee79f-910">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-910">Default Value</span></span> | <span data-ttu-id="ee79f-911">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-911">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="ee79f-912">5 sekund</span><span class="sxs-lookup"><span data-stu-id="ee79f-912">5 seconds</span></span> | <span data-ttu-id="ee79f-913">Pokud se po ukončení serveru aplikace v tomto časovém intervalu nepovede zavřít, připojení se ukončí.</span><span class="sxs-lookup"><span data-stu-id="ee79f-913">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="ee79f-914">Delegát, který lze použít k nastavení `Sec-WebSocket-Protocol` záhlaví na vlastní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="ee79f-914">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="ee79f-915">Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že se vrátí požadovaná hodnota.</span><span class="sxs-lookup"><span data-stu-id="ee79f-915">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="ee79f-916">Konfigurace možností klienta</span><span class="sxs-lookup"><span data-stu-id="ee79f-916">Configure client options</span></span>

<span data-ttu-id="ee79f-917">Možnosti klienta lze nakonfigurovat pro `HubConnectionBuilder` typ (k dispozici v klientech rozhraní .NET a v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="ee79f-917">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="ee79f-918">Je také k dispozici v klientovi Java, ale `HttpHubConnectionBuilder` podtřídou je to, co obsahuje možnosti konfigurace tvůrce a také na `HubConnection` sebe sama.</span><span class="sxs-lookup"><span data-stu-id="ee79f-918">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="ee79f-919">Konfigurovat protokolování</span><span class="sxs-lookup"><span data-stu-id="ee79f-919">Configure logging</span></span>

<span data-ttu-id="ee79f-920">Protokolování je konfigurováno v klientovi .NET pomocí `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="ee79f-920">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="ee79f-921">Zprostředkovatelé protokolování a filtry je možné zaregistrovat stejným způsobem jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="ee79f-921">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="ee79f-922">Další informace najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="ee79f-922">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="ee79f-923">Aby bylo možné registrovat poskytovatele protokolování, je nutné nainstalovat potřebné balíčky.</span><span class="sxs-lookup"><span data-stu-id="ee79f-923">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="ee79f-924">Úplný seznam najdete v části [předdefinovaná zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="ee79f-924">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="ee79f-925">Chcete-li například povolit protokolování konzoly, nainstalujte `Microsoft.Extensions.Logging.Console` balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="ee79f-925">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="ee79f-926">Zavolejte `AddConsole` metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="ee79f-926">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="ee79f-927">V klientovi jazyka JavaScript existuje podobná `configureLogging` metoda.</span><span class="sxs-lookup"><span data-stu-id="ee79f-927">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="ee79f-928">Zadejte `LogLevel` hodnotu, která určuje minimální úroveň zpráv protokolu, které mají být vyprodukovány.</span><span class="sxs-lookup"><span data-stu-id="ee79f-928">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="ee79f-929">Protokoly se zapisují do okna konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="ee79f-929">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="ee79f-930">Chcete-li protokolování zcela zakázat, zadejte `signalR.LogLevel.None` v `configureLogging` metodě.</span><span class="sxs-lookup"><span data-stu-id="ee79f-930">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="ee79f-931">Další informace o protokolování naleznete v dokumentaci k [ SignalR diagnostice](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="ee79f-931">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="ee79f-932">SignalRKlient Java používá k protokolování knihovnu [SLF4J](https://www.slf4j.org/) .</span><span class="sxs-lookup"><span data-stu-id="ee79f-932">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="ee79f-933">Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí.</span><span class="sxs-lookup"><span data-stu-id="ee79f-933">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="ee79f-934">Následující fragment kódu ukazuje, jak používat `java.util.logging` s SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="ee79f-934">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="ee79f-935">Pokud ve svých závislostech nenakonfigurujete protokolování, SLF4J načte výchozí protokolovací nástroj No-operation s následující zprávou upozornění:</span><span class="sxs-lookup"><span data-stu-id="ee79f-935">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="ee79f-936">Tuto možnost lze bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="ee79f-936">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="ee79f-937">Konfigurace povolených přenosů</span><span class="sxs-lookup"><span data-stu-id="ee79f-937">Configure allowed transports</span></span>

<span data-ttu-id="ee79f-938">Přenosy používané nástrojem SignalR lze konfigurovat ve `WithUrl` volání ( `withUrl` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="ee79f-938">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="ee79f-939">Bitové nebo hodnoty `HttpTransportType` lze použít k omezení klienta na používání pouze zadaných přenosů.</span><span class="sxs-lookup"><span data-stu-id="ee79f-939">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="ee79f-940">Ve výchozím nastavení jsou povolené všechny přenosy.</span><span class="sxs-lookup"><span data-stu-id="ee79f-940">All transports are enabled by default.</span></span>

<span data-ttu-id="ee79f-941">Pokud třeba chcete zakázat přenos událostí odeslaných serverem, ale povolit WebSockets a dlouhá připojení s dotazem:</span><span class="sxs-lookup"><span data-stu-id="ee79f-941">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="ee79f-942">V klientu jazyka JavaScript jsou přenosy konfigurovány nastavením `transport` pole v objektu Options, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="ee79f-942">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="ee79f-943">V této verzi je jediným dostupným přenosem klientský WebSocket v jazyce Java.</span><span class="sxs-lookup"><span data-stu-id="ee79f-943">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="ee79f-944">Konfigurace ověřování nosiče</span><span class="sxs-lookup"><span data-stu-id="ee79f-944">Configure bearer authentication</span></span>

<span data-ttu-id="ee79f-945">K poskytnutí ověřovacích dat spolu s SignalR požadavky použijte `AccessTokenProvider` možnost ( `accessTokenFactory` v jazyce JavaScript) k určení funkce, která vrací požadovaný přístupový token.</span><span class="sxs-lookup"><span data-stu-id="ee79f-945">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="ee79f-946">V klientovi .NET se tento přístupový token předává jako token ověřování HTTP "Bearer" (s použitím `Authorization` hlavičky s typem `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="ee79f-946">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="ee79f-947">V klientu jazyka JavaScript se přístupový token používá jako nosný token, **s výjimkou** případů, kdy rozhraní API prohlížeče omezuje možnost použít hlavičky (konkrétně v požadavcích na události odeslané serverem a objekty WebSockets).</span><span class="sxs-lookup"><span data-stu-id="ee79f-947">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="ee79f-948">V těchto případech je přístupový token k dispozici jako hodnota řetězce dotazu `access_token` .</span><span class="sxs-lookup"><span data-stu-id="ee79f-948">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="ee79f-949">V klientu .NET `AccessTokenProvider` lze možnost zadat pomocí delegáta možnosti v `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="ee79f-949">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="ee79f-950">V klientovi JavaScriptu je přístupový token nakonfigurovaný nastavením `accessTokenFactory` pole v objektu Options v `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="ee79f-950">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="ee79f-951">V SignalR klientovi Java můžete nakonfigurovat nosný token, který se bude používat pro ověřování, a to poskytnutím továrny přístupového tokenu [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="ee79f-951">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="ee79f-952">Pomocí [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) můžete zadat [RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="ee79f-952">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="ee79f-953">Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.</span><span class="sxs-lookup"><span data-stu-id="ee79f-953">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="ee79f-954">Konfigurace možností timeout a Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="ee79f-954">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="ee79f-955">Další možnosti konfigurace časového limitu a chování funkce Keep-Alive jsou k dispozici na `HubConnection` samotném objektu:</span><span class="sxs-lookup"><span data-stu-id="ee79f-955">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="ee79f-956">.NET</span><span class="sxs-lookup"><span data-stu-id="ee79f-956">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="ee79f-957">Možnost</span><span class="sxs-lookup"><span data-stu-id="ee79f-957">Option</span></span> | <span data-ttu-id="ee79f-958">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-958">Default value</span></span> | <span data-ttu-id="ee79f-959">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-959">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="ee79f-960">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="ee79f-960">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="ee79f-961">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="ee79f-961">Timeout for server activity.</span></span> <span data-ttu-id="ee79f-962">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí `Closed` událost ( `onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="ee79f-962">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="ee79f-963">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="ee79f-963">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="ee79f-964">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="ee79f-964">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="ee79f-965">15 sekund</span><span class="sxs-lookup"><span data-stu-id="ee79f-965">15 seconds</span></span> | <span data-ttu-id="ee79f-966">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="ee79f-966">Timeout for initial server handshake.</span></span> <span data-ttu-id="ee79f-967">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a spustí `Closed` událost ( `onclose` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="ee79f-967">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="ee79f-968">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="ee79f-968">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="ee79f-969">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="ee79f-969">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="ee79f-970">15 sekund</span><span class="sxs-lookup"><span data-stu-id="ee79f-970">15 seconds</span></span> | <span data-ttu-id="ee79f-971">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="ee79f-971">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="ee79f-972">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="ee79f-972">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="ee79f-973">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="ee79f-973">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="ee79f-974">V klientovi .NET jsou hodnoty časového limitu zadány jako `TimeSpan` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="ee79f-974">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="ee79f-975">JavaScript</span><span class="sxs-lookup"><span data-stu-id="ee79f-975">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="ee79f-976">Možnost</span><span class="sxs-lookup"><span data-stu-id="ee79f-976">Option</span></span> | <span data-ttu-id="ee79f-977">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-977">Default value</span></span> | <span data-ttu-id="ee79f-978">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-978">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="ee79f-979">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="ee79f-979">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="ee79f-980">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="ee79f-980">Timeout for server activity.</span></span> <span data-ttu-id="ee79f-981">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onclose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="ee79f-981">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="ee79f-982">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="ee79f-982">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="ee79f-983">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="ee79f-983">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="ee79f-984">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="ee79f-984">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="ee79f-985">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="ee79f-985">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="ee79f-986">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="ee79f-986">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="ee79f-987">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="ee79f-987">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="ee79f-988">Java</span><span class="sxs-lookup"><span data-stu-id="ee79f-988">Java</span></span>](#tab/java)

| <span data-ttu-id="ee79f-989">Možnost</span><span class="sxs-lookup"><span data-stu-id="ee79f-989">Option</span></span> | <span data-ttu-id="ee79f-990">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-990">Default value</span></span> | <span data-ttu-id="ee79f-991">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-991">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="ee79f-992">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="ee79f-992">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="ee79f-993">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="ee79f-993">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="ee79f-994">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="ee79f-994">Timeout for server activity.</span></span> <span data-ttu-id="ee79f-995">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onClose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="ee79f-995">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="ee79f-996">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="ee79f-996">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="ee79f-997">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="ee79f-997">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="ee79f-998">15 sekund</span><span class="sxs-lookup"><span data-stu-id="ee79f-998">15 seconds</span></span> | <span data-ttu-id="ee79f-999">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="ee79f-999">Timeout for initial server handshake.</span></span> <span data-ttu-id="ee79f-1000">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje `onClose` událost.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1000">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="ee79f-1001">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1001">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="ee79f-1002">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="ee79f-1002">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="ee79f-1003">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="ee79f-1003">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="ee79f-1004">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="ee79f-1004">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="ee79f-1005">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1005">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="ee79f-1006">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1006">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="ee79f-1007">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1007">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="ee79f-1008">Konfigurace dalších možností</span><span class="sxs-lookup"><span data-stu-id="ee79f-1008">Configure additional options</span></span>

<span data-ttu-id="ee79f-1009">Další možnosti lze konfigurovat v `WithUrl` `withUrl` metodě (v jazyce JavaScript) na `HubConnectionBuilder` různých konfiguračních rozhraních API v `HttpHubConnectionBuilder` klientovi Java:</span><span class="sxs-lookup"><span data-stu-id="ee79f-1009">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="ee79f-1010">.NET</span><span class="sxs-lookup"><span data-stu-id="ee79f-1010">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="ee79f-1011">Možnost .NET</span><span class="sxs-lookup"><span data-stu-id="ee79f-1011">.NET Option</span></span> |  <span data-ttu-id="ee79f-1012">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-1012">Default value</span></span> | <span data-ttu-id="ee79f-1013">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-1013">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="ee79f-1014">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1014">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="ee79f-1015">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1015">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="ee79f-1016">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1016">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="ee79f-1017">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="ee79f-1017">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="ee79f-1018">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="ee79f-1018">Empty</span></span> | <span data-ttu-id="ee79f-1019">Kolekce certifikátů TLS pro odeslání na požadavky na ověření.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1019">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="ee79f-1020">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="ee79f-1020">Empty</span></span> | <span data-ttu-id="ee79f-1021">Kolekce souborů cookie protokolu HTTP, které se mají odeslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="ee79f-1021">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="ee79f-1022">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="ee79f-1022">Empty</span></span> | <span data-ttu-id="ee79f-1023">Přihlašovací údaje, které se mají poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="ee79f-1023">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="ee79f-1024">5 sekund</span><span class="sxs-lookup"><span data-stu-id="ee79f-1024">5 seconds</span></span> | <span data-ttu-id="ee79f-1025">Jenom objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1025">WebSockets only.</span></span> <span data-ttu-id="ee79f-1026">Maximální doba, po jejímž uplynutí bude klient čekat na ukončení žádosti o uzavření.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1026">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="ee79f-1027">Pokud server nepotvrdí zavření v této době, klient se odpojí.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1027">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="ee79f-1028">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="ee79f-1028">Empty</span></span> | <span data-ttu-id="ee79f-1029">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="ee79f-1029">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="ee79f-1030">Delegát, který lze použít ke konfiguraci nebo nahrazení `HttpMessageHandler` používaného k odeslání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1030">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="ee79f-1031">Nepoužívá se pro připojení pomocí protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1031">Not used for WebSocket connections.</span></span> <span data-ttu-id="ee79f-1032">Tento delegát musí vracet hodnotu, která není null, a obdrží výchozí hodnotu jako parametr.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1032">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="ee79f-1033">Buď změňte nastavení u této výchozí hodnoty a vraťte je, nebo vraťte novou `HttpMessageHandler` instanci.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1033">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="ee79f-1034">**Při nahrazování obslužné rutiny Nezapomeňte zkopírovat nastavení, která chcete zachovat, od poskytnuté obslužné rutiny, jinak konfigurované možnosti (například soubory cookie a záhlaví) nebudou použity pro novou obslužnou rutinu.**</span><span class="sxs-lookup"><span data-stu-id="ee79f-1034">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="ee79f-1035">Proxy server HTTP, který se má použít při odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1035">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="ee79f-1036">Nastavte tuto logickou hodnotu pro odeslání výchozích přihlašovacích údajů pro požadavky HTTP a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1036">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="ee79f-1037">To umožňuje použití ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1037">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="ee79f-1038">Delegát, který lze použít ke konfiguraci dalších možností protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1038">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="ee79f-1039">Přijímá instanci [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , která se dá použít ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1039">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="ee79f-1040">JavaScript</span><span class="sxs-lookup"><span data-stu-id="ee79f-1040">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="ee79f-1041">Možnost JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="ee79f-1041">JavaScript Option</span></span> | <span data-ttu-id="ee79f-1042">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-1042">Default Value</span></span> | <span data-ttu-id="ee79f-1043">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-1043">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="ee79f-1044">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1044">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="ee79f-1045">Nastavte na `true` protokolování bajtů/znaků zpráv odesílaných a přijímaných klientem.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1045">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="ee79f-1046">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1046">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="ee79f-1047">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1047">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="ee79f-1048">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="ee79f-1048">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="ee79f-1049">Java</span><span class="sxs-lookup"><span data-stu-id="ee79f-1049">Java</span></span>](#tab/java)

| <span data-ttu-id="ee79f-1050">Možnost jazyka Java</span><span class="sxs-lookup"><span data-stu-id="ee79f-1050">Java Option</span></span> | <span data-ttu-id="ee79f-1051">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-1051">Default Value</span></span> | <span data-ttu-id="ee79f-1052">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-1052">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="ee79f-1053">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1053">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="ee79f-1054">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1054">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="ee79f-1055">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1055">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="ee79f-1056">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="ee79f-1056">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="ee79f-1057">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="ee79f-1057">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="ee79f-1058">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="ee79f-1058">Empty</span></span> | <span data-ttu-id="ee79f-1059">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="ee79f-1059">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="ee79f-1060">V klientu .NET můžete tyto možnosti upravit pomocí delegáta možností, který je k dispozici pro `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="ee79f-1060">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="ee79f-1061">V klientovi JavaScriptu lze tyto možnosti poskytnout v objektu jazyka JavaScript, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="ee79f-1061">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="ee79f-1062">V klientovi Java lze tyto možnosti nakonfigurovat s metodami `HttpHubConnectionBuilder` vrácenými z`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="ee79f-1062">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="ee79f-1063">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="ee79f-1063">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="ee79f-1064">Možnosti serializace JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="ee79f-1064">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="ee79f-1065">ASP.NET Core SignalR podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="ee79f-1065">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="ee79f-1066">Každý protokol má možnosti konfigurace serializace.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1066">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="ee79f-1067">Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , kterou lze přidat po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v `Startup.ConfigureServices` metodě.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1067">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="ee79f-1068">`AddJsonProtocol`Metoda přebírá delegáta, který přijímá `options` objekt.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1068">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="ee79f-1069">Vlastnost [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) tohoto objektu je `JsonSerializerSettings` objekt JSON.NET, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1069">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="ee79f-1070">Další informace najdete v [dokumentaci k JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="ee79f-1070">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="ee79f-1071">Chcete-li například nakonfigurovat serializátor pro použití názvů vlastností "PascalCase" namísto výchozích názvů "camelCase", použijte následující kód v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="ee79f-1071">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="ee79f-1072">V klientovi .NET `AddJsonProtocol` existuje stejná rozšiřující metoda v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="ee79f-1072">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="ee79f-1073">`Microsoft.Extensions.DependencyInjection`Obor názvů musí být importován, aby bylo možné přeložit metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="ee79f-1073">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="ee79f-1074">V tuto chvíli není možné konfigurovat serializaci JSON v klientovi jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1074">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="ee79f-1075">Možnosti serializace MessagePack</span><span class="sxs-lookup"><span data-stu-id="ee79f-1075">MessagePack serialization options</span></span>

<span data-ttu-id="ee79f-1076">Serializaci MessagePack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="ee79f-1076">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="ee79f-1077">Další podrobnosti najdete [v SignalR tématu MessagePack](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="ee79f-1077">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="ee79f-1078">V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi v jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1078">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="ee79f-1079">Konfigurovat možnosti serveru</span><span class="sxs-lookup"><span data-stu-id="ee79f-1079">Configure server options</span></span>

<span data-ttu-id="ee79f-1080">Následující tabulka popisuje možnosti konfigurace SignalR Center:</span><span class="sxs-lookup"><span data-stu-id="ee79f-1080">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="ee79f-1081">Možnost</span><span class="sxs-lookup"><span data-stu-id="ee79f-1081">Option</span></span> | <span data-ttu-id="ee79f-1082">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-1082">Default Value</span></span> | <span data-ttu-id="ee79f-1083">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-1083">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="ee79f-1084">15 sekund</span><span class="sxs-lookup"><span data-stu-id="ee79f-1084">15 seconds</span></span> | <span data-ttu-id="ee79f-1085">Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1085">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="ee79f-1086">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1086">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="ee79f-1087">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="ee79f-1087">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="ee79f-1088">15 sekund</span><span class="sxs-lookup"><span data-stu-id="ee79f-1088">15 seconds</span></span> | <span data-ttu-id="ee79f-1089">Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1089">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="ee79f-1090">Při změně `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení změňte nastavení v klientovi.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1090">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="ee79f-1091">Doporučená `ServerTimeout` / `serverTimeoutInMilliseconds` hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1091">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="ee79f-1092">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="ee79f-1092">All installed protocols</span></span> | <span data-ttu-id="ee79f-1093">Protokoly podporované tímto rozbočovačem</span><span class="sxs-lookup"><span data-stu-id="ee79f-1093">Protocols supported by this hub.</span></span> <span data-ttu-id="ee79f-1094">Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1094">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="ee79f-1095">Pokud se `true` do klientů vrátí podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1095">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="ee79f-1096">Výchozí hodnota je `false` , protože tyto zprávy o výjimce můžou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1096">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="ee79f-1097">Možnosti lze nakonfigurovat pro všechna centra tím, že poskytnete možnosti delegáta pro `AddSignalR` volání v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="ee79f-1097">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="ee79f-1098">Možnosti pro jeden rozbočovač přepíší globální možnosti poskytované v `AddSignalR` a můžou být nakonfigurované pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="ee79f-1098">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="ee79f-1099">Rozšířené možnosti konfigurace protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="ee79f-1099">Advanced HTTP configuration options</span></span>

<span data-ttu-id="ee79f-1100">Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1100">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="ee79f-1101">Tyto možnosti jsou nakonfigurovány předáním delegáta [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="ee79f-1101">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="ee79f-1102">Následující tabulka popisuje možnosti konfigurace SignalR rozšířených možností protokolu HTTP v ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="ee79f-1102">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="ee79f-1103">Možnost</span><span class="sxs-lookup"><span data-stu-id="ee79f-1103">Option</span></span> | <span data-ttu-id="ee79f-1104">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-1104">Default Value</span></span> | <span data-ttu-id="ee79f-1105">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-1105">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="ee79f-1106">32 KB</span><span class="sxs-lookup"><span data-stu-id="ee79f-1106">32 KB</span></span> | <span data-ttu-id="ee79f-1107">Maximální počet bajtů přijatých od klienta, které jsou vyrovnávací paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1107">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="ee79f-1108">Zvýšením této hodnoty umožníte serveru přijímat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1108">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="ee79f-1109">Data se automaticky shromažďují z `Authorize` atributů použitých pro třídu centra.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1109">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="ee79f-1110">Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru</span><span class="sxs-lookup"><span data-stu-id="ee79f-1110">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="ee79f-1111">32 KB</span><span class="sxs-lookup"><span data-stu-id="ee79f-1111">32 KB</span></span> | <span data-ttu-id="ee79f-1112">Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1112">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="ee79f-1113">Zvýšením této hodnoty umožníte serveru odesílat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1113">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="ee79f-1114">Všechny přenosy jsou povolené.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1114">All Transports are enabled.</span></span> | <span data-ttu-id="ee79f-1115">Bitové příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenos, který může klient použít pro připojení.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1115">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="ee79f-1116">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1116">See below.</span></span> | <span data-ttu-id="ee79f-1117">Další možnosti specifické pro přenos dlouhého cyklického dotazování.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1117">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="ee79f-1118">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1118">See below.</span></span> | <span data-ttu-id="ee79f-1119">Další možnosti specifické pro přenos pomocí protokolu WebSockets</span><span class="sxs-lookup"><span data-stu-id="ee79f-1119">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="ee79f-1120">Přenos dlouhého cyklického dotazování má další možnosti, které je možné konfigurovat pomocí `LongPolling` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="ee79f-1120">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="ee79f-1121">Možnost</span><span class="sxs-lookup"><span data-stu-id="ee79f-1121">Option</span></span> | <span data-ttu-id="ee79f-1122">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-1122">Default Value</span></span> | <span data-ttu-id="ee79f-1123">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-1123">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="ee79f-1124">90 sekund</span><span class="sxs-lookup"><span data-stu-id="ee79f-1124">90 seconds</span></span> | <span data-ttu-id="ee79f-1125">Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jedné žádosti o cyklické dotazování.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1125">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="ee79f-1126">Snížení této hodnoty způsobí, že klient bude vydávat nové požadavky na dotaz častěji.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1126">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="ee79f-1127">Přenos protokolu WebSocket má další možnosti, které je možné konfigurovat pomocí `WebSockets` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="ee79f-1127">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="ee79f-1128">Možnost</span><span class="sxs-lookup"><span data-stu-id="ee79f-1128">Option</span></span> | <span data-ttu-id="ee79f-1129">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-1129">Default Value</span></span> | <span data-ttu-id="ee79f-1130">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-1130">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="ee79f-1131">5 sekund</span><span class="sxs-lookup"><span data-stu-id="ee79f-1131">5 seconds</span></span> | <span data-ttu-id="ee79f-1132">Pokud se po ukončení serveru aplikace v tomto časovém intervalu nepovede zavřít, připojení se ukončí.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1132">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="ee79f-1133">Delegát, který lze použít k nastavení `Sec-WebSocket-Protocol` záhlaví na vlastní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1133">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="ee79f-1134">Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že se vrátí požadovaná hodnota.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1134">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="ee79f-1135">Konfigurace možností klienta</span><span class="sxs-lookup"><span data-stu-id="ee79f-1135">Configure client options</span></span>

<span data-ttu-id="ee79f-1136">Možnosti klienta lze nakonfigurovat pro `HubConnectionBuilder` typ (k dispozici v klientech rozhraní .NET a v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="ee79f-1136">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="ee79f-1137">Je také k dispozici v klientovi Java, ale `HttpHubConnectionBuilder` podtřídou je to, co obsahuje možnosti konfigurace tvůrce a také na `HubConnection` sebe sama.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1137">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="ee79f-1138">Konfigurovat protokolování</span><span class="sxs-lookup"><span data-stu-id="ee79f-1138">Configure logging</span></span>

<span data-ttu-id="ee79f-1139">Protokolování je konfigurováno v klientovi .NET pomocí `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1139">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="ee79f-1140">Zprostředkovatelé protokolování a filtry je možné zaregistrovat stejným způsobem jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1140">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="ee79f-1141">Další informace najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="ee79f-1141">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="ee79f-1142">Aby bylo možné registrovat poskytovatele protokolování, je nutné nainstalovat potřebné balíčky.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1142">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="ee79f-1143">Úplný seznam najdete v části [předdefinovaná zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1143">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="ee79f-1144">Chcete-li například povolit protokolování konzoly, nainstalujte `Microsoft.Extensions.Logging.Console` balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1144">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="ee79f-1145">Zavolejte `AddConsole` metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="ee79f-1145">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="ee79f-1146">V klientovi jazyka JavaScript existuje podobná `configureLogging` metoda.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1146">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="ee79f-1147">Zadejte `LogLevel` hodnotu, která určuje minimální úroveň zpráv protokolu, které mají být vyprodukovány.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1147">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="ee79f-1148">Protokoly se zapisují do okna konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1148">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="ee79f-1149">Chcete-li protokolování zcela zakázat, zadejte `signalR.LogLevel.None` v `configureLogging` metodě.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1149">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="ee79f-1150">Další informace o protokolování naleznete v dokumentaci k [ SignalR diagnostice](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="ee79f-1150">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="ee79f-1151">SignalRKlient Java používá k protokolování knihovnu [SLF4J](https://www.slf4j.org/) .</span><span class="sxs-lookup"><span data-stu-id="ee79f-1151">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="ee79f-1152">Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1152">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="ee79f-1153">Následující fragment kódu ukazuje, jak používat `java.util.logging` s SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1153">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="ee79f-1154">Pokud ve svých závislostech nenakonfigurujete protokolování, SLF4J načte výchozí protokolovací nástroj No-operation s následující zprávou upozornění:</span><span class="sxs-lookup"><span data-stu-id="ee79f-1154">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="ee79f-1155">Tuto možnost lze bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1155">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="ee79f-1156">Konfigurace povolených přenosů</span><span class="sxs-lookup"><span data-stu-id="ee79f-1156">Configure allowed transports</span></span>

<span data-ttu-id="ee79f-1157">Přenosy používané nástrojem SignalR lze konfigurovat ve `WithUrl` volání ( `withUrl` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="ee79f-1157">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="ee79f-1158">Bitové nebo hodnoty `HttpTransportType` lze použít k omezení klienta na používání pouze zadaných přenosů.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1158">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="ee79f-1159">Ve výchozím nastavení jsou povolené všechny přenosy.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1159">All transports are enabled by default.</span></span>

<span data-ttu-id="ee79f-1160">Pokud třeba chcete zakázat přenos událostí odeslaných serverem, ale povolit WebSockets a dlouhá připojení s dotazem:</span><span class="sxs-lookup"><span data-stu-id="ee79f-1160">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="ee79f-1161">V klientu jazyka JavaScript jsou přenosy konfigurovány nastavením `transport` pole v objektu Options, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="ee79f-1161">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="ee79f-1162">Konfigurace ověřování nosiče</span><span class="sxs-lookup"><span data-stu-id="ee79f-1162">Configure bearer authentication</span></span>

<span data-ttu-id="ee79f-1163">K poskytnutí ověřovacích dat spolu s SignalR požadavky použijte `AccessTokenProvider` možnost ( `accessTokenFactory` v jazyce JavaScript) k určení funkce, která vrací požadovaný přístupový token.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1163">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="ee79f-1164">V klientovi .NET se tento přístupový token předává jako token ověřování HTTP "Bearer" (s použitím `Authorization` hlavičky s typem `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="ee79f-1164">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="ee79f-1165">V klientu jazyka JavaScript se přístupový token používá jako nosný token, **s výjimkou** případů, kdy rozhraní API prohlížeče omezuje možnost použít hlavičky (konkrétně v požadavcích na události odeslané serverem a objekty WebSockets).</span><span class="sxs-lookup"><span data-stu-id="ee79f-1165">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="ee79f-1166">V těchto případech je přístupový token k dispozici jako hodnota řetězce dotazu `access_token` .</span><span class="sxs-lookup"><span data-stu-id="ee79f-1166">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="ee79f-1167">V klientu .NET `AccessTokenProvider` lze možnost zadat pomocí delegáta možnosti v `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="ee79f-1167">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="ee79f-1168">V klientovi JavaScriptu je přístupový token nakonfigurovaný nastavením `accessTokenFactory` pole v objektu Options v `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="ee79f-1168">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="ee79f-1169">V SignalR klientovi Java můžete nakonfigurovat nosný token, který se bude používat pro ověřování, a to poskytnutím továrny přístupového tokenu [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="ee79f-1169">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="ee79f-1170">Pomocí [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) můžete zadat [RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="ee79f-1170">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="ee79f-1171">Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1171">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="ee79f-1172">Konfigurace možností timeout a Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="ee79f-1172">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="ee79f-1173">Další možnosti konfigurace časového limitu a chování funkce Keep-Alive jsou k dispozici na `HubConnection` samotném objektu:</span><span class="sxs-lookup"><span data-stu-id="ee79f-1173">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="ee79f-1174">.NET</span><span class="sxs-lookup"><span data-stu-id="ee79f-1174">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="ee79f-1175">Možnost</span><span class="sxs-lookup"><span data-stu-id="ee79f-1175">Option</span></span> | <span data-ttu-id="ee79f-1176">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-1176">Default value</span></span> | <span data-ttu-id="ee79f-1177">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-1177">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="ee79f-1178">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="ee79f-1178">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="ee79f-1179">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1179">Timeout for server activity.</span></span> <span data-ttu-id="ee79f-1180">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí `Closed` událost ( `onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="ee79f-1180">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="ee79f-1181">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1181">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="ee79f-1182">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1182">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="ee79f-1183">15 sekund</span><span class="sxs-lookup"><span data-stu-id="ee79f-1183">15 seconds</span></span> | <span data-ttu-id="ee79f-1184">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1184">Timeout for initial server handshake.</span></span> <span data-ttu-id="ee79f-1185">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a spustí `Closed` událost ( `onclose` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="ee79f-1185">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="ee79f-1186">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1186">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="ee79f-1187">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="ee79f-1187">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="ee79f-1188">V klientovi .NET jsou hodnoty časového limitu zadány jako `TimeSpan` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1188">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="ee79f-1189">JavaScript</span><span class="sxs-lookup"><span data-stu-id="ee79f-1189">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="ee79f-1190">Možnost</span><span class="sxs-lookup"><span data-stu-id="ee79f-1190">Option</span></span> | <span data-ttu-id="ee79f-1191">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-1191">Default value</span></span> | <span data-ttu-id="ee79f-1192">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-1192">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="ee79f-1193">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="ee79f-1193">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="ee79f-1194">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1194">Timeout for server activity.</span></span> <span data-ttu-id="ee79f-1195">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onclose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1195">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="ee79f-1196">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1196">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="ee79f-1197">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1197">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="ee79f-1198">Java</span><span class="sxs-lookup"><span data-stu-id="ee79f-1198">Java</span></span>](#tab/java)

| <span data-ttu-id="ee79f-1199">Možnost</span><span class="sxs-lookup"><span data-stu-id="ee79f-1199">Option</span></span> | <span data-ttu-id="ee79f-1200">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-1200">Default value</span></span> | <span data-ttu-id="ee79f-1201">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-1201">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="ee79f-1202">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="ee79f-1202">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="ee79f-1203">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="ee79f-1203">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="ee79f-1204">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1204">Timeout for server activity.</span></span> <span data-ttu-id="ee79f-1205">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onClose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1205">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="ee79f-1206">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1206">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="ee79f-1207">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit k příkazům k zadání času.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1207">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="ee79f-1208">15 sekund</span><span class="sxs-lookup"><span data-stu-id="ee79f-1208">15 seconds</span></span> | <span data-ttu-id="ee79f-1209">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1209">Timeout for initial server handshake.</span></span> <span data-ttu-id="ee79f-1210">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje `onClose` událost.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1210">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="ee79f-1211">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1211">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="ee79f-1212">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="ee79f-1212">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="ee79f-1213">Konfigurace dalších možností</span><span class="sxs-lookup"><span data-stu-id="ee79f-1213">Configure additional options</span></span>

<span data-ttu-id="ee79f-1214">Další možnosti lze konfigurovat v `WithUrl` `withUrl` metodě (v jazyce JavaScript) na `HubConnectionBuilder` různých konfiguračních rozhraních API v `HttpHubConnectionBuilder` klientovi Java:</span><span class="sxs-lookup"><span data-stu-id="ee79f-1214">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="ee79f-1215">.NET</span><span class="sxs-lookup"><span data-stu-id="ee79f-1215">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="ee79f-1216">Možnost .NET</span><span class="sxs-lookup"><span data-stu-id="ee79f-1216">.NET Option</span></span> |  <span data-ttu-id="ee79f-1217">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-1217">Default value</span></span> | <span data-ttu-id="ee79f-1218">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-1218">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="ee79f-1219">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1219">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="ee79f-1220">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1220">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="ee79f-1221">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1221">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="ee79f-1222">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="ee79f-1222">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="ee79f-1223">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="ee79f-1223">Empty</span></span> | <span data-ttu-id="ee79f-1224">Kolekce certifikátů TLS pro odeslání na požadavky na ověření.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1224">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="ee79f-1225">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="ee79f-1225">Empty</span></span> | <span data-ttu-id="ee79f-1226">Kolekce souborů cookie protokolu HTTP, které se mají odeslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="ee79f-1226">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="ee79f-1227">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="ee79f-1227">Empty</span></span> | <span data-ttu-id="ee79f-1228">Přihlašovací údaje, které se mají poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="ee79f-1228">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="ee79f-1229">5 sekund</span><span class="sxs-lookup"><span data-stu-id="ee79f-1229">5 seconds</span></span> | <span data-ttu-id="ee79f-1230">Jenom objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1230">WebSockets only.</span></span> <span data-ttu-id="ee79f-1231">Maximální doba, po jejímž uplynutí bude klient čekat na ukončení žádosti o uzavření.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1231">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="ee79f-1232">Pokud server nepotvrdí zavření v této době, klient se odpojí.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1232">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="ee79f-1233">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="ee79f-1233">Empty</span></span> | <span data-ttu-id="ee79f-1234">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="ee79f-1234">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="ee79f-1235">Delegát, který lze použít ke konfiguraci nebo nahrazení `HttpMessageHandler` používaného k odeslání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1235">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="ee79f-1236">Nepoužívá se pro připojení pomocí protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1236">Not used for WebSocket connections.</span></span> <span data-ttu-id="ee79f-1237">Tento delegát musí vracet hodnotu, která není null, a obdrží výchozí hodnotu jako parametr.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1237">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="ee79f-1238">Buď změňte nastavení u této výchozí hodnoty a vraťte je, nebo vraťte novou `HttpMessageHandler` instanci.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1238">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="ee79f-1239">**Při nahrazování obslužné rutiny Nezapomeňte zkopírovat nastavení, která chcete zachovat, od poskytnuté obslužné rutiny, jinak konfigurované možnosti (například soubory cookie a záhlaví) nebudou použity pro novou obslužnou rutinu.**</span><span class="sxs-lookup"><span data-stu-id="ee79f-1239">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="ee79f-1240">Proxy server HTTP, který se má použít při odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1240">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="ee79f-1241">Nastavte tuto logickou hodnotu pro odeslání výchozích přihlašovacích údajů pro požadavky HTTP a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1241">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="ee79f-1242">To umožňuje použití ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1242">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="ee79f-1243">Delegát, který lze použít ke konfiguraci dalších možností protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1243">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="ee79f-1244">Přijímá instanci [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , která se dá použít ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1244">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="ee79f-1245">JavaScript</span><span class="sxs-lookup"><span data-stu-id="ee79f-1245">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="ee79f-1246">Možnost JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="ee79f-1246">JavaScript Option</span></span> | <span data-ttu-id="ee79f-1247">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-1247">Default Value</span></span> | <span data-ttu-id="ee79f-1248">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-1248">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="ee79f-1249">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1249">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="ee79f-1250">Nastavte na `true` protokolování bajtů/znaků zpráv odesílaných a přijímaných klientem.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1250">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="ee79f-1251">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1251">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="ee79f-1252">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1252">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="ee79f-1253">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="ee79f-1253">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="ee79f-1254">Java</span><span class="sxs-lookup"><span data-stu-id="ee79f-1254">Java</span></span>](#tab/java)

| <span data-ttu-id="ee79f-1255">Možnost jazyka Java</span><span class="sxs-lookup"><span data-stu-id="ee79f-1255">Java Option</span></span> | <span data-ttu-id="ee79f-1256">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="ee79f-1256">Default Value</span></span> | <span data-ttu-id="ee79f-1257">Description</span><span class="sxs-lookup"><span data-stu-id="ee79f-1257">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="ee79f-1258">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1258">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="ee79f-1259">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1259">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="ee79f-1260">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="ee79f-1260">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="ee79f-1261">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="ee79f-1261">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="ee79f-1262">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="ee79f-1262">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="ee79f-1263">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="ee79f-1263">Empty</span></span> | <span data-ttu-id="ee79f-1264">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="ee79f-1264">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="ee79f-1265">V klientu .NET můžete tyto možnosti upravit pomocí delegáta možností, který je k dispozici pro `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="ee79f-1265">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="ee79f-1266">V klientovi JavaScriptu lze tyto možnosti poskytnout v objektu jazyka JavaScript, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="ee79f-1266">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="ee79f-1267">V klientovi Java lze tyto možnosti nakonfigurovat s metodami `HttpHubConnectionBuilder` vrácenými z`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="ee79f-1267">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="ee79f-1268">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="ee79f-1268">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
