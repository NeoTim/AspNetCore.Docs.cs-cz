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
ms.openlocfilehash: fc0e6398884bb5c3b806a587a8a361d7f279461f
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625554"
---
# <a name="aspnet-core-no-locsignalr-configuration"></a><span data-ttu-id="b3d60-103">SignalRKonfigurace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b3d60-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="b3d60-104">Možnosti serializace JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="b3d60-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="b3d60-105">ASP.NET Core SignalR podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="b3d60-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="b3d60-106">Každý protokol má možnosti konfigurace serializace.</span><span class="sxs-lookup"><span data-stu-id="b3d60-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="b3d60-107">Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="b3d60-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="b3d60-108">`AddJsonProtocol`lze přidat po [Přidání SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) do `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b3d60-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b3d60-109">`AddJsonProtocol`Metoda přebírá delegáta, který přijímá `options` objekt.</span><span class="sxs-lookup"><span data-stu-id="b3d60-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="b3d60-110">Vlastnost [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) tohoto objektu je `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objekt, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="b3d60-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="b3d60-111">Další informace najdete v [System.Text.Jsv dokumentaci](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="b3d60-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="b3d60-112">Chcete-li například nakonfigurovat serializátor, aby neměnil velká a malá písmena názvů vlastností namísto výchozích názvů "camelCase", použijte následující kód v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b3d60-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="b3d60-113">V klientovi .NET `AddJsonProtocol` existuje stejná rozšiřující metoda v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="b3d60-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="b3d60-114">`Microsoft.Extensions.DependencyInjection`Obor názvů musí být importován, aby bylo možné přeložit metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="b3d60-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="b3d60-115">V tuto chvíli není možné konfigurovat serializaci JSON v klientovi jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b3d60-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="b3d60-116">Přepnout na Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="b3d60-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="b3d60-117">Pokud potřebujete funkce nástroje `Newtonsoft.Json` , které nejsou podporované v systému `System.Text.Json` , přečtěte si téma [Přepnutí na Newtonsoft.Jsna](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="b3d60-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="b3d60-118">Možnosti serializace MessagePack</span><span class="sxs-lookup"><span data-stu-id="b3d60-118">MessagePack serialization options</span></span>

<span data-ttu-id="b3d60-119">Serializaci MessagePack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="b3d60-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="b3d60-120">Další podrobnosti najdete [v SignalR tématu MessagePack](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="b3d60-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="b3d60-121">V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi v jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b3d60-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="b3d60-122">Konfigurovat možnosti serveru</span><span class="sxs-lookup"><span data-stu-id="b3d60-122">Configure server options</span></span>

<span data-ttu-id="b3d60-123">Následující tabulka popisuje možnosti konfigurace SignalR Center:</span><span class="sxs-lookup"><span data-stu-id="b3d60-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="b3d60-124">Možnost</span><span class="sxs-lookup"><span data-stu-id="b3d60-124">Option</span></span> | <span data-ttu-id="b3d60-125">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-125">Default Value</span></span> | <span data-ttu-id="b3d60-126">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="b3d60-127">30 sekund</span><span class="sxs-lookup"><span data-stu-id="b3d60-127">30 seconds</span></span> | <span data-ttu-id="b3d60-128">Server bude předpokládat, že je klient odpojený, pokud v tomto intervalu neobdržel zprávu (včetně Keep-Alive).</span><span class="sxs-lookup"><span data-stu-id="b3d60-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="b3d60-129">Může trvat delší dobu, než je tento časový limit, aby bylo možné klienta ve skutečnosti označit jako odpojený, protože to je implementováno.</span><span class="sxs-lookup"><span data-stu-id="b3d60-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="b3d60-130">Doporučená hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="b3d60-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="b3d60-131">15 sekund</span><span class="sxs-lookup"><span data-stu-id="b3d60-131">15 seconds</span></span> | <span data-ttu-id="b3d60-132">Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře.</span><span class="sxs-lookup"><span data-stu-id="b3d60-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="b3d60-133">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="b3d60-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b3d60-134">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b3d60-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="b3d60-135">15 sekund</span><span class="sxs-lookup"><span data-stu-id="b3d60-135">15 seconds</span></span> | <span data-ttu-id="b3d60-136">Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="b3d60-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="b3d60-137">Při změně `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení změňte nastavení v klientovi.</span><span class="sxs-lookup"><span data-stu-id="b3d60-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="b3d60-138">Doporučená `ServerTimeout` / `serverTimeoutInMilliseconds` hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="b3d60-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="b3d60-139">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="b3d60-139">All installed protocols</span></span> | <span data-ttu-id="b3d60-140">Protokoly podporované tímto rozbočovačem</span><span class="sxs-lookup"><span data-stu-id="b3d60-140">Protocols supported by this hub.</span></span> <span data-ttu-id="b3d60-141">Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly.</span><span class="sxs-lookup"><span data-stu-id="b3d60-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="b3d60-142">Pokud se `true` do klientů vrátí podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="b3d60-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="b3d60-143">Výchozí hodnota je `false` , protože tyto zprávy o výjimce můžou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="b3d60-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="b3d60-144">Maximální počet položek, které lze uložit do vyrovnávací paměti pro datové proudy pro odeslání klienta.</span><span class="sxs-lookup"><span data-stu-id="b3d60-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="b3d60-145">Pokud je dosaženo tohoto limitu, zpracování volání je blokováno, dokud server nezpracovává položky datového proudu.</span><span class="sxs-lookup"><span data-stu-id="b3d60-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="b3d60-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="b3d60-146">32 KB</span></span> | <span data-ttu-id="b3d60-147">Maximální velikost jedné příchozí zprávy centra</span><span class="sxs-lookup"><span data-stu-id="b3d60-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="b3d60-148">Možnosti lze nakonfigurovat pro všechna centra tím, že poskytnete možnosti delegáta pro `AddSignalR` volání v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b3d60-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="b3d60-149">Možnosti pro jeden rozbočovač přepíší globální možnosti poskytované v `AddSignalR` a můžou být nakonfigurované pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="b3d60-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="b3d60-150">Rozšířené možnosti konfigurace protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="b3d60-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="b3d60-151">Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="b3d60-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="b3d60-152">Tyto možnosti jsou nakonfigurovány předáním delegáta [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b3d60-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="b3d60-153">Následující tabulka popisuje možnosti konfigurace SignalR rozšířených možností protokolu HTTP v ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="b3d60-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="b3d60-154">Možnost</span><span class="sxs-lookup"><span data-stu-id="b3d60-154">Option</span></span> | <span data-ttu-id="b3d60-155">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-155">Default Value</span></span> | <span data-ttu-id="b3d60-156">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="b3d60-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="b3d60-157">32 KB</span></span> | <span data-ttu-id="b3d60-158">Maximální počet bajtů přijatých od klienta, které jsou ukládány do vyrovnávací paměti serveru před použitím protitlaku.</span><span class="sxs-lookup"><span data-stu-id="b3d60-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="b3d60-159">Zvýšením této hodnoty umožníte, aby server přijímal větší zprávy rychleji bez použití zatížení, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="b3d60-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="b3d60-160">Data se automaticky shromažďují z `Authorize` atributů použitých pro třídu centra.</span><span class="sxs-lookup"><span data-stu-id="b3d60-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="b3d60-161">Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru</span><span class="sxs-lookup"><span data-stu-id="b3d60-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="b3d60-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="b3d60-162">32 KB</span></span> | <span data-ttu-id="b3d60-163">Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru před pozorováním protitlaku.</span><span class="sxs-lookup"><span data-stu-id="b3d60-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="b3d60-164">Zvýšením této hodnoty umožníte serveru rychleji ukládat větší zprávy bez nutnosti očekávat zatížení, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="b3d60-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="b3d60-165">Všechny přenosy jsou povolené.</span><span class="sxs-lookup"><span data-stu-id="b3d60-165">All Transports are enabled.</span></span> | <span data-ttu-id="b3d60-166">Bitové příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenos, který může klient použít pro připojení.</span><span class="sxs-lookup"><span data-stu-id="b3d60-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="b3d60-167">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="b3d60-167">See below.</span></span> | <span data-ttu-id="b3d60-168">Další možnosti specifické pro přenos dlouhého cyklického dotazování.</span><span class="sxs-lookup"><span data-stu-id="b3d60-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="b3d60-169">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="b3d60-169">See below.</span></span> | <span data-ttu-id="b3d60-170">Další možnosti specifické pro přenos pomocí protokolu WebSockets</span><span class="sxs-lookup"><span data-stu-id="b3d60-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="b3d60-171">0</span><span class="sxs-lookup"><span data-stu-id="b3d60-171">0</span></span> | <span data-ttu-id="b3d60-172">Zadejte minimální verzi protokolu Negotiate.</span><span class="sxs-lookup"><span data-stu-id="b3d60-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="b3d60-173">Slouží k omezení klientů na novější verze.</span><span class="sxs-lookup"><span data-stu-id="b3d60-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="b3d60-174">Přenos dlouhého cyklického dotazování má další možnosti, které je možné konfigurovat pomocí `LongPolling` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="b3d60-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="b3d60-175">Možnost</span><span class="sxs-lookup"><span data-stu-id="b3d60-175">Option</span></span> | <span data-ttu-id="b3d60-176">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-176">Default Value</span></span> | <span data-ttu-id="b3d60-177">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="b3d60-178">90 sekund</span><span class="sxs-lookup"><span data-stu-id="b3d60-178">90 seconds</span></span> | <span data-ttu-id="b3d60-179">Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jedné žádosti o cyklické dotazování.</span><span class="sxs-lookup"><span data-stu-id="b3d60-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="b3d60-180">Snížení této hodnoty způsobí, že klient bude vydávat nové požadavky na dotaz častěji.</span><span class="sxs-lookup"><span data-stu-id="b3d60-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="b3d60-181">Přenos protokolu WebSocket má další možnosti, které je možné konfigurovat pomocí `WebSockets` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="b3d60-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="b3d60-182">Možnost</span><span class="sxs-lookup"><span data-stu-id="b3d60-182">Option</span></span> | <span data-ttu-id="b3d60-183">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-183">Default Value</span></span> | <span data-ttu-id="b3d60-184">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="b3d60-185">5 sekund</span><span class="sxs-lookup"><span data-stu-id="b3d60-185">5 seconds</span></span> | <span data-ttu-id="b3d60-186">Pokud se po ukončení serveru aplikace v tomto časovém intervalu nepovede zavřít, připojení se ukončí.</span><span class="sxs-lookup"><span data-stu-id="b3d60-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="b3d60-187">Delegát, který lze použít k nastavení `Sec-WebSocket-Protocol` záhlaví na vlastní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="b3d60-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="b3d60-188">Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že se vrátí požadovaná hodnota.</span><span class="sxs-lookup"><span data-stu-id="b3d60-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="b3d60-189">Konfigurace možností klienta</span><span class="sxs-lookup"><span data-stu-id="b3d60-189">Configure client options</span></span>

<span data-ttu-id="b3d60-190">Možnosti klienta lze nakonfigurovat pro `HubConnectionBuilder` typ (k dispozici v klientech rozhraní .NET a v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b3d60-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="b3d60-191">Je také k dispozici v klientovi Java, ale `HttpHubConnectionBuilder` podtřídou je to, co obsahuje možnosti konfigurace tvůrce a také na `HubConnection` sebe sama.</span><span class="sxs-lookup"><span data-stu-id="b3d60-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="b3d60-192">Konfigurovat protokolování</span><span class="sxs-lookup"><span data-stu-id="b3d60-192">Configure logging</span></span>

<span data-ttu-id="b3d60-193">Protokolování je konfigurováno v klientovi .NET pomocí `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="b3d60-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="b3d60-194">Zprostředkovatelé protokolování a filtry je možné zaregistrovat stejným způsobem jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="b3d60-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="b3d60-195">Další informace najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="b3d60-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="b3d60-196">Aby bylo možné registrovat poskytovatele protokolování, je nutné nainstalovat potřebné balíčky.</span><span class="sxs-lookup"><span data-stu-id="b3d60-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="b3d60-197">Úplný seznam najdete v části [předdefinovaná zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="b3d60-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="b3d60-198">Chcete-li například povolit protokolování konzoly, nainstalujte `Microsoft.Extensions.Logging.Console` balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="b3d60-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="b3d60-199">Zavolejte `AddConsole` metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="b3d60-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="b3d60-200">V klientovi jazyka JavaScript existuje podobná `configureLogging` metoda.</span><span class="sxs-lookup"><span data-stu-id="b3d60-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="b3d60-201">Zadejte `LogLevel` hodnotu, která určuje minimální úroveň zpráv protokolu, které mají být vyprodukovány.</span><span class="sxs-lookup"><span data-stu-id="b3d60-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="b3d60-202">Protokoly se zapisují do okna konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="b3d60-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="b3d60-203">Místo `LogLevel` hodnoty můžete zadat také `string` hodnotu představující název úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="b3d60-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="b3d60-204">To je užitečné při konfiguraci SignalR protokolování v prostředích, kde nemáte přístup k `LogLevel` konstantám.</span><span class="sxs-lookup"><span data-stu-id="b3d60-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="b3d60-205">V následující tabulce jsou uvedeny dostupné úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="b3d60-205">The following table lists the available log levels.</span></span> <span data-ttu-id="b3d60-206">Hodnota, kterou zadáte, `configureLogging` nastaví **minimální** úroveň protokolu, která se bude protokolovat.</span><span class="sxs-lookup"><span data-stu-id="b3d60-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="b3d60-207">Zprávy zaznamenané na této úrovni **nebo úrovně uvedené za ní v tabulce**budou protokolovány.</span><span class="sxs-lookup"><span data-stu-id="b3d60-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="b3d60-208">String</span><span class="sxs-lookup"><span data-stu-id="b3d60-208">String</span></span>                      | <span data-ttu-id="b3d60-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="b3d60-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="b3d60-210">`info` **nebo** `information`</span><span class="sxs-lookup"><span data-stu-id="b3d60-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="b3d60-211">`warn` **nebo** `warning`</span><span class="sxs-lookup"><span data-stu-id="b3d60-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="b3d60-212">Chcete-li protokolování zcela zakázat, zadejte `signalR.LogLevel.None` v `configureLogging` metodě.</span><span class="sxs-lookup"><span data-stu-id="b3d60-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="b3d60-213">Další informace o protokolování naleznete v dokumentaci k [ SignalR diagnostice](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="b3d60-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="b3d60-214">SignalRKlient Java používá k protokolování knihovnu [SLF4J](https://www.slf4j.org/) .</span><span class="sxs-lookup"><span data-stu-id="b3d60-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="b3d60-215">Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí.</span><span class="sxs-lookup"><span data-stu-id="b3d60-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="b3d60-216">Následující fragment kódu ukazuje, jak používat `java.util.logging` s SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="b3d60-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="b3d60-217">Pokud ve svých závislostech nenakonfigurujete protokolování, SLF4J načte výchozí protokolovací nástroj No-operation s následující zprávou upozornění:</span><span class="sxs-lookup"><span data-stu-id="b3d60-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="b3d60-218">Tuto možnost lze bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="b3d60-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="b3d60-219">Konfigurace povolených přenosů</span><span class="sxs-lookup"><span data-stu-id="b3d60-219">Configure allowed transports</span></span>

<span data-ttu-id="b3d60-220">Přenosy používané nástrojem SignalR lze konfigurovat ve `WithUrl` volání ( `withUrl` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b3d60-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="b3d60-221">Bitové nebo hodnoty `HttpTransportType` lze použít k omezení klienta na používání pouze zadaných přenosů.</span><span class="sxs-lookup"><span data-stu-id="b3d60-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="b3d60-222">Ve výchozím nastavení jsou povolené všechny přenosy.</span><span class="sxs-lookup"><span data-stu-id="b3d60-222">All transports are enabled by default.</span></span>

<span data-ttu-id="b3d60-223">Pokud třeba chcete zakázat přenos událostí odeslaných serverem, ale povolit WebSockets a dlouhá připojení s dotazem:</span><span class="sxs-lookup"><span data-stu-id="b3d60-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="b3d60-224">V klientu jazyka JavaScript jsou přenosy konfigurovány nastavením `transport` pole v objektu Options, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="b3d60-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="b3d60-225">V této verzi je jediným dostupným přenosem klientský WebSocket v jazyce Java.</span><span class="sxs-lookup"><span data-stu-id="b3d60-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="b3d60-226">V klientovi Java se přenos vybere s `withTransport` metodou na `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="b3d60-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="b3d60-227">Klient Java standardně používá přenos pomocí protokolu WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b3d60-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="b3d60-228">SignalRKlient Java ještě nepodporuje přenosovou zálohu.</span><span class="sxs-lookup"><span data-stu-id="b3d60-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="b3d60-229">Konfigurace ověřování nosiče</span><span class="sxs-lookup"><span data-stu-id="b3d60-229">Configure bearer authentication</span></span>

<span data-ttu-id="b3d60-230">K poskytnutí ověřovacích dat spolu s SignalR požadavky použijte `AccessTokenProvider` možnost ( `accessTokenFactory` v jazyce JavaScript) k určení funkce, která vrací požadovaný přístupový token.</span><span class="sxs-lookup"><span data-stu-id="b3d60-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="b3d60-231">V klientovi .NET se tento přístupový token předává jako token ověřování HTTP "Bearer" (s použitím `Authorization` hlavičky s typem `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="b3d60-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="b3d60-232">V klientu jazyka JavaScript se přístupový token používá jako nosný token, **s výjimkou** případů, kdy rozhraní API prohlížeče omezuje možnost použít hlavičky (konkrétně v požadavcích na události odeslané serverem a objekty WebSockets).</span><span class="sxs-lookup"><span data-stu-id="b3d60-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="b3d60-233">V těchto případech je přístupový token k dispozici jako hodnota řetězce dotazu `access_token` .</span><span class="sxs-lookup"><span data-stu-id="b3d60-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="b3d60-234">V klientu .NET `AccessTokenProvider` lze možnost zadat pomocí delegáta možnosti v `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="b3d60-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="b3d60-235">V klientovi JavaScriptu je přístupový token nakonfigurovaný nastavením `accessTokenFactory` pole v objektu Options v `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="b3d60-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="b3d60-236">V SignalR klientovi Java můžete nakonfigurovat nosný token, který se bude používat pro ověřování, a to poskytnutím továrny přístupového tokenu [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="b3d60-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="b3d60-237">Pomocí [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) můžete zadat [RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="b3d60-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="b3d60-238">Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.</span><span class="sxs-lookup"><span data-stu-id="b3d60-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="b3d60-239">Konfigurace možností timeout a Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="b3d60-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="b3d60-240">Další možnosti konfigurace časového limitu a chování funkce Keep-Alive jsou k dispozici na `HubConnection` samotném objektu:</span><span class="sxs-lookup"><span data-stu-id="b3d60-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="b3d60-241">.NET</span><span class="sxs-lookup"><span data-stu-id="b3d60-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="b3d60-242">Možnost</span><span class="sxs-lookup"><span data-stu-id="b3d60-242">Option</span></span> | <span data-ttu-id="b3d60-243">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-243">Default value</span></span> | <span data-ttu-id="b3d60-244">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="b3d60-245">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="b3d60-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b3d60-246">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="b3d60-246">Timeout for server activity.</span></span> <span data-ttu-id="b3d60-247">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí `Closed` událost ( `onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="b3d60-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="b3d60-248">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="b3d60-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b3d60-249">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="b3d60-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="b3d60-250">15 sekund</span><span class="sxs-lookup"><span data-stu-id="b3d60-250">15 seconds</span></span> | <span data-ttu-id="b3d60-251">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="b3d60-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="b3d60-252">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a spustí `Closed` událost ( `onclose` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b3d60-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="b3d60-253">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="b3d60-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b3d60-254">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b3d60-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="b3d60-255">15 sekund</span><span class="sxs-lookup"><span data-stu-id="b3d60-255">15 seconds</span></span> | <span data-ttu-id="b3d60-256">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="b3d60-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b3d60-257">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="b3d60-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b3d60-258">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="b3d60-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="b3d60-259">V klientovi .NET jsou hodnoty časového limitu zadány jako `TimeSpan` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="b3d60-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="b3d60-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="b3d60-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="b3d60-261">Možnost</span><span class="sxs-lookup"><span data-stu-id="b3d60-261">Option</span></span> | <span data-ttu-id="b3d60-262">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-262">Default value</span></span> | <span data-ttu-id="b3d60-263">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="b3d60-264">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="b3d60-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b3d60-265">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="b3d60-265">Timeout for server activity.</span></span> <span data-ttu-id="b3d60-266">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onclose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="b3d60-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="b3d60-267">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="b3d60-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b3d60-268">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="b3d60-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="b3d60-269">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="b3d60-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="b3d60-270">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="b3d60-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b3d60-271">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="b3d60-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b3d60-272">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="b3d60-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="b3d60-273">Java</span><span class="sxs-lookup"><span data-stu-id="b3d60-273">Java</span></span>](#tab/java)

| <span data-ttu-id="b3d60-274">Možnost</span><span class="sxs-lookup"><span data-stu-id="b3d60-274">Option</span></span> | <span data-ttu-id="b3d60-275">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-275">Default value</span></span> | <span data-ttu-id="b3d60-276">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="b3d60-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="b3d60-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="b3d60-278">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="b3d60-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b3d60-279">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="b3d60-279">Timeout for server activity.</span></span> <span data-ttu-id="b3d60-280">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onClose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="b3d60-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="b3d60-281">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="b3d60-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b3d60-282">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="b3d60-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="b3d60-283">15 sekund</span><span class="sxs-lookup"><span data-stu-id="b3d60-283">15 seconds</span></span> | <span data-ttu-id="b3d60-284">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="b3d60-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="b3d60-285">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje `onClose` událost.</span><span class="sxs-lookup"><span data-stu-id="b3d60-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="b3d60-286">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="b3d60-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b3d60-287">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b3d60-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="b3d60-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="b3d60-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="b3d60-289">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="b3d60-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="b3d60-290">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="b3d60-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b3d60-291">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="b3d60-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b3d60-292">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="b3d60-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="b3d60-293">Konfigurace dalších možností</span><span class="sxs-lookup"><span data-stu-id="b3d60-293">Configure additional options</span></span>

<span data-ttu-id="b3d60-294">Další možnosti lze konfigurovat v `WithUrl` `withUrl` metodě (v jazyce JavaScript) na `HubConnectionBuilder` různých konfiguračních rozhraních API v `HttpHubConnectionBuilder` klientovi Java:</span><span class="sxs-lookup"><span data-stu-id="b3d60-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="b3d60-295">.NET</span><span class="sxs-lookup"><span data-stu-id="b3d60-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="b3d60-296">Možnost .NET</span><span class="sxs-lookup"><span data-stu-id="b3d60-296">.NET Option</span></span> |  <span data-ttu-id="b3d60-297">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-297">Default value</span></span> | <span data-ttu-id="b3d60-298">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="b3d60-299">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="b3d60-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="b3d60-300">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="b3d60-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b3d60-301">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="b3d60-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b3d60-302">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="b3d60-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="b3d60-303">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="b3d60-303">Empty</span></span> | <span data-ttu-id="b3d60-304">Kolekce certifikátů TLS pro odeslání na požadavky na ověření.</span><span class="sxs-lookup"><span data-stu-id="b3d60-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="b3d60-305">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="b3d60-305">Empty</span></span> | <span data-ttu-id="b3d60-306">Kolekce HTTP cookie s pro odeslání s každou žádostí http</span><span class="sxs-lookup"><span data-stu-id="b3d60-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="b3d60-307">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="b3d60-307">Empty</span></span> | <span data-ttu-id="b3d60-308">Přihlašovací údaje, které se mají poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="b3d60-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="b3d60-309">5 sekund</span><span class="sxs-lookup"><span data-stu-id="b3d60-309">5 seconds</span></span> | <span data-ttu-id="b3d60-310">Jenom objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b3d60-310">WebSockets only.</span></span> <span data-ttu-id="b3d60-311">Maximální doba, po jejímž uplynutí bude klient čekat na ukončení žádosti o uzavření.</span><span class="sxs-lookup"><span data-stu-id="b3d60-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="b3d60-312">Pokud server nepotvrdí zavření v této době, klient se odpojí.</span><span class="sxs-lookup"><span data-stu-id="b3d60-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="b3d60-313">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="b3d60-313">Empty</span></span> | <span data-ttu-id="b3d60-314">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="b3d60-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="b3d60-315">Delegát, který lze použít ke konfiguraci nebo nahrazení `HttpMessageHandler` používaného k odeslání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="b3d60-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="b3d60-316">Nepoužívá se pro připojení pomocí protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b3d60-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="b3d60-317">Tento delegát musí vracet hodnotu, která není null, a obdrží výchozí hodnotu jako parametr.</span><span class="sxs-lookup"><span data-stu-id="b3d60-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="b3d60-318">Buď změňte nastavení u této výchozí hodnoty a vraťte je, nebo vraťte novou `HttpMessageHandler` instanci.</span><span class="sxs-lookup"><span data-stu-id="b3d60-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="b3d60-319">**Při nahrazování obslužné rutiny Nezapomeňte zkopírovat nastavení, která chcete zachovat od poskytnuté obslužné rutiny, jinak konfigurované možnosti (například Cookie s a záhlaví) nebudou použity pro novou obslužnou rutinu.**</span><span class="sxs-lookup"><span data-stu-id="b3d60-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="b3d60-320">Proxy server HTTP, který se má použít při odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="b3d60-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="b3d60-321">Nastavte tuto logickou hodnotu pro odeslání výchozích přihlašovacích údajů pro požadavky HTTP a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b3d60-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="b3d60-322">To umožňuje použití ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="b3d60-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="b3d60-323">Delegát, který lze použít ke konfiguraci dalších možností protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b3d60-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="b3d60-324">Přijímá instanci [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , která se dá použít ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="b3d60-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="b3d60-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="b3d60-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="b3d60-326">Možnost JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="b3d60-326">JavaScript Option</span></span> | <span data-ttu-id="b3d60-327">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-327">Default Value</span></span> | <span data-ttu-id="b3d60-328">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="b3d60-329">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="b3d60-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `headers` | `null` | <span data-ttu-id="b3d60-330">Slovník hlaviček odeslaných s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="b3d60-330">Dictionary of headers sent with every HTTP request.</span></span> <span data-ttu-id="b3d60-331">Odesílání hlaviček v prohlížeči nefunguje pro objekty WebSockets ani <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> Stream.</span><span class="sxs-lookup"><span data-stu-id="b3d60-331">Sending headers in the browser doesn't work for WebSockets or the <xref:Microsoft.AspNetCore.Http.Connections.HttpTransportType.ServerSentEvents> stream.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="b3d60-332">Nastavte na `true` protokolování bajtů/znaků zpráv odesílaných a přijímaných klientem.</span><span class="sxs-lookup"><span data-stu-id="b3d60-332">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="b3d60-333">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="b3d60-333">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b3d60-334">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="b3d60-334">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b3d60-335">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="b3d60-335">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="b3d60-336">Určuje, jestli se budou odesílat přihlašovací údaje s požadavkem CORS.</span><span class="sxs-lookup"><span data-stu-id="b3d60-336">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="b3d60-337">Azure App Service používá cookie s pro rychlé relace a vyžaduje, aby tato možnost fungovala správně.</span><span class="sxs-lookup"><span data-stu-id="b3d60-337">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="b3d60-338">Další informace o CORS s SignalR najdete v tématu <xref:signalr/security#cross-origin-resource-sharing> .</span><span class="sxs-lookup"><span data-stu-id="b3d60-338">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="b3d60-339">Java</span><span class="sxs-lookup"><span data-stu-id="b3d60-339">Java</span></span>](#tab/java)

| <span data-ttu-id="b3d60-340">Možnost jazyka Java</span><span class="sxs-lookup"><span data-stu-id="b3d60-340">Java Option</span></span> | <span data-ttu-id="b3d60-341">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-341">Default Value</span></span> | <span data-ttu-id="b3d60-342">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-342">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="b3d60-343">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="b3d60-343">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="b3d60-344">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="b3d60-344">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b3d60-345">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="b3d60-345">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b3d60-346">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="b3d60-346">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="b3d60-347">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="b3d60-347">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="b3d60-348">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="b3d60-348">Empty</span></span> | <span data-ttu-id="b3d60-349">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="b3d60-349">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="b3d60-350">V klientu .NET můžete tyto možnosti upravit pomocí delegáta možností, který je k dispozici pro `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="b3d60-350">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="b3d60-351">V klientovi JavaScriptu lze tyto možnosti poskytnout v objektu jazyka JavaScript, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="b3d60-351">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="b3d60-352">V klientovi Java lze tyto možnosti nakonfigurovat s metodami `HttpHubConnectionBuilder` vrácenými z `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="b3d60-352">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="b3d60-353">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="b3d60-353">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="b3d60-354">Možnosti serializace JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="b3d60-354">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="b3d60-355">ASP.NET Core SignalR podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="b3d60-355">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="b3d60-356">Každý protokol má možnosti konfigurace serializace.</span><span class="sxs-lookup"><span data-stu-id="b3d60-356">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="b3d60-357">Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="b3d60-357">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="b3d60-358">`AddJsonProtocol`lze přidat po [Přidání SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) do `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b3d60-358">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b3d60-359">`AddJsonProtocol`Metoda přebírá delegáta, který přijímá `options` objekt.</span><span class="sxs-lookup"><span data-stu-id="b3d60-359">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="b3d60-360">Vlastnost [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) tohoto objektu je `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objekt, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="b3d60-360">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="b3d60-361">Další informace najdete v [System.Text.Jsv dokumentaci](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="b3d60-361">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="b3d60-362">Chcete-li například nakonfigurovat serializátor, aby neměnil velká a malá písmena názvů vlastností namísto výchozích názvů "camelCase", použijte následující kód v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b3d60-362">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="b3d60-363">V klientovi .NET `AddJsonProtocol` existuje stejná rozšiřující metoda v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="b3d60-363">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="b3d60-364">`Microsoft.Extensions.DependencyInjection`Obor názvů musí být importován, aby bylo možné přeložit metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="b3d60-364">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="b3d60-365">V tuto chvíli není možné konfigurovat serializaci JSON v klientovi jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b3d60-365">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="b3d60-366">Přepnout na Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="b3d60-366">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="b3d60-367">Pokud potřebujete funkce nástroje `Newtonsoft.Json` , které nejsou podporované v systému `System.Text.Json` , přečtěte si téma [Přepnutí na Newtonsoft.Jsna](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="b3d60-367">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="b3d60-368">Možnosti serializace MessagePack</span><span class="sxs-lookup"><span data-stu-id="b3d60-368">MessagePack serialization options</span></span>

<span data-ttu-id="b3d60-369">Serializaci MessagePack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="b3d60-369">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="b3d60-370">Další podrobnosti najdete [v SignalR tématu MessagePack](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="b3d60-370">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="b3d60-371">V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi v jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b3d60-371">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="b3d60-372">Konfigurovat možnosti serveru</span><span class="sxs-lookup"><span data-stu-id="b3d60-372">Configure server options</span></span>

<span data-ttu-id="b3d60-373">Následující tabulka popisuje možnosti konfigurace SignalR Center:</span><span class="sxs-lookup"><span data-stu-id="b3d60-373">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="b3d60-374">Možnost</span><span class="sxs-lookup"><span data-stu-id="b3d60-374">Option</span></span> | <span data-ttu-id="b3d60-375">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-375">Default Value</span></span> | <span data-ttu-id="b3d60-376">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-376">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="b3d60-377">30 sekund</span><span class="sxs-lookup"><span data-stu-id="b3d60-377">30 seconds</span></span> | <span data-ttu-id="b3d60-378">Server bude předpokládat, že je klient odpojený, pokud v tomto intervalu neobdržel zprávu (včetně Keep-Alive).</span><span class="sxs-lookup"><span data-stu-id="b3d60-378">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="b3d60-379">Může trvat delší dobu, než je tento časový limit, aby bylo možné klienta ve skutečnosti označit jako odpojený, protože to je implementováno.</span><span class="sxs-lookup"><span data-stu-id="b3d60-379">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="b3d60-380">Doporučená hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="b3d60-380">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="b3d60-381">15 sekund</span><span class="sxs-lookup"><span data-stu-id="b3d60-381">15 seconds</span></span> | <span data-ttu-id="b3d60-382">Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře.</span><span class="sxs-lookup"><span data-stu-id="b3d60-382">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="b3d60-383">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="b3d60-383">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b3d60-384">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b3d60-384">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="b3d60-385">15 sekund</span><span class="sxs-lookup"><span data-stu-id="b3d60-385">15 seconds</span></span> | <span data-ttu-id="b3d60-386">Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="b3d60-386">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="b3d60-387">Při změně `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení změňte nastavení v klientovi.</span><span class="sxs-lookup"><span data-stu-id="b3d60-387">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="b3d60-388">Doporučená `ServerTimeout` / `serverTimeoutInMilliseconds` hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="b3d60-388">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="b3d60-389">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="b3d60-389">All installed protocols</span></span> | <span data-ttu-id="b3d60-390">Protokoly podporované tímto rozbočovačem</span><span class="sxs-lookup"><span data-stu-id="b3d60-390">Protocols supported by this hub.</span></span> <span data-ttu-id="b3d60-391">Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly.</span><span class="sxs-lookup"><span data-stu-id="b3d60-391">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="b3d60-392">Pokud se `true` do klientů vrátí podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="b3d60-392">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="b3d60-393">Výchozí hodnota je `false` , protože tyto zprávy o výjimce můžou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="b3d60-393">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="b3d60-394">Maximální počet položek, které lze uložit do vyrovnávací paměti pro datové proudy pro odeslání klienta.</span><span class="sxs-lookup"><span data-stu-id="b3d60-394">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="b3d60-395">Pokud je dosaženo tohoto limitu, zpracování volání je blokováno, dokud server nezpracovává položky datového proudu.</span><span class="sxs-lookup"><span data-stu-id="b3d60-395">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="b3d60-396">32 KB</span><span class="sxs-lookup"><span data-stu-id="b3d60-396">32 KB</span></span> | <span data-ttu-id="b3d60-397">Maximální velikost jedné příchozí zprávy centra</span><span class="sxs-lookup"><span data-stu-id="b3d60-397">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="b3d60-398">Možnosti lze nakonfigurovat pro všechna centra tím, že poskytnete možnosti delegáta pro `AddSignalR` volání v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b3d60-398">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="b3d60-399">Možnosti pro jeden rozbočovač přepíší globální možnosti poskytované v `AddSignalR` a můžou být nakonfigurované pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="b3d60-399">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="b3d60-400">Rozšířené možnosti konfigurace protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="b3d60-400">Advanced HTTP configuration options</span></span>

<span data-ttu-id="b3d60-401">Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="b3d60-401">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="b3d60-402">Tyto možnosti jsou nakonfigurovány předáním delegáta [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b3d60-402">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="b3d60-403">Následující tabulka popisuje možnosti konfigurace SignalR rozšířených možností protokolu HTTP v ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="b3d60-403">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="b3d60-404">Možnost</span><span class="sxs-lookup"><span data-stu-id="b3d60-404">Option</span></span> | <span data-ttu-id="b3d60-405">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-405">Default Value</span></span> | <span data-ttu-id="b3d60-406">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-406">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="b3d60-407">32 KB</span><span class="sxs-lookup"><span data-stu-id="b3d60-407">32 KB</span></span> | <span data-ttu-id="b3d60-408">Maximální počet bajtů přijatých od klienta, které jsou ukládány do vyrovnávací paměti serveru před použitím protitlaku.</span><span class="sxs-lookup"><span data-stu-id="b3d60-408">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="b3d60-409">Zvýšením této hodnoty umožníte, aby server přijímal větší zprávy rychleji bez použití zatížení, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="b3d60-409">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="b3d60-410">Data se automaticky shromažďují z `Authorize` atributů použitých pro třídu centra.</span><span class="sxs-lookup"><span data-stu-id="b3d60-410">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="b3d60-411">Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru</span><span class="sxs-lookup"><span data-stu-id="b3d60-411">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="b3d60-412">32 KB</span><span class="sxs-lookup"><span data-stu-id="b3d60-412">32 KB</span></span> | <span data-ttu-id="b3d60-413">Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru před pozorováním protitlaku.</span><span class="sxs-lookup"><span data-stu-id="b3d60-413">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="b3d60-414">Zvýšením této hodnoty umožníte serveru rychleji ukládat větší zprávy bez nutnosti očekávat zatížení, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="b3d60-414">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="b3d60-415">Všechny přenosy jsou povolené.</span><span class="sxs-lookup"><span data-stu-id="b3d60-415">All Transports are enabled.</span></span> | <span data-ttu-id="b3d60-416">Bitové příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenos, který může klient použít pro připojení.</span><span class="sxs-lookup"><span data-stu-id="b3d60-416">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="b3d60-417">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="b3d60-417">See below.</span></span> | <span data-ttu-id="b3d60-418">Další možnosti specifické pro přenos dlouhého cyklického dotazování.</span><span class="sxs-lookup"><span data-stu-id="b3d60-418">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="b3d60-419">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="b3d60-419">See below.</span></span> | <span data-ttu-id="b3d60-420">Další možnosti specifické pro přenos pomocí protokolu WebSockets</span><span class="sxs-lookup"><span data-stu-id="b3d60-420">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="b3d60-421">0</span><span class="sxs-lookup"><span data-stu-id="b3d60-421">0</span></span> | <span data-ttu-id="b3d60-422">Zadejte minimální verzi protokolu Negotiate.</span><span class="sxs-lookup"><span data-stu-id="b3d60-422">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="b3d60-423">Slouží k omezení klientů na novější verze.</span><span class="sxs-lookup"><span data-stu-id="b3d60-423">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="b3d60-424">Přenos dlouhého cyklického dotazování má další možnosti, které je možné konfigurovat pomocí `LongPolling` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="b3d60-424">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="b3d60-425">Možnost</span><span class="sxs-lookup"><span data-stu-id="b3d60-425">Option</span></span> | <span data-ttu-id="b3d60-426">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-426">Default Value</span></span> | <span data-ttu-id="b3d60-427">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-427">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="b3d60-428">90 sekund</span><span class="sxs-lookup"><span data-stu-id="b3d60-428">90 seconds</span></span> | <span data-ttu-id="b3d60-429">Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jedné žádosti o cyklické dotazování.</span><span class="sxs-lookup"><span data-stu-id="b3d60-429">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="b3d60-430">Snížení této hodnoty způsobí, že klient bude vydávat nové požadavky na dotaz častěji.</span><span class="sxs-lookup"><span data-stu-id="b3d60-430">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="b3d60-431">Přenos protokolu WebSocket má další možnosti, které je možné konfigurovat pomocí `WebSockets` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="b3d60-431">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="b3d60-432">Možnost</span><span class="sxs-lookup"><span data-stu-id="b3d60-432">Option</span></span> | <span data-ttu-id="b3d60-433">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-433">Default Value</span></span> | <span data-ttu-id="b3d60-434">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-434">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="b3d60-435">5 sekund</span><span class="sxs-lookup"><span data-stu-id="b3d60-435">5 seconds</span></span> | <span data-ttu-id="b3d60-436">Pokud se po ukončení serveru aplikace v tomto časovém intervalu nepovede zavřít, připojení se ukončí.</span><span class="sxs-lookup"><span data-stu-id="b3d60-436">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="b3d60-437">Delegát, který lze použít k nastavení `Sec-WebSocket-Protocol` záhlaví na vlastní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="b3d60-437">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="b3d60-438">Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že se vrátí požadovaná hodnota.</span><span class="sxs-lookup"><span data-stu-id="b3d60-438">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="b3d60-439">Konfigurace možností klienta</span><span class="sxs-lookup"><span data-stu-id="b3d60-439">Configure client options</span></span>

<span data-ttu-id="b3d60-440">Možnosti klienta lze nakonfigurovat pro `HubConnectionBuilder` typ (k dispozici v klientech rozhraní .NET a v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b3d60-440">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="b3d60-441">Je také k dispozici v klientovi Java, ale `HttpHubConnectionBuilder` podtřídou je to, co obsahuje možnosti konfigurace tvůrce a také na `HubConnection` sebe sama.</span><span class="sxs-lookup"><span data-stu-id="b3d60-441">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="b3d60-442">Konfigurovat protokolování</span><span class="sxs-lookup"><span data-stu-id="b3d60-442">Configure logging</span></span>

<span data-ttu-id="b3d60-443">Protokolování je konfigurováno v klientovi .NET pomocí `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="b3d60-443">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="b3d60-444">Zprostředkovatelé protokolování a filtry je možné zaregistrovat stejným způsobem jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="b3d60-444">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="b3d60-445">Další informace najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="b3d60-445">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="b3d60-446">Aby bylo možné registrovat poskytovatele protokolování, je nutné nainstalovat potřebné balíčky.</span><span class="sxs-lookup"><span data-stu-id="b3d60-446">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="b3d60-447">Úplný seznam najdete v části [předdefinovaná zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="b3d60-447">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="b3d60-448">Chcete-li například povolit protokolování konzoly, nainstalujte `Microsoft.Extensions.Logging.Console` balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="b3d60-448">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="b3d60-449">Zavolejte `AddConsole` metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="b3d60-449">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="b3d60-450">V klientovi jazyka JavaScript existuje podobná `configureLogging` metoda.</span><span class="sxs-lookup"><span data-stu-id="b3d60-450">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="b3d60-451">Zadejte `LogLevel` hodnotu, která určuje minimální úroveň zpráv protokolu, které mají být vyprodukovány.</span><span class="sxs-lookup"><span data-stu-id="b3d60-451">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="b3d60-452">Protokoly se zapisují do okna konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="b3d60-452">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="b3d60-453">Místo `LogLevel` hodnoty můžete zadat také `string` hodnotu představující název úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="b3d60-453">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="b3d60-454">To je užitečné při konfiguraci SignalR protokolování v prostředích, kde nemáte přístup k `LogLevel` konstantám.</span><span class="sxs-lookup"><span data-stu-id="b3d60-454">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="b3d60-455">V následující tabulce jsou uvedeny dostupné úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="b3d60-455">The following table lists the available log levels.</span></span> <span data-ttu-id="b3d60-456">Hodnota, kterou zadáte, `configureLogging` nastaví **minimální** úroveň protokolu, která se bude protokolovat.</span><span class="sxs-lookup"><span data-stu-id="b3d60-456">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="b3d60-457">Zprávy zaznamenané na této úrovni **nebo úrovně uvedené za ní v tabulce**budou protokolovány.</span><span class="sxs-lookup"><span data-stu-id="b3d60-457">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="b3d60-458">String</span><span class="sxs-lookup"><span data-stu-id="b3d60-458">String</span></span>                      | <span data-ttu-id="b3d60-459">LogLevel</span><span class="sxs-lookup"><span data-stu-id="b3d60-459">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="b3d60-460">`info` **nebo** `information`</span><span class="sxs-lookup"><span data-stu-id="b3d60-460">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="b3d60-461">`warn` **nebo** `warning`</span><span class="sxs-lookup"><span data-stu-id="b3d60-461">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="b3d60-462">Chcete-li protokolování zcela zakázat, zadejte `signalR.LogLevel.None` v `configureLogging` metodě.</span><span class="sxs-lookup"><span data-stu-id="b3d60-462">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="b3d60-463">Další informace o protokolování naleznete v dokumentaci k [ SignalR diagnostice](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="b3d60-463">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="b3d60-464">SignalRKlient Java používá k protokolování knihovnu [SLF4J](https://www.slf4j.org/) .</span><span class="sxs-lookup"><span data-stu-id="b3d60-464">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="b3d60-465">Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí.</span><span class="sxs-lookup"><span data-stu-id="b3d60-465">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="b3d60-466">Následující fragment kódu ukazuje, jak používat `java.util.logging` s SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="b3d60-466">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="b3d60-467">Pokud ve svých závislostech nenakonfigurujete protokolování, SLF4J načte výchozí protokolovací nástroj No-operation s následující zprávou upozornění:</span><span class="sxs-lookup"><span data-stu-id="b3d60-467">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="b3d60-468">Tuto možnost lze bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="b3d60-468">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="b3d60-469">Konfigurace povolených přenosů</span><span class="sxs-lookup"><span data-stu-id="b3d60-469">Configure allowed transports</span></span>

<span data-ttu-id="b3d60-470">Přenosy používané nástrojem SignalR lze konfigurovat ve `WithUrl` volání ( `withUrl` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b3d60-470">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="b3d60-471">Bitové nebo hodnoty `HttpTransportType` lze použít k omezení klienta na používání pouze zadaných přenosů.</span><span class="sxs-lookup"><span data-stu-id="b3d60-471">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="b3d60-472">Ve výchozím nastavení jsou povolené všechny přenosy.</span><span class="sxs-lookup"><span data-stu-id="b3d60-472">All transports are enabled by default.</span></span>

<span data-ttu-id="b3d60-473">Pokud třeba chcete zakázat přenos událostí odeslaných serverem, ale povolit WebSockets a dlouhá připojení s dotazem:</span><span class="sxs-lookup"><span data-stu-id="b3d60-473">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="b3d60-474">V klientu jazyka JavaScript jsou přenosy konfigurovány nastavením `transport` pole v objektu Options, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="b3d60-474">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="b3d60-475">V této verzi je jediným dostupným přenosem klientský WebSocket v jazyce Java.</span><span class="sxs-lookup"><span data-stu-id="b3d60-475">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="b3d60-476">V klientovi Java se přenos vybere s `withTransport` metodou na `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="b3d60-476">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="b3d60-477">Klient Java standardně používá přenos pomocí protokolu WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b3d60-477">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="b3d60-478">SignalRKlient Java ještě nepodporuje přenosovou zálohu.</span><span class="sxs-lookup"><span data-stu-id="b3d60-478">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="b3d60-479">Konfigurace ověřování nosiče</span><span class="sxs-lookup"><span data-stu-id="b3d60-479">Configure bearer authentication</span></span>

<span data-ttu-id="b3d60-480">K poskytnutí ověřovacích dat spolu s SignalR požadavky použijte `AccessTokenProvider` možnost ( `accessTokenFactory` v jazyce JavaScript) k určení funkce, která vrací požadovaný přístupový token.</span><span class="sxs-lookup"><span data-stu-id="b3d60-480">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="b3d60-481">V klientovi .NET se tento přístupový token předává jako token ověřování HTTP "Bearer" (s použitím `Authorization` hlavičky s typem `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="b3d60-481">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="b3d60-482">V klientu jazyka JavaScript se přístupový token používá jako nosný token, **s výjimkou** případů, kdy rozhraní API prohlížeče omezuje možnost použít hlavičky (konkrétně v požadavcích na události odeslané serverem a objekty WebSockets).</span><span class="sxs-lookup"><span data-stu-id="b3d60-482">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="b3d60-483">V těchto případech je přístupový token k dispozici jako hodnota řetězce dotazu `access_token` .</span><span class="sxs-lookup"><span data-stu-id="b3d60-483">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="b3d60-484">V klientu .NET `AccessTokenProvider` lze možnost zadat pomocí delegáta možnosti v `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="b3d60-484">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="b3d60-485">V klientovi JavaScriptu je přístupový token nakonfigurovaný nastavením `accessTokenFactory` pole v objektu Options v `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="b3d60-485">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="b3d60-486">V SignalR klientovi Java můžete nakonfigurovat nosný token, který se bude používat pro ověřování, a to poskytnutím továrny přístupového tokenu [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="b3d60-486">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="b3d60-487">Pomocí [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) můžete zadat [RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="b3d60-487">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="b3d60-488">Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.</span><span class="sxs-lookup"><span data-stu-id="b3d60-488">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="b3d60-489">Konfigurace možností timeout a Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="b3d60-489">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="b3d60-490">Další možnosti konfigurace časového limitu a chování funkce Keep-Alive jsou k dispozici na `HubConnection` samotném objektu:</span><span class="sxs-lookup"><span data-stu-id="b3d60-490">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="b3d60-491">.NET</span><span class="sxs-lookup"><span data-stu-id="b3d60-491">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="b3d60-492">Možnost</span><span class="sxs-lookup"><span data-stu-id="b3d60-492">Option</span></span> | <span data-ttu-id="b3d60-493">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-493">Default value</span></span> | <span data-ttu-id="b3d60-494">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-494">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="b3d60-495">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="b3d60-495">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b3d60-496">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="b3d60-496">Timeout for server activity.</span></span> <span data-ttu-id="b3d60-497">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí `Closed` událost ( `onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="b3d60-497">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="b3d60-498">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="b3d60-498">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b3d60-499">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="b3d60-499">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="b3d60-500">15 sekund</span><span class="sxs-lookup"><span data-stu-id="b3d60-500">15 seconds</span></span> | <span data-ttu-id="b3d60-501">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="b3d60-501">Timeout for initial server handshake.</span></span> <span data-ttu-id="b3d60-502">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a spustí `Closed` událost ( `onclose` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b3d60-502">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="b3d60-503">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="b3d60-503">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b3d60-504">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b3d60-504">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="b3d60-505">15 sekund</span><span class="sxs-lookup"><span data-stu-id="b3d60-505">15 seconds</span></span> | <span data-ttu-id="b3d60-506">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="b3d60-506">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b3d60-507">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="b3d60-507">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b3d60-508">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="b3d60-508">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="b3d60-509">V klientovi .NET jsou hodnoty časového limitu zadány jako `TimeSpan` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="b3d60-509">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="b3d60-510">JavaScript</span><span class="sxs-lookup"><span data-stu-id="b3d60-510">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="b3d60-511">Možnost</span><span class="sxs-lookup"><span data-stu-id="b3d60-511">Option</span></span> | <span data-ttu-id="b3d60-512">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-512">Default value</span></span> | <span data-ttu-id="b3d60-513">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-513">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="b3d60-514">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="b3d60-514">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b3d60-515">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="b3d60-515">Timeout for server activity.</span></span> <span data-ttu-id="b3d60-516">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onclose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="b3d60-516">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="b3d60-517">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="b3d60-517">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b3d60-518">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="b3d60-518">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="b3d60-519">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="b3d60-519">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="b3d60-520">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="b3d60-520">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b3d60-521">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="b3d60-521">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b3d60-522">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="b3d60-522">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="b3d60-523">Java</span><span class="sxs-lookup"><span data-stu-id="b3d60-523">Java</span></span>](#tab/java)

| <span data-ttu-id="b3d60-524">Možnost</span><span class="sxs-lookup"><span data-stu-id="b3d60-524">Option</span></span> | <span data-ttu-id="b3d60-525">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-525">Default value</span></span> | <span data-ttu-id="b3d60-526">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-526">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="b3d60-527">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="b3d60-527">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="b3d60-528">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="b3d60-528">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b3d60-529">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="b3d60-529">Timeout for server activity.</span></span> <span data-ttu-id="b3d60-530">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onClose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="b3d60-530">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="b3d60-531">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="b3d60-531">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b3d60-532">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="b3d60-532">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="b3d60-533">15 sekund</span><span class="sxs-lookup"><span data-stu-id="b3d60-533">15 seconds</span></span> | <span data-ttu-id="b3d60-534">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="b3d60-534">Timeout for initial server handshake.</span></span> <span data-ttu-id="b3d60-535">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje `onClose` událost.</span><span class="sxs-lookup"><span data-stu-id="b3d60-535">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="b3d60-536">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="b3d60-536">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b3d60-537">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b3d60-537">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="b3d60-538">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="b3d60-538">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="b3d60-539">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="b3d60-539">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="b3d60-540">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="b3d60-540">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b3d60-541">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="b3d60-541">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b3d60-542">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="b3d60-542">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="b3d60-543">Konfigurace dalších možností</span><span class="sxs-lookup"><span data-stu-id="b3d60-543">Configure additional options</span></span>

<span data-ttu-id="b3d60-544">Další možnosti lze konfigurovat v `WithUrl` `withUrl` metodě (v jazyce JavaScript) na `HubConnectionBuilder` různých konfiguračních rozhraních API v `HttpHubConnectionBuilder` klientovi Java:</span><span class="sxs-lookup"><span data-stu-id="b3d60-544">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="b3d60-545">.NET</span><span class="sxs-lookup"><span data-stu-id="b3d60-545">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="b3d60-546">Možnost .NET</span><span class="sxs-lookup"><span data-stu-id="b3d60-546">.NET Option</span></span> |  <span data-ttu-id="b3d60-547">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-547">Default value</span></span> | <span data-ttu-id="b3d60-548">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-548">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="b3d60-549">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="b3d60-549">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="b3d60-550">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="b3d60-550">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b3d60-551">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="b3d60-551">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b3d60-552">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="b3d60-552">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="b3d60-553">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="b3d60-553">Empty</span></span> | <span data-ttu-id="b3d60-554">Kolekce certifikátů TLS pro odeslání na požadavky na ověření.</span><span class="sxs-lookup"><span data-stu-id="b3d60-554">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="b3d60-555">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="b3d60-555">Empty</span></span> | <span data-ttu-id="b3d60-556">Kolekce HTTP cookie s pro odeslání s každou žádostí http</span><span class="sxs-lookup"><span data-stu-id="b3d60-556">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="b3d60-557">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="b3d60-557">Empty</span></span> | <span data-ttu-id="b3d60-558">Přihlašovací údaje, které se mají poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="b3d60-558">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="b3d60-559">5 sekund</span><span class="sxs-lookup"><span data-stu-id="b3d60-559">5 seconds</span></span> | <span data-ttu-id="b3d60-560">Jenom objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b3d60-560">WebSockets only.</span></span> <span data-ttu-id="b3d60-561">Maximální doba, po jejímž uplynutí bude klient čekat na ukončení žádosti o uzavření.</span><span class="sxs-lookup"><span data-stu-id="b3d60-561">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="b3d60-562">Pokud server nepotvrdí zavření v této době, klient se odpojí.</span><span class="sxs-lookup"><span data-stu-id="b3d60-562">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="b3d60-563">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="b3d60-563">Empty</span></span> | <span data-ttu-id="b3d60-564">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="b3d60-564">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="b3d60-565">Delegát, který lze použít ke konfiguraci nebo nahrazení `HttpMessageHandler` používaného k odeslání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="b3d60-565">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="b3d60-566">Nepoužívá se pro připojení pomocí protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b3d60-566">Not used for WebSocket connections.</span></span> <span data-ttu-id="b3d60-567">Tento delegát musí vracet hodnotu, která není null, a obdrží výchozí hodnotu jako parametr.</span><span class="sxs-lookup"><span data-stu-id="b3d60-567">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="b3d60-568">Buď změňte nastavení u této výchozí hodnoty a vraťte je, nebo vraťte novou `HttpMessageHandler` instanci.</span><span class="sxs-lookup"><span data-stu-id="b3d60-568">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="b3d60-569">**Při nahrazování obslužné rutiny Nezapomeňte zkopírovat nastavení, která chcete zachovat od poskytnuté obslužné rutiny, jinak konfigurované možnosti (například Cookie s a záhlaví) nebudou použity pro novou obslužnou rutinu.**</span><span class="sxs-lookup"><span data-stu-id="b3d60-569">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="b3d60-570">Proxy server HTTP, který se má použít při odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="b3d60-570">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="b3d60-571">Nastavte tuto logickou hodnotu pro odeslání výchozích přihlašovacích údajů pro požadavky HTTP a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b3d60-571">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="b3d60-572">To umožňuje použití ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="b3d60-572">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="b3d60-573">Delegát, který lze použít ke konfiguraci dalších možností protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b3d60-573">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="b3d60-574">Přijímá instanci [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , která se dá použít ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="b3d60-574">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="b3d60-575">JavaScript</span><span class="sxs-lookup"><span data-stu-id="b3d60-575">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="b3d60-576">Možnost JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="b3d60-576">JavaScript Option</span></span> | <span data-ttu-id="b3d60-577">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-577">Default Value</span></span> | <span data-ttu-id="b3d60-578">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-578">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="b3d60-579">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="b3d60-579">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="b3d60-580">Nastavte na `true` protokolování bajtů/znaků zpráv odesílaných a přijímaných klientem.</span><span class="sxs-lookup"><span data-stu-id="b3d60-580">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="b3d60-581">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="b3d60-581">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b3d60-582">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="b3d60-582">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b3d60-583">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="b3d60-583">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="b3d60-584">Java</span><span class="sxs-lookup"><span data-stu-id="b3d60-584">Java</span></span>](#tab/java)

| <span data-ttu-id="b3d60-585">Možnost jazyka Java</span><span class="sxs-lookup"><span data-stu-id="b3d60-585">Java Option</span></span> | <span data-ttu-id="b3d60-586">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-586">Default Value</span></span> | <span data-ttu-id="b3d60-587">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-587">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="b3d60-588">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="b3d60-588">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="b3d60-589">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="b3d60-589">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b3d60-590">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="b3d60-590">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b3d60-591">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="b3d60-591">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="b3d60-592">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="b3d60-592">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="b3d60-593">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="b3d60-593">Empty</span></span> | <span data-ttu-id="b3d60-594">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="b3d60-594">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="b3d60-595">V klientu .NET můžete tyto možnosti upravit pomocí delegáta možností, který je k dispozici pro `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="b3d60-595">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="b3d60-596">V klientovi JavaScriptu lze tyto možnosti poskytnout v objektu jazyka JavaScript, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="b3d60-596">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="b3d60-597">V klientovi Java lze tyto možnosti nakonfigurovat s metodami `HttpHubConnectionBuilder` vrácenými z `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="b3d60-597">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="b3d60-598">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="b3d60-598">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="b3d60-599">Možnosti serializace JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="b3d60-599">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="b3d60-600">ASP.NET Core SignalR podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="b3d60-600">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="b3d60-601">Každý protokol má možnosti konfigurace serializace.</span><span class="sxs-lookup"><span data-stu-id="b3d60-601">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="b3d60-602">Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="b3d60-602">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="b3d60-603">`AddJsonProtocol`lze přidat po [Přidání SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) do `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b3d60-603">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b3d60-604">`AddJsonProtocol`Metoda přebírá delegáta, který přijímá `options` objekt.</span><span class="sxs-lookup"><span data-stu-id="b3d60-604">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="b3d60-605">Vlastnost [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) tohoto objektu je `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objekt, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="b3d60-605">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="b3d60-606">Další informace najdete v [System.Text.Jsv dokumentaci](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="b3d60-606">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="b3d60-607">Chcete-li například nakonfigurovat serializátor, aby neměnil velká a malá písmena názvů vlastností namísto výchozích názvů "camelCase", použijte následující kód v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b3d60-607">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="b3d60-608">V klientovi .NET `AddJsonProtocol` existuje stejná rozšiřující metoda v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="b3d60-608">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="b3d60-609">`Microsoft.Extensions.DependencyInjection`Obor názvů musí být importován, aby bylo možné přeložit metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="b3d60-609">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="b3d60-610">V tuto chvíli není možné konfigurovat serializaci JSON v klientovi jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b3d60-610">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="b3d60-611">Přepnout na Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="b3d60-611">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="b3d60-612">Pokud potřebujete funkce nástroje `Newtonsoft.Json` , které nejsou podporované v systému `System.Text.Json` , přečtěte si téma [Přepnutí na Newtonsoft.Jsna](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="b3d60-612">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="b3d60-613">Možnosti serializace MessagePack</span><span class="sxs-lookup"><span data-stu-id="b3d60-613">MessagePack serialization options</span></span>

<span data-ttu-id="b3d60-614">Serializaci MessagePack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="b3d60-614">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="b3d60-615">Další podrobnosti najdete [v SignalR tématu MessagePack](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="b3d60-615">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="b3d60-616">V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi v jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b3d60-616">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="b3d60-617">Konfigurovat možnosti serveru</span><span class="sxs-lookup"><span data-stu-id="b3d60-617">Configure server options</span></span>

<span data-ttu-id="b3d60-618">Následující tabulka popisuje možnosti konfigurace SignalR Center:</span><span class="sxs-lookup"><span data-stu-id="b3d60-618">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="b3d60-619">Možnost</span><span class="sxs-lookup"><span data-stu-id="b3d60-619">Option</span></span> | <span data-ttu-id="b3d60-620">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-620">Default Value</span></span> | <span data-ttu-id="b3d60-621">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-621">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="b3d60-622">30 sekund</span><span class="sxs-lookup"><span data-stu-id="b3d60-622">30 seconds</span></span> | <span data-ttu-id="b3d60-623">Server bude předpokládat, že je klient odpojený, pokud v tomto intervalu neobdržel zprávu (včetně Keep-Alive).</span><span class="sxs-lookup"><span data-stu-id="b3d60-623">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="b3d60-624">Může trvat delší dobu, než je tento časový limit, aby bylo možné klienta ve skutečnosti označit jako odpojený, protože to je implementováno.</span><span class="sxs-lookup"><span data-stu-id="b3d60-624">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="b3d60-625">Doporučená hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="b3d60-625">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="b3d60-626">15 sekund</span><span class="sxs-lookup"><span data-stu-id="b3d60-626">15 seconds</span></span> | <span data-ttu-id="b3d60-627">Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře.</span><span class="sxs-lookup"><span data-stu-id="b3d60-627">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="b3d60-628">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="b3d60-628">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b3d60-629">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b3d60-629">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="b3d60-630">15 sekund</span><span class="sxs-lookup"><span data-stu-id="b3d60-630">15 seconds</span></span> | <span data-ttu-id="b3d60-631">Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="b3d60-631">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="b3d60-632">Při změně `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení změňte nastavení v klientovi.</span><span class="sxs-lookup"><span data-stu-id="b3d60-632">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="b3d60-633">Doporučená `ServerTimeout` / `serverTimeoutInMilliseconds` hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="b3d60-633">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="b3d60-634">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="b3d60-634">All installed protocols</span></span> | <span data-ttu-id="b3d60-635">Protokoly podporované tímto rozbočovačem</span><span class="sxs-lookup"><span data-stu-id="b3d60-635">Protocols supported by this hub.</span></span> <span data-ttu-id="b3d60-636">Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly.</span><span class="sxs-lookup"><span data-stu-id="b3d60-636">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="b3d60-637">Pokud se `true` do klientů vrátí podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="b3d60-637">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="b3d60-638">Výchozí hodnota je `false` , protože tyto zprávy o výjimce můžou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="b3d60-638">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="b3d60-639">Maximální počet položek, které lze uložit do vyrovnávací paměti pro datové proudy pro odeslání klienta.</span><span class="sxs-lookup"><span data-stu-id="b3d60-639">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="b3d60-640">Pokud je dosaženo tohoto limitu, zpracování volání je blokováno, dokud server nezpracovává položky datového proudu.</span><span class="sxs-lookup"><span data-stu-id="b3d60-640">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="b3d60-641">32 KB</span><span class="sxs-lookup"><span data-stu-id="b3d60-641">32 KB</span></span> | <span data-ttu-id="b3d60-642">Maximální velikost jedné příchozí zprávy centra</span><span class="sxs-lookup"><span data-stu-id="b3d60-642">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="b3d60-643">Možnosti lze nakonfigurovat pro všechna centra tím, že poskytnete možnosti delegáta pro `AddSignalR` volání v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b3d60-643">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="b3d60-644">Možnosti pro jeden rozbočovač přepíší globální možnosti poskytované v `AddSignalR` a můžou být nakonfigurované pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="b3d60-644">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="b3d60-645">Rozšířené možnosti konfigurace protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="b3d60-645">Advanced HTTP configuration options</span></span>

<span data-ttu-id="b3d60-646">Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="b3d60-646">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="b3d60-647">Tyto možnosti jsou nakonfigurovány předáním delegáta [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b3d60-647">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="b3d60-648">Následující tabulka popisuje možnosti konfigurace SignalR rozšířených možností protokolu HTTP v ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="b3d60-648">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="b3d60-649">Možnost</span><span class="sxs-lookup"><span data-stu-id="b3d60-649">Option</span></span> | <span data-ttu-id="b3d60-650">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-650">Default Value</span></span> | <span data-ttu-id="b3d60-651">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-651">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="b3d60-652">32 KB</span><span class="sxs-lookup"><span data-stu-id="b3d60-652">32 KB</span></span> | <span data-ttu-id="b3d60-653">Maximální počet bajtů přijatých od klienta, které jsou ukládány do vyrovnávací paměti serveru před použitím protitlaku.</span><span class="sxs-lookup"><span data-stu-id="b3d60-653">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="b3d60-654">Zvýšením této hodnoty umožníte, aby server přijímal větší zprávy rychleji bez použití zatížení, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="b3d60-654">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="b3d60-655">Data se automaticky shromažďují z `Authorize` atributů použitých pro třídu centra.</span><span class="sxs-lookup"><span data-stu-id="b3d60-655">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="b3d60-656">Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru</span><span class="sxs-lookup"><span data-stu-id="b3d60-656">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="b3d60-657">32 KB</span><span class="sxs-lookup"><span data-stu-id="b3d60-657">32 KB</span></span> | <span data-ttu-id="b3d60-658">Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru před pozorováním protitlaku.</span><span class="sxs-lookup"><span data-stu-id="b3d60-658">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="b3d60-659">Zvýšením této hodnoty umožníte serveru rychleji ukládat větší zprávy bez nutnosti očekávat zatížení, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="b3d60-659">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="b3d60-660">Všechny přenosy jsou povolené.</span><span class="sxs-lookup"><span data-stu-id="b3d60-660">All Transports are enabled.</span></span> | <span data-ttu-id="b3d60-661">Bitové příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenos, který může klient použít pro připojení.</span><span class="sxs-lookup"><span data-stu-id="b3d60-661">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="b3d60-662">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="b3d60-662">See below.</span></span> | <span data-ttu-id="b3d60-663">Další možnosti specifické pro přenos dlouhého cyklického dotazování.</span><span class="sxs-lookup"><span data-stu-id="b3d60-663">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="b3d60-664">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="b3d60-664">See below.</span></span> | <span data-ttu-id="b3d60-665">Další možnosti specifické pro přenos pomocí protokolu WebSockets</span><span class="sxs-lookup"><span data-stu-id="b3d60-665">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="b3d60-666">Přenos dlouhého cyklického dotazování má další možnosti, které je možné konfigurovat pomocí `LongPolling` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="b3d60-666">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="b3d60-667">Možnost</span><span class="sxs-lookup"><span data-stu-id="b3d60-667">Option</span></span> | <span data-ttu-id="b3d60-668">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-668">Default Value</span></span> | <span data-ttu-id="b3d60-669">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-669">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="b3d60-670">90 sekund</span><span class="sxs-lookup"><span data-stu-id="b3d60-670">90 seconds</span></span> | <span data-ttu-id="b3d60-671">Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jedné žádosti o cyklické dotazování.</span><span class="sxs-lookup"><span data-stu-id="b3d60-671">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="b3d60-672">Snížení této hodnoty způsobí, že klient bude vydávat nové požadavky na dotaz častěji.</span><span class="sxs-lookup"><span data-stu-id="b3d60-672">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="b3d60-673">Přenos protokolu WebSocket má další možnosti, které je možné konfigurovat pomocí `WebSockets` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="b3d60-673">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="b3d60-674">Možnost</span><span class="sxs-lookup"><span data-stu-id="b3d60-674">Option</span></span> | <span data-ttu-id="b3d60-675">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-675">Default Value</span></span> | <span data-ttu-id="b3d60-676">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-676">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="b3d60-677">5 sekund</span><span class="sxs-lookup"><span data-stu-id="b3d60-677">5 seconds</span></span> | <span data-ttu-id="b3d60-678">Pokud se po ukončení serveru aplikace v tomto časovém intervalu nepovede zavřít, připojení se ukončí.</span><span class="sxs-lookup"><span data-stu-id="b3d60-678">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="b3d60-679">Delegát, který lze použít k nastavení `Sec-WebSocket-Protocol` záhlaví na vlastní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="b3d60-679">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="b3d60-680">Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že se vrátí požadovaná hodnota.</span><span class="sxs-lookup"><span data-stu-id="b3d60-680">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="b3d60-681">Konfigurace možností klienta</span><span class="sxs-lookup"><span data-stu-id="b3d60-681">Configure client options</span></span>

<span data-ttu-id="b3d60-682">Možnosti klienta lze nakonfigurovat pro `HubConnectionBuilder` typ (k dispozici v klientech rozhraní .NET a v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b3d60-682">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="b3d60-683">Je také k dispozici v klientovi Java, ale `HttpHubConnectionBuilder` podtřídou je to, co obsahuje možnosti konfigurace tvůrce a také na `HubConnection` sebe sama.</span><span class="sxs-lookup"><span data-stu-id="b3d60-683">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="b3d60-684">Konfigurovat protokolování</span><span class="sxs-lookup"><span data-stu-id="b3d60-684">Configure logging</span></span>

<span data-ttu-id="b3d60-685">Protokolování je konfigurováno v klientovi .NET pomocí `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="b3d60-685">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="b3d60-686">Zprostředkovatelé protokolování a filtry je možné zaregistrovat stejným způsobem jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="b3d60-686">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="b3d60-687">Další informace najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="b3d60-687">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="b3d60-688">Aby bylo možné registrovat poskytovatele protokolování, je nutné nainstalovat potřebné balíčky.</span><span class="sxs-lookup"><span data-stu-id="b3d60-688">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="b3d60-689">Úplný seznam najdete v části [předdefinovaná zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="b3d60-689">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="b3d60-690">Chcete-li například povolit protokolování konzoly, nainstalujte `Microsoft.Extensions.Logging.Console` balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="b3d60-690">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="b3d60-691">Zavolejte `AddConsole` metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="b3d60-691">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="b3d60-692">V klientovi jazyka JavaScript existuje podobná `configureLogging` metoda.</span><span class="sxs-lookup"><span data-stu-id="b3d60-692">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="b3d60-693">Zadejte `LogLevel` hodnotu, která určuje minimální úroveň zpráv protokolu, které mají být vyprodukovány.</span><span class="sxs-lookup"><span data-stu-id="b3d60-693">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="b3d60-694">Protokoly se zapisují do okna konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="b3d60-694">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="b3d60-695">Místo `LogLevel` hodnoty můžete zadat také `string` hodnotu představující název úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="b3d60-695">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="b3d60-696">To je užitečné při konfiguraci SignalR protokolování v prostředích, kde nemáte přístup k `LogLevel` konstantám.</span><span class="sxs-lookup"><span data-stu-id="b3d60-696">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="b3d60-697">V následující tabulce jsou uvedeny dostupné úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="b3d60-697">The following table lists the available log levels.</span></span> <span data-ttu-id="b3d60-698">Hodnota, kterou zadáte, `configureLogging` nastaví **minimální** úroveň protokolu, která se bude protokolovat.</span><span class="sxs-lookup"><span data-stu-id="b3d60-698">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="b3d60-699">Zprávy zaznamenané na této úrovni **nebo úrovně uvedené za ní v tabulce**budou protokolovány.</span><span class="sxs-lookup"><span data-stu-id="b3d60-699">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="b3d60-700">String</span><span class="sxs-lookup"><span data-stu-id="b3d60-700">String</span></span>                      | <span data-ttu-id="b3d60-701">LogLevel</span><span class="sxs-lookup"><span data-stu-id="b3d60-701">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="b3d60-702">`info` **nebo** `information`</span><span class="sxs-lookup"><span data-stu-id="b3d60-702">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="b3d60-703">`warn` **nebo** `warning`</span><span class="sxs-lookup"><span data-stu-id="b3d60-703">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="b3d60-704">Chcete-li protokolování zcela zakázat, zadejte `signalR.LogLevel.None` v `configureLogging` metodě.</span><span class="sxs-lookup"><span data-stu-id="b3d60-704">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="b3d60-705">Další informace o protokolování naleznete v dokumentaci k [ SignalR diagnostice](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="b3d60-705">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="b3d60-706">SignalRKlient Java používá k protokolování knihovnu [SLF4J](https://www.slf4j.org/) .</span><span class="sxs-lookup"><span data-stu-id="b3d60-706">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="b3d60-707">Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí.</span><span class="sxs-lookup"><span data-stu-id="b3d60-707">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="b3d60-708">Následující fragment kódu ukazuje, jak používat `java.util.logging` s SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="b3d60-708">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="b3d60-709">Pokud ve svých závislostech nenakonfigurujete protokolování, SLF4J načte výchozí protokolovací nástroj No-operation s následující zprávou upozornění:</span><span class="sxs-lookup"><span data-stu-id="b3d60-709">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="b3d60-710">Tuto možnost lze bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="b3d60-710">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="b3d60-711">Konfigurace povolených přenosů</span><span class="sxs-lookup"><span data-stu-id="b3d60-711">Configure allowed transports</span></span>

<span data-ttu-id="b3d60-712">Přenosy používané nástrojem SignalR lze konfigurovat ve `WithUrl` volání ( `withUrl` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b3d60-712">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="b3d60-713">Bitové nebo hodnoty `HttpTransportType` lze použít k omezení klienta na používání pouze zadaných přenosů.</span><span class="sxs-lookup"><span data-stu-id="b3d60-713">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="b3d60-714">Ve výchozím nastavení jsou povolené všechny přenosy.</span><span class="sxs-lookup"><span data-stu-id="b3d60-714">All transports are enabled by default.</span></span>

<span data-ttu-id="b3d60-715">Pokud třeba chcete zakázat přenos událostí odeslaných serverem, ale povolit WebSockets a dlouhá připojení s dotazem:</span><span class="sxs-lookup"><span data-stu-id="b3d60-715">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="b3d60-716">V klientu jazyka JavaScript jsou přenosy konfigurovány nastavením `transport` pole v objektu Options, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="b3d60-716">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="b3d60-717">V této verzi je jediným dostupným přenosem klientský WebSocket v jazyce Java.</span><span class="sxs-lookup"><span data-stu-id="b3d60-717">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="b3d60-718">V klientovi Java se přenos vybere s `withTransport` metodou na `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="b3d60-718">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="b3d60-719">Klient Java standardně používá přenos pomocí protokolu WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b3d60-719">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="b3d60-720">SignalRKlient Java ještě nepodporuje přenosovou zálohu.</span><span class="sxs-lookup"><span data-stu-id="b3d60-720">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="b3d60-721">Konfigurace ověřování nosiče</span><span class="sxs-lookup"><span data-stu-id="b3d60-721">Configure bearer authentication</span></span>

<span data-ttu-id="b3d60-722">K poskytnutí ověřovacích dat spolu s SignalR požadavky použijte `AccessTokenProvider` možnost ( `accessTokenFactory` v jazyce JavaScript) k určení funkce, která vrací požadovaný přístupový token.</span><span class="sxs-lookup"><span data-stu-id="b3d60-722">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="b3d60-723">V klientovi .NET se tento přístupový token předává jako token ověřování HTTP "Bearer" (s použitím `Authorization` hlavičky s typem `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="b3d60-723">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="b3d60-724">V klientu jazyka JavaScript se přístupový token používá jako nosný token, **s výjimkou** případů, kdy rozhraní API prohlížeče omezuje možnost použít hlavičky (konkrétně v požadavcích na události odeslané serverem a objekty WebSockets).</span><span class="sxs-lookup"><span data-stu-id="b3d60-724">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="b3d60-725">V těchto případech je přístupový token k dispozici jako hodnota řetězce dotazu `access_token` .</span><span class="sxs-lookup"><span data-stu-id="b3d60-725">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="b3d60-726">V klientu .NET `AccessTokenProvider` lze možnost zadat pomocí delegáta možnosti v `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="b3d60-726">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="b3d60-727">V klientovi JavaScriptu je přístupový token nakonfigurovaný nastavením `accessTokenFactory` pole v objektu Options v `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="b3d60-727">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="b3d60-728">V SignalR klientovi Java můžete nakonfigurovat nosný token, který se bude používat pro ověřování, a to poskytnutím továrny přístupového tokenu [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="b3d60-728">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="b3d60-729">Pomocí [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) můžete zadat [RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="b3d60-729">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="b3d60-730">Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.</span><span class="sxs-lookup"><span data-stu-id="b3d60-730">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="b3d60-731">Konfigurace možností timeout a Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="b3d60-731">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="b3d60-732">Další možnosti konfigurace časového limitu a chování funkce Keep-Alive jsou k dispozici na `HubConnection` samotném objektu:</span><span class="sxs-lookup"><span data-stu-id="b3d60-732">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="b3d60-733">.NET</span><span class="sxs-lookup"><span data-stu-id="b3d60-733">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="b3d60-734">Možnost</span><span class="sxs-lookup"><span data-stu-id="b3d60-734">Option</span></span> | <span data-ttu-id="b3d60-735">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-735">Default value</span></span> | <span data-ttu-id="b3d60-736">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-736">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="b3d60-737">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="b3d60-737">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b3d60-738">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="b3d60-738">Timeout for server activity.</span></span> <span data-ttu-id="b3d60-739">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí `Closed` událost ( `onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="b3d60-739">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="b3d60-740">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="b3d60-740">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b3d60-741">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="b3d60-741">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="b3d60-742">15 sekund</span><span class="sxs-lookup"><span data-stu-id="b3d60-742">15 seconds</span></span> | <span data-ttu-id="b3d60-743">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="b3d60-743">Timeout for initial server handshake.</span></span> <span data-ttu-id="b3d60-744">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a spustí `Closed` událost ( `onclose` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b3d60-744">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="b3d60-745">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="b3d60-745">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b3d60-746">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b3d60-746">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="b3d60-747">15 sekund</span><span class="sxs-lookup"><span data-stu-id="b3d60-747">15 seconds</span></span> | <span data-ttu-id="b3d60-748">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="b3d60-748">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b3d60-749">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="b3d60-749">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b3d60-750">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="b3d60-750">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="b3d60-751">V klientovi .NET jsou hodnoty časového limitu zadány jako `TimeSpan` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="b3d60-751">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="b3d60-752">JavaScript</span><span class="sxs-lookup"><span data-stu-id="b3d60-752">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="b3d60-753">Možnost</span><span class="sxs-lookup"><span data-stu-id="b3d60-753">Option</span></span> | <span data-ttu-id="b3d60-754">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-754">Default value</span></span> | <span data-ttu-id="b3d60-755">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-755">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="b3d60-756">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="b3d60-756">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b3d60-757">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="b3d60-757">Timeout for server activity.</span></span> <span data-ttu-id="b3d60-758">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onclose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="b3d60-758">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="b3d60-759">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="b3d60-759">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b3d60-760">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="b3d60-760">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="b3d60-761">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="b3d60-761">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="b3d60-762">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="b3d60-762">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b3d60-763">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="b3d60-763">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b3d60-764">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="b3d60-764">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="b3d60-765">Java</span><span class="sxs-lookup"><span data-stu-id="b3d60-765">Java</span></span>](#tab/java)

| <span data-ttu-id="b3d60-766">Možnost</span><span class="sxs-lookup"><span data-stu-id="b3d60-766">Option</span></span> | <span data-ttu-id="b3d60-767">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-767">Default value</span></span> | <span data-ttu-id="b3d60-768">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-768">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="b3d60-769">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="b3d60-769">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="b3d60-770">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="b3d60-770">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b3d60-771">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="b3d60-771">Timeout for server activity.</span></span> <span data-ttu-id="b3d60-772">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onClose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="b3d60-772">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="b3d60-773">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="b3d60-773">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b3d60-774">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="b3d60-774">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="b3d60-775">15 sekund</span><span class="sxs-lookup"><span data-stu-id="b3d60-775">15 seconds</span></span> | <span data-ttu-id="b3d60-776">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="b3d60-776">Timeout for initial server handshake.</span></span> <span data-ttu-id="b3d60-777">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje `onClose` událost.</span><span class="sxs-lookup"><span data-stu-id="b3d60-777">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="b3d60-778">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="b3d60-778">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b3d60-779">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b3d60-779">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="b3d60-780">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="b3d60-780">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="b3d60-781">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="b3d60-781">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="b3d60-782">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="b3d60-782">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b3d60-783">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="b3d60-783">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b3d60-784">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="b3d60-784">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="b3d60-785">Konfigurace dalších možností</span><span class="sxs-lookup"><span data-stu-id="b3d60-785">Configure additional options</span></span>

<span data-ttu-id="b3d60-786">Další možnosti lze konfigurovat v `WithUrl` `withUrl` metodě (v jazyce JavaScript) na `HubConnectionBuilder` různých konfiguračních rozhraních API v `HttpHubConnectionBuilder` klientovi Java:</span><span class="sxs-lookup"><span data-stu-id="b3d60-786">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="b3d60-787">.NET</span><span class="sxs-lookup"><span data-stu-id="b3d60-787">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="b3d60-788">Možnost .NET</span><span class="sxs-lookup"><span data-stu-id="b3d60-788">.NET Option</span></span> |  <span data-ttu-id="b3d60-789">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-789">Default value</span></span> | <span data-ttu-id="b3d60-790">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-790">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="b3d60-791">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="b3d60-791">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="b3d60-792">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="b3d60-792">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b3d60-793">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="b3d60-793">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b3d60-794">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="b3d60-794">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="b3d60-795">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="b3d60-795">Empty</span></span> | <span data-ttu-id="b3d60-796">Kolekce certifikátů TLS pro odeslání na požadavky na ověření.</span><span class="sxs-lookup"><span data-stu-id="b3d60-796">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="b3d60-797">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="b3d60-797">Empty</span></span> | <span data-ttu-id="b3d60-798">Kolekce HTTP cookie s pro odeslání s každou žádostí http</span><span class="sxs-lookup"><span data-stu-id="b3d60-798">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="b3d60-799">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="b3d60-799">Empty</span></span> | <span data-ttu-id="b3d60-800">Přihlašovací údaje, které se mají poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="b3d60-800">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="b3d60-801">5 sekund</span><span class="sxs-lookup"><span data-stu-id="b3d60-801">5 seconds</span></span> | <span data-ttu-id="b3d60-802">Jenom objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b3d60-802">WebSockets only.</span></span> <span data-ttu-id="b3d60-803">Maximální doba, po jejímž uplynutí bude klient čekat na ukončení žádosti o uzavření.</span><span class="sxs-lookup"><span data-stu-id="b3d60-803">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="b3d60-804">Pokud server nepotvrdí zavření v této době, klient se odpojí.</span><span class="sxs-lookup"><span data-stu-id="b3d60-804">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="b3d60-805">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="b3d60-805">Empty</span></span> | <span data-ttu-id="b3d60-806">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="b3d60-806">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="b3d60-807">Delegát, který lze použít ke konfiguraci nebo nahrazení `HttpMessageHandler` používaného k odeslání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="b3d60-807">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="b3d60-808">Nepoužívá se pro připojení pomocí protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b3d60-808">Not used for WebSocket connections.</span></span> <span data-ttu-id="b3d60-809">Tento delegát musí vracet hodnotu, která není null, a obdrží výchozí hodnotu jako parametr.</span><span class="sxs-lookup"><span data-stu-id="b3d60-809">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="b3d60-810">Buď změňte nastavení u této výchozí hodnoty a vraťte je, nebo vraťte novou `HttpMessageHandler` instanci.</span><span class="sxs-lookup"><span data-stu-id="b3d60-810">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="b3d60-811">**Při nahrazování obslužné rutiny Nezapomeňte zkopírovat nastavení, která chcete zachovat od poskytnuté obslužné rutiny, jinak konfigurované možnosti (například Cookie s a záhlaví) nebudou použity pro novou obslužnou rutinu.**</span><span class="sxs-lookup"><span data-stu-id="b3d60-811">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="b3d60-812">Proxy server HTTP, který se má použít při odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="b3d60-812">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="b3d60-813">Nastavte tuto logickou hodnotu pro odeslání výchozích přihlašovacích údajů pro požadavky HTTP a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b3d60-813">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="b3d60-814">To umožňuje použití ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="b3d60-814">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="b3d60-815">Delegát, který lze použít ke konfiguraci dalších možností protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b3d60-815">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="b3d60-816">Přijímá instanci [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , která se dá použít ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="b3d60-816">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="b3d60-817">JavaScript</span><span class="sxs-lookup"><span data-stu-id="b3d60-817">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="b3d60-818">Možnost JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="b3d60-818">JavaScript Option</span></span> | <span data-ttu-id="b3d60-819">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-819">Default Value</span></span> | <span data-ttu-id="b3d60-820">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-820">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="b3d60-821">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="b3d60-821">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="b3d60-822">Nastavte na `true` protokolování bajtů/znaků zpráv odesílaných a přijímaných klientem.</span><span class="sxs-lookup"><span data-stu-id="b3d60-822">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="b3d60-823">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="b3d60-823">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b3d60-824">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="b3d60-824">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b3d60-825">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="b3d60-825">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="b3d60-826">Java</span><span class="sxs-lookup"><span data-stu-id="b3d60-826">Java</span></span>](#tab/java)

| <span data-ttu-id="b3d60-827">Možnost jazyka Java</span><span class="sxs-lookup"><span data-stu-id="b3d60-827">Java Option</span></span> | <span data-ttu-id="b3d60-828">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-828">Default Value</span></span> | <span data-ttu-id="b3d60-829">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-829">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="b3d60-830">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="b3d60-830">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="b3d60-831">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="b3d60-831">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b3d60-832">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="b3d60-832">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b3d60-833">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="b3d60-833">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="b3d60-834">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="b3d60-834">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="b3d60-835">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="b3d60-835">Empty</span></span> | <span data-ttu-id="b3d60-836">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="b3d60-836">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="b3d60-837">V klientu .NET můžete tyto možnosti upravit pomocí delegáta možností, který je k dispozici pro `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="b3d60-837">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="b3d60-838">V klientovi JavaScriptu lze tyto možnosti poskytnout v objektu jazyka JavaScript, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="b3d60-838">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="b3d60-839">V klientovi Java lze tyto možnosti nakonfigurovat s metodami `HttpHubConnectionBuilder` vrácenými z `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="b3d60-839">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="b3d60-840">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="b3d60-840">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="b3d60-841">Možnosti serializace JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="b3d60-841">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="b3d60-842">ASP.NET Core SignalR podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="b3d60-842">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="b3d60-843">Každý protokol má možnosti konfigurace serializace.</span><span class="sxs-lookup"><span data-stu-id="b3d60-843">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="b3d60-844">Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , kterou lze přidat po [Přidání SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) do `Startup.ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="b3d60-844">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="b3d60-845">`AddJsonProtocol`Metoda přebírá delegáta, který přijímá `options` objekt.</span><span class="sxs-lookup"><span data-stu-id="b3d60-845">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="b3d60-846">Vlastnost [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) tohoto objektu je `JsonSerializerSettings` objekt JSON.NET, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="b3d60-846">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="b3d60-847">Další informace najdete v [dokumentaci k JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="b3d60-847">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="b3d60-848">Chcete-li například nakonfigurovat serializátor pro použití názvů vlastností "PascalCase" namísto výchozích názvů "camelCase", použijte následující kód v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b3d60-848">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="b3d60-849">V klientovi .NET `AddJsonProtocol` existuje stejná rozšiřující metoda v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="b3d60-849">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="b3d60-850">`Microsoft.Extensions.DependencyInjection`Obor názvů musí být importován, aby bylo možné přeložit metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="b3d60-850">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="b3d60-851">V tuto chvíli není možné konfigurovat serializaci JSON v klientovi jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b3d60-851">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="b3d60-852">Možnosti serializace MessagePack</span><span class="sxs-lookup"><span data-stu-id="b3d60-852">MessagePack serialization options</span></span>

<span data-ttu-id="b3d60-853">Serializaci MessagePack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="b3d60-853">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="b3d60-854">Další podrobnosti najdete [v SignalR tématu MessagePack](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="b3d60-854">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="b3d60-855">V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi v jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b3d60-855">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="b3d60-856">Konfigurovat možnosti serveru</span><span class="sxs-lookup"><span data-stu-id="b3d60-856">Configure server options</span></span>

<span data-ttu-id="b3d60-857">Následující tabulka popisuje možnosti konfigurace SignalR Center:</span><span class="sxs-lookup"><span data-stu-id="b3d60-857">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="b3d60-858">Možnost</span><span class="sxs-lookup"><span data-stu-id="b3d60-858">Option</span></span> | <span data-ttu-id="b3d60-859">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-859">Default Value</span></span> | <span data-ttu-id="b3d60-860">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-860">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="b3d60-861">30 sekund</span><span class="sxs-lookup"><span data-stu-id="b3d60-861">30 seconds</span></span> | <span data-ttu-id="b3d60-862">Server bude předpokládat, že je klient odpojený, pokud v tomto intervalu neobdržel zprávu (včetně Keep-Alive).</span><span class="sxs-lookup"><span data-stu-id="b3d60-862">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="b3d60-863">Může trvat delší dobu, než je tento časový limit, aby bylo možné klienta ve skutečnosti označit jako odpojený, protože to je implementováno.</span><span class="sxs-lookup"><span data-stu-id="b3d60-863">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="b3d60-864">Doporučená hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="b3d60-864">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="b3d60-865">15 sekund</span><span class="sxs-lookup"><span data-stu-id="b3d60-865">15 seconds</span></span> | <span data-ttu-id="b3d60-866">Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře.</span><span class="sxs-lookup"><span data-stu-id="b3d60-866">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="b3d60-867">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="b3d60-867">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b3d60-868">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b3d60-868">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="b3d60-869">15 sekund</span><span class="sxs-lookup"><span data-stu-id="b3d60-869">15 seconds</span></span> | <span data-ttu-id="b3d60-870">Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="b3d60-870">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="b3d60-871">Při změně `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení změňte nastavení v klientovi.</span><span class="sxs-lookup"><span data-stu-id="b3d60-871">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="b3d60-872">Doporučená `ServerTimeout` / `serverTimeoutInMilliseconds` hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="b3d60-872">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="b3d60-873">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="b3d60-873">All installed protocols</span></span> | <span data-ttu-id="b3d60-874">Protokoly podporované tímto rozbočovačem</span><span class="sxs-lookup"><span data-stu-id="b3d60-874">Protocols supported by this hub.</span></span> <span data-ttu-id="b3d60-875">Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly.</span><span class="sxs-lookup"><span data-stu-id="b3d60-875">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="b3d60-876">Pokud se `true` do klientů vrátí podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="b3d60-876">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="b3d60-877">Výchozí hodnota je `false` , protože tyto zprávy o výjimce můžou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="b3d60-877">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="b3d60-878">Možnosti lze nakonfigurovat pro všechna centra tím, že poskytnete možnosti delegáta pro `AddSignalR` volání v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b3d60-878">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="b3d60-879">Možnosti pro jeden rozbočovač přepíší globální možnosti poskytované v `AddSignalR` a můžou být nakonfigurované pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="b3d60-879">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="b3d60-880">Rozšířené možnosti konfigurace protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="b3d60-880">Advanced HTTP configuration options</span></span>

<span data-ttu-id="b3d60-881">Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="b3d60-881">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="b3d60-882">Tyto možnosti jsou nakonfigurovány předáním delegáta [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b3d60-882">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="b3d60-883">Následující tabulka popisuje možnosti konfigurace SignalR rozšířených možností protokolu HTTP v ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="b3d60-883">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="b3d60-884">Možnost</span><span class="sxs-lookup"><span data-stu-id="b3d60-884">Option</span></span> | <span data-ttu-id="b3d60-885">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-885">Default Value</span></span> | <span data-ttu-id="b3d60-886">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-886">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="b3d60-887">32 KB</span><span class="sxs-lookup"><span data-stu-id="b3d60-887">32 KB</span></span> | <span data-ttu-id="b3d60-888">Maximální počet bajtů přijatých od klienta, které jsou vyrovnávací paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="b3d60-888">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="b3d60-889">Zvýšením této hodnoty umožníte serveru přijímat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="b3d60-889">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="b3d60-890">Data se automaticky shromažďují z `Authorize` atributů použitých pro třídu centra.</span><span class="sxs-lookup"><span data-stu-id="b3d60-890">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="b3d60-891">Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru</span><span class="sxs-lookup"><span data-stu-id="b3d60-891">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="b3d60-892">32 KB</span><span class="sxs-lookup"><span data-stu-id="b3d60-892">32 KB</span></span> | <span data-ttu-id="b3d60-893">Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="b3d60-893">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="b3d60-894">Zvýšením této hodnoty umožníte serveru odesílat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="b3d60-894">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="b3d60-895">Všechny přenosy jsou povolené.</span><span class="sxs-lookup"><span data-stu-id="b3d60-895">All Transports are enabled.</span></span> | <span data-ttu-id="b3d60-896">Bitové příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenos, který může klient použít pro připojení.</span><span class="sxs-lookup"><span data-stu-id="b3d60-896">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="b3d60-897">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="b3d60-897">See below.</span></span> | <span data-ttu-id="b3d60-898">Další možnosti specifické pro přenos dlouhého cyklického dotazování.</span><span class="sxs-lookup"><span data-stu-id="b3d60-898">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="b3d60-899">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="b3d60-899">See below.</span></span> | <span data-ttu-id="b3d60-900">Další možnosti specifické pro přenos pomocí protokolu WebSockets</span><span class="sxs-lookup"><span data-stu-id="b3d60-900">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="b3d60-901">Přenos dlouhého cyklického dotazování má další možnosti, které je možné konfigurovat pomocí `LongPolling` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="b3d60-901">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="b3d60-902">Možnost</span><span class="sxs-lookup"><span data-stu-id="b3d60-902">Option</span></span> | <span data-ttu-id="b3d60-903">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-903">Default Value</span></span> | <span data-ttu-id="b3d60-904">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-904">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="b3d60-905">90 sekund</span><span class="sxs-lookup"><span data-stu-id="b3d60-905">90 seconds</span></span> | <span data-ttu-id="b3d60-906">Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jedné žádosti o cyklické dotazování.</span><span class="sxs-lookup"><span data-stu-id="b3d60-906">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="b3d60-907">Snížení této hodnoty způsobí, že klient bude vydávat nové požadavky na dotaz častěji.</span><span class="sxs-lookup"><span data-stu-id="b3d60-907">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="b3d60-908">Přenos protokolu WebSocket má další možnosti, které je možné konfigurovat pomocí `WebSockets` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="b3d60-908">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="b3d60-909">Možnost</span><span class="sxs-lookup"><span data-stu-id="b3d60-909">Option</span></span> | <span data-ttu-id="b3d60-910">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-910">Default Value</span></span> | <span data-ttu-id="b3d60-911">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-911">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="b3d60-912">5 sekund</span><span class="sxs-lookup"><span data-stu-id="b3d60-912">5 seconds</span></span> | <span data-ttu-id="b3d60-913">Pokud se po ukončení serveru aplikace v tomto časovém intervalu nepovede zavřít, připojení se ukončí.</span><span class="sxs-lookup"><span data-stu-id="b3d60-913">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="b3d60-914">Delegát, který lze použít k nastavení `Sec-WebSocket-Protocol` záhlaví na vlastní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="b3d60-914">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="b3d60-915">Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že se vrátí požadovaná hodnota.</span><span class="sxs-lookup"><span data-stu-id="b3d60-915">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="b3d60-916">Konfigurace možností klienta</span><span class="sxs-lookup"><span data-stu-id="b3d60-916">Configure client options</span></span>

<span data-ttu-id="b3d60-917">Možnosti klienta lze nakonfigurovat pro `HubConnectionBuilder` typ (k dispozici v klientech rozhraní .NET a v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b3d60-917">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="b3d60-918">Je také k dispozici v klientovi Java, ale `HttpHubConnectionBuilder` podtřídou je to, co obsahuje možnosti konfigurace tvůrce a také na `HubConnection` sebe sama.</span><span class="sxs-lookup"><span data-stu-id="b3d60-918">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="b3d60-919">Konfigurovat protokolování</span><span class="sxs-lookup"><span data-stu-id="b3d60-919">Configure logging</span></span>

<span data-ttu-id="b3d60-920">Protokolování je konfigurováno v klientovi .NET pomocí `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="b3d60-920">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="b3d60-921">Zprostředkovatelé protokolování a filtry je možné zaregistrovat stejným způsobem jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="b3d60-921">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="b3d60-922">Další informace najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="b3d60-922">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="b3d60-923">Aby bylo možné registrovat poskytovatele protokolování, je nutné nainstalovat potřebné balíčky.</span><span class="sxs-lookup"><span data-stu-id="b3d60-923">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="b3d60-924">Úplný seznam najdete v části [předdefinovaná zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="b3d60-924">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="b3d60-925">Chcete-li například povolit protokolování konzoly, nainstalujte `Microsoft.Extensions.Logging.Console` balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="b3d60-925">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="b3d60-926">Zavolejte `AddConsole` metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="b3d60-926">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="b3d60-927">V klientovi jazyka JavaScript existuje podobná `configureLogging` metoda.</span><span class="sxs-lookup"><span data-stu-id="b3d60-927">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="b3d60-928">Zadejte `LogLevel` hodnotu, která určuje minimální úroveň zpráv protokolu, které mají být vyprodukovány.</span><span class="sxs-lookup"><span data-stu-id="b3d60-928">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="b3d60-929">Protokoly se zapisují do okna konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="b3d60-929">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="b3d60-930">Chcete-li protokolování zcela zakázat, zadejte `signalR.LogLevel.None` v `configureLogging` metodě.</span><span class="sxs-lookup"><span data-stu-id="b3d60-930">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="b3d60-931">Další informace o protokolování naleznete v dokumentaci k [ SignalR diagnostice](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="b3d60-931">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="b3d60-932">SignalRKlient Java používá k protokolování knihovnu [SLF4J](https://www.slf4j.org/) .</span><span class="sxs-lookup"><span data-stu-id="b3d60-932">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="b3d60-933">Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí.</span><span class="sxs-lookup"><span data-stu-id="b3d60-933">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="b3d60-934">Následující fragment kódu ukazuje, jak používat `java.util.logging` s SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="b3d60-934">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="b3d60-935">Pokud ve svých závislostech nenakonfigurujete protokolování, SLF4J načte výchozí protokolovací nástroj No-operation s následující zprávou upozornění:</span><span class="sxs-lookup"><span data-stu-id="b3d60-935">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="b3d60-936">Tuto možnost lze bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="b3d60-936">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="b3d60-937">Konfigurace povolených přenosů</span><span class="sxs-lookup"><span data-stu-id="b3d60-937">Configure allowed transports</span></span>

<span data-ttu-id="b3d60-938">Přenosy používané nástrojem SignalR lze konfigurovat ve `WithUrl` volání ( `withUrl` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b3d60-938">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="b3d60-939">Bitové nebo hodnoty `HttpTransportType` lze použít k omezení klienta na používání pouze zadaných přenosů.</span><span class="sxs-lookup"><span data-stu-id="b3d60-939">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="b3d60-940">Ve výchozím nastavení jsou povolené všechny přenosy.</span><span class="sxs-lookup"><span data-stu-id="b3d60-940">All transports are enabled by default.</span></span>

<span data-ttu-id="b3d60-941">Pokud třeba chcete zakázat přenos událostí odeslaných serverem, ale povolit WebSockets a dlouhá připojení s dotazem:</span><span class="sxs-lookup"><span data-stu-id="b3d60-941">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="b3d60-942">V klientu jazyka JavaScript jsou přenosy konfigurovány nastavením `transport` pole v objektu Options, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="b3d60-942">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="b3d60-943">V této verzi je jediným dostupným přenosem klientský WebSocket v jazyce Java.</span><span class="sxs-lookup"><span data-stu-id="b3d60-943">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="b3d60-944">Konfigurace ověřování nosiče</span><span class="sxs-lookup"><span data-stu-id="b3d60-944">Configure bearer authentication</span></span>

<span data-ttu-id="b3d60-945">K poskytnutí ověřovacích dat spolu s SignalR požadavky použijte `AccessTokenProvider` možnost ( `accessTokenFactory` v jazyce JavaScript) k určení funkce, která vrací požadovaný přístupový token.</span><span class="sxs-lookup"><span data-stu-id="b3d60-945">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="b3d60-946">V klientovi .NET se tento přístupový token předává jako token ověřování HTTP "Bearer" (s použitím `Authorization` hlavičky s typem `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="b3d60-946">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="b3d60-947">V klientu jazyka JavaScript se přístupový token používá jako nosný token, **s výjimkou** případů, kdy rozhraní API prohlížeče omezuje možnost použít hlavičky (konkrétně v požadavcích na události odeslané serverem a objekty WebSockets).</span><span class="sxs-lookup"><span data-stu-id="b3d60-947">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="b3d60-948">V těchto případech je přístupový token k dispozici jako hodnota řetězce dotazu `access_token` .</span><span class="sxs-lookup"><span data-stu-id="b3d60-948">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="b3d60-949">V klientu .NET `AccessTokenProvider` lze možnost zadat pomocí delegáta možnosti v `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="b3d60-949">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="b3d60-950">V klientovi JavaScriptu je přístupový token nakonfigurovaný nastavením `accessTokenFactory` pole v objektu Options v `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="b3d60-950">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="b3d60-951">V SignalR klientovi Java můžete nakonfigurovat nosný token, který se bude používat pro ověřování, a to poskytnutím továrny přístupového tokenu [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="b3d60-951">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="b3d60-952">Pomocí [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) můžete zadat [RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="b3d60-952">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="b3d60-953">Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.</span><span class="sxs-lookup"><span data-stu-id="b3d60-953">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="b3d60-954">Konfigurace možností timeout a Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="b3d60-954">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="b3d60-955">Další možnosti konfigurace časového limitu a chování funkce Keep-Alive jsou k dispozici na `HubConnection` samotném objektu:</span><span class="sxs-lookup"><span data-stu-id="b3d60-955">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="b3d60-956">.NET</span><span class="sxs-lookup"><span data-stu-id="b3d60-956">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="b3d60-957">Možnost</span><span class="sxs-lookup"><span data-stu-id="b3d60-957">Option</span></span> | <span data-ttu-id="b3d60-958">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-958">Default value</span></span> | <span data-ttu-id="b3d60-959">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-959">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="b3d60-960">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="b3d60-960">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b3d60-961">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="b3d60-961">Timeout for server activity.</span></span> <span data-ttu-id="b3d60-962">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí `Closed` událost ( `onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="b3d60-962">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="b3d60-963">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="b3d60-963">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b3d60-964">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="b3d60-964">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="b3d60-965">15 sekund</span><span class="sxs-lookup"><span data-stu-id="b3d60-965">15 seconds</span></span> | <span data-ttu-id="b3d60-966">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="b3d60-966">Timeout for initial server handshake.</span></span> <span data-ttu-id="b3d60-967">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a spustí `Closed` událost ( `onclose` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b3d60-967">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="b3d60-968">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="b3d60-968">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b3d60-969">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b3d60-969">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="b3d60-970">15 sekund</span><span class="sxs-lookup"><span data-stu-id="b3d60-970">15 seconds</span></span> | <span data-ttu-id="b3d60-971">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="b3d60-971">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b3d60-972">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="b3d60-972">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b3d60-973">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="b3d60-973">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="b3d60-974">V klientovi .NET jsou hodnoty časového limitu zadány jako `TimeSpan` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="b3d60-974">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="b3d60-975">JavaScript</span><span class="sxs-lookup"><span data-stu-id="b3d60-975">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="b3d60-976">Možnost</span><span class="sxs-lookup"><span data-stu-id="b3d60-976">Option</span></span> | <span data-ttu-id="b3d60-977">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-977">Default value</span></span> | <span data-ttu-id="b3d60-978">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-978">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="b3d60-979">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="b3d60-979">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b3d60-980">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="b3d60-980">Timeout for server activity.</span></span> <span data-ttu-id="b3d60-981">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onclose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="b3d60-981">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="b3d60-982">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="b3d60-982">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b3d60-983">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="b3d60-983">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="b3d60-984">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="b3d60-984">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="b3d60-985">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="b3d60-985">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b3d60-986">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="b3d60-986">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b3d60-987">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="b3d60-987">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="b3d60-988">Java</span><span class="sxs-lookup"><span data-stu-id="b3d60-988">Java</span></span>](#tab/java)

| <span data-ttu-id="b3d60-989">Možnost</span><span class="sxs-lookup"><span data-stu-id="b3d60-989">Option</span></span> | <span data-ttu-id="b3d60-990">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-990">Default value</span></span> | <span data-ttu-id="b3d60-991">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-991">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="b3d60-992">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="b3d60-992">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="b3d60-993">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="b3d60-993">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b3d60-994">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="b3d60-994">Timeout for server activity.</span></span> <span data-ttu-id="b3d60-995">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onClose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="b3d60-995">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="b3d60-996">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="b3d60-996">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b3d60-997">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="b3d60-997">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="b3d60-998">15 sekund</span><span class="sxs-lookup"><span data-stu-id="b3d60-998">15 seconds</span></span> | <span data-ttu-id="b3d60-999">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="b3d60-999">Timeout for initial server handshake.</span></span> <span data-ttu-id="b3d60-1000">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje `onClose` událost.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1000">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="b3d60-1001">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1001">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b3d60-1002">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b3d60-1002">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="b3d60-1003">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="b3d60-1003">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="b3d60-1004">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="b3d60-1004">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="b3d60-1005">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1005">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="b3d60-1006">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1006">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="b3d60-1007">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1007">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="b3d60-1008">Konfigurace dalších možností</span><span class="sxs-lookup"><span data-stu-id="b3d60-1008">Configure additional options</span></span>

<span data-ttu-id="b3d60-1009">Další možnosti lze konfigurovat v `WithUrl` `withUrl` metodě (v jazyce JavaScript) na `HubConnectionBuilder` různých konfiguračních rozhraních API v `HttpHubConnectionBuilder` klientovi Java:</span><span class="sxs-lookup"><span data-stu-id="b3d60-1009">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="b3d60-1010">.NET</span><span class="sxs-lookup"><span data-stu-id="b3d60-1010">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="b3d60-1011">Možnost .NET</span><span class="sxs-lookup"><span data-stu-id="b3d60-1011">.NET Option</span></span> |  <span data-ttu-id="b3d60-1012">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-1012">Default value</span></span> | <span data-ttu-id="b3d60-1013">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-1013">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="b3d60-1014">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1014">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="b3d60-1015">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1015">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b3d60-1016">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1016">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b3d60-1017">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="b3d60-1017">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="b3d60-1018">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="b3d60-1018">Empty</span></span> | <span data-ttu-id="b3d60-1019">Kolekce certifikátů TLS pro odeslání na požadavky na ověření.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1019">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="b3d60-1020">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="b3d60-1020">Empty</span></span> | <span data-ttu-id="b3d60-1021">Kolekce HTTP cookie s pro odeslání s každou žádostí http</span><span class="sxs-lookup"><span data-stu-id="b3d60-1021">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="b3d60-1022">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="b3d60-1022">Empty</span></span> | <span data-ttu-id="b3d60-1023">Přihlašovací údaje, které se mají poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="b3d60-1023">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="b3d60-1024">5 sekund</span><span class="sxs-lookup"><span data-stu-id="b3d60-1024">5 seconds</span></span> | <span data-ttu-id="b3d60-1025">Jenom objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1025">WebSockets only.</span></span> <span data-ttu-id="b3d60-1026">Maximální doba, po jejímž uplynutí bude klient čekat na ukončení žádosti o uzavření.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1026">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="b3d60-1027">Pokud server nepotvrdí zavření v této době, klient se odpojí.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1027">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="b3d60-1028">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="b3d60-1028">Empty</span></span> | <span data-ttu-id="b3d60-1029">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="b3d60-1029">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="b3d60-1030">Delegát, který lze použít ke konfiguraci nebo nahrazení `HttpMessageHandler` používaného k odeslání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1030">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="b3d60-1031">Nepoužívá se pro připojení pomocí protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1031">Not used for WebSocket connections.</span></span> <span data-ttu-id="b3d60-1032">Tento delegát musí vracet hodnotu, která není null, a obdrží výchozí hodnotu jako parametr.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1032">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="b3d60-1033">Buď změňte nastavení u této výchozí hodnoty a vraťte je, nebo vraťte novou `HttpMessageHandler` instanci.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1033">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="b3d60-1034">**Při nahrazování obslužné rutiny Nezapomeňte zkopírovat nastavení, která chcete zachovat od poskytnuté obslužné rutiny, jinak konfigurované možnosti (například Cookie s a záhlaví) nebudou použity pro novou obslužnou rutinu.**</span><span class="sxs-lookup"><span data-stu-id="b3d60-1034">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="b3d60-1035">Proxy server HTTP, který se má použít při odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1035">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="b3d60-1036">Nastavte tuto logickou hodnotu pro odeslání výchozích přihlašovacích údajů pro požadavky HTTP a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1036">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="b3d60-1037">To umožňuje použití ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1037">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="b3d60-1038">Delegát, který lze použít ke konfiguraci dalších možností protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1038">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="b3d60-1039">Přijímá instanci [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , která se dá použít ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1039">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="b3d60-1040">JavaScript</span><span class="sxs-lookup"><span data-stu-id="b3d60-1040">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="b3d60-1041">Možnost JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="b3d60-1041">JavaScript Option</span></span> | <span data-ttu-id="b3d60-1042">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-1042">Default Value</span></span> | <span data-ttu-id="b3d60-1043">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-1043">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="b3d60-1044">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1044">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="b3d60-1045">Nastavte na `true` protokolování bajtů/znaků zpráv odesílaných a přijímaných klientem.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1045">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="b3d60-1046">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1046">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b3d60-1047">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1047">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b3d60-1048">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="b3d60-1048">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="b3d60-1049">Java</span><span class="sxs-lookup"><span data-stu-id="b3d60-1049">Java</span></span>](#tab/java)

| <span data-ttu-id="b3d60-1050">Možnost jazyka Java</span><span class="sxs-lookup"><span data-stu-id="b3d60-1050">Java Option</span></span> | <span data-ttu-id="b3d60-1051">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-1051">Default Value</span></span> | <span data-ttu-id="b3d60-1052">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-1052">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="b3d60-1053">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1053">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="b3d60-1054">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1054">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b3d60-1055">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1055">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b3d60-1056">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="b3d60-1056">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="b3d60-1057">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="b3d60-1057">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="b3d60-1058">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="b3d60-1058">Empty</span></span> | <span data-ttu-id="b3d60-1059">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="b3d60-1059">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="b3d60-1060">V klientu .NET můžete tyto možnosti upravit pomocí delegáta možností, který je k dispozici pro `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="b3d60-1060">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="b3d60-1061">V klientovi JavaScriptu lze tyto možnosti poskytnout v objektu jazyka JavaScript, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="b3d60-1061">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="b3d60-1062">V klientovi Java lze tyto možnosti nakonfigurovat s metodami `HttpHubConnectionBuilder` vrácenými z `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="b3d60-1062">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="b3d60-1063">Další zdroje informací</span><span class="sxs-lookup"><span data-stu-id="b3d60-1063">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="b3d60-1064">Možnosti serializace JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="b3d60-1064">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="b3d60-1065">ASP.NET Core SignalR podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="b3d60-1065">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="b3d60-1066">Každý protokol má možnosti konfigurace serializace.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1066">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="b3d60-1067">Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , kterou lze přidat po [Přidání SignalR ](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) do `Startup.ConfigureServices` metody.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1067">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="b3d60-1068">`AddJsonProtocol`Metoda přebírá delegáta, který přijímá `options` objekt.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1068">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="b3d60-1069">Vlastnost [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) tohoto objektu je `JsonSerializerSettings` objekt JSON.NET, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1069">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="b3d60-1070">Další informace najdete v [dokumentaci k JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="b3d60-1070">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="b3d60-1071">Chcete-li například nakonfigurovat serializátor pro použití názvů vlastností "PascalCase" namísto výchozích názvů "camelCase", použijte následující kód v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="b3d60-1071">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="b3d60-1072">V klientovi .NET `AddJsonProtocol` existuje stejná rozšiřující metoda v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="b3d60-1072">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="b3d60-1073">`Microsoft.Extensions.DependencyInjection`Obor názvů musí být importován, aby bylo možné přeložit metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="b3d60-1073">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="b3d60-1074">V tuto chvíli není možné konfigurovat serializaci JSON v klientovi jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1074">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="b3d60-1075">Možnosti serializace MessagePack</span><span class="sxs-lookup"><span data-stu-id="b3d60-1075">MessagePack serialization options</span></span>

<span data-ttu-id="b3d60-1076">Serializaci MessagePack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="b3d60-1076">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="b3d60-1077">Další podrobnosti najdete [v SignalR tématu MessagePack](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="b3d60-1077">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="b3d60-1078">V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi v jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1078">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="b3d60-1079">Konfigurovat možnosti serveru</span><span class="sxs-lookup"><span data-stu-id="b3d60-1079">Configure server options</span></span>

<span data-ttu-id="b3d60-1080">Následující tabulka popisuje možnosti konfigurace SignalR Center:</span><span class="sxs-lookup"><span data-stu-id="b3d60-1080">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="b3d60-1081">Možnost</span><span class="sxs-lookup"><span data-stu-id="b3d60-1081">Option</span></span> | <span data-ttu-id="b3d60-1082">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-1082">Default Value</span></span> | <span data-ttu-id="b3d60-1083">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-1083">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="b3d60-1084">15 sekund</span><span class="sxs-lookup"><span data-stu-id="b3d60-1084">15 seconds</span></span> | <span data-ttu-id="b3d60-1085">Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1085">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="b3d60-1086">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1086">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b3d60-1087">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b3d60-1087">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="b3d60-1088">15 sekund</span><span class="sxs-lookup"><span data-stu-id="b3d60-1088">15 seconds</span></span> | <span data-ttu-id="b3d60-1089">Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1089">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="b3d60-1090">Při změně `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení změňte nastavení v klientovi.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1090">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="b3d60-1091">Doporučená `ServerTimeout` / `serverTimeoutInMilliseconds` hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1091">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="b3d60-1092">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="b3d60-1092">All installed protocols</span></span> | <span data-ttu-id="b3d60-1093">Protokoly podporované tímto rozbočovačem</span><span class="sxs-lookup"><span data-stu-id="b3d60-1093">Protocols supported by this hub.</span></span> <span data-ttu-id="b3d60-1094">Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1094">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="b3d60-1095">Pokud se `true` do klientů vrátí podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1095">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="b3d60-1096">Výchozí hodnota je `false` , protože tyto zprávy o výjimce můžou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1096">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="b3d60-1097">Možnosti lze nakonfigurovat pro všechna centra tím, že poskytnete možnosti delegáta pro `AddSignalR` volání v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="b3d60-1097">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="b3d60-1098">Možnosti pro jeden rozbočovač přepíší globální možnosti poskytované v `AddSignalR` a můžou být nakonfigurované pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="b3d60-1098">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="b3d60-1099">Rozšířené možnosti konfigurace protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="b3d60-1099">Advanced HTTP configuration options</span></span>

<span data-ttu-id="b3d60-1100">Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1100">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="b3d60-1101">Tyto možnosti jsou nakonfigurovány předáním delegáta [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="b3d60-1101">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="b3d60-1102">Následující tabulka popisuje možnosti konfigurace SignalR rozšířených možností protokolu HTTP v ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="b3d60-1102">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="b3d60-1103">Možnost</span><span class="sxs-lookup"><span data-stu-id="b3d60-1103">Option</span></span> | <span data-ttu-id="b3d60-1104">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-1104">Default Value</span></span> | <span data-ttu-id="b3d60-1105">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-1105">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="b3d60-1106">32 KB</span><span class="sxs-lookup"><span data-stu-id="b3d60-1106">32 KB</span></span> | <span data-ttu-id="b3d60-1107">Maximální počet bajtů přijatých od klienta, které jsou vyrovnávací paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1107">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="b3d60-1108">Zvýšením této hodnoty umožníte serveru přijímat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1108">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="b3d60-1109">Data se automaticky shromažďují z `Authorize` atributů použitých pro třídu centra.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1109">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="b3d60-1110">Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru</span><span class="sxs-lookup"><span data-stu-id="b3d60-1110">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="b3d60-1111">32 KB</span><span class="sxs-lookup"><span data-stu-id="b3d60-1111">32 KB</span></span> | <span data-ttu-id="b3d60-1112">Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1112">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="b3d60-1113">Zvýšením této hodnoty umožníte serveru odesílat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1113">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="b3d60-1114">Všechny přenosy jsou povolené.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1114">All Transports are enabled.</span></span> | <span data-ttu-id="b3d60-1115">Bitové příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenos, který může klient použít pro připojení.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1115">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="b3d60-1116">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1116">See below.</span></span> | <span data-ttu-id="b3d60-1117">Další možnosti specifické pro přenos dlouhého cyklického dotazování.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1117">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="b3d60-1118">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1118">See below.</span></span> | <span data-ttu-id="b3d60-1119">Další možnosti specifické pro přenos pomocí protokolu WebSockets</span><span class="sxs-lookup"><span data-stu-id="b3d60-1119">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="b3d60-1120">Přenos dlouhého cyklického dotazování má další možnosti, které je možné konfigurovat pomocí `LongPolling` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="b3d60-1120">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="b3d60-1121">Možnost</span><span class="sxs-lookup"><span data-stu-id="b3d60-1121">Option</span></span> | <span data-ttu-id="b3d60-1122">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-1122">Default Value</span></span> | <span data-ttu-id="b3d60-1123">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-1123">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="b3d60-1124">90 sekund</span><span class="sxs-lookup"><span data-stu-id="b3d60-1124">90 seconds</span></span> | <span data-ttu-id="b3d60-1125">Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jedné žádosti o cyklické dotazování.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1125">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="b3d60-1126">Snížení této hodnoty způsobí, že klient bude vydávat nové požadavky na dotaz častěji.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1126">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="b3d60-1127">Přenos protokolu WebSocket má další možnosti, které je možné konfigurovat pomocí `WebSockets` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="b3d60-1127">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="b3d60-1128">Možnost</span><span class="sxs-lookup"><span data-stu-id="b3d60-1128">Option</span></span> | <span data-ttu-id="b3d60-1129">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-1129">Default Value</span></span> | <span data-ttu-id="b3d60-1130">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-1130">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="b3d60-1131">5 sekund</span><span class="sxs-lookup"><span data-stu-id="b3d60-1131">5 seconds</span></span> | <span data-ttu-id="b3d60-1132">Pokud se po ukončení serveru aplikace v tomto časovém intervalu nepovede zavřít, připojení se ukončí.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1132">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="b3d60-1133">Delegát, který lze použít k nastavení `Sec-WebSocket-Protocol` záhlaví na vlastní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1133">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="b3d60-1134">Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že se vrátí požadovaná hodnota.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1134">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="b3d60-1135">Konfigurace možností klienta</span><span class="sxs-lookup"><span data-stu-id="b3d60-1135">Configure client options</span></span>

<span data-ttu-id="b3d60-1136">Možnosti klienta lze nakonfigurovat pro `HubConnectionBuilder` typ (k dispozici v klientech rozhraní .NET a v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b3d60-1136">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="b3d60-1137">Je také k dispozici v klientovi Java, ale `HttpHubConnectionBuilder` podtřídou je to, co obsahuje možnosti konfigurace tvůrce a také na `HubConnection` sebe sama.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1137">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="b3d60-1138">Konfigurovat protokolování</span><span class="sxs-lookup"><span data-stu-id="b3d60-1138">Configure logging</span></span>

<span data-ttu-id="b3d60-1139">Protokolování je konfigurováno v klientovi .NET pomocí `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1139">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="b3d60-1140">Zprostředkovatelé protokolování a filtry je možné zaregistrovat stejným způsobem jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1140">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="b3d60-1141">Další informace najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="b3d60-1141">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="b3d60-1142">Aby bylo možné registrovat poskytovatele protokolování, je nutné nainstalovat potřebné balíčky.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1142">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="b3d60-1143">Úplný seznam najdete v části [předdefinovaná zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1143">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="b3d60-1144">Chcete-li například povolit protokolování konzoly, nainstalujte `Microsoft.Extensions.Logging.Console` balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1144">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="b3d60-1145">Zavolejte `AddConsole` metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="b3d60-1145">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="b3d60-1146">V klientovi jazyka JavaScript existuje podobná `configureLogging` metoda.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1146">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="b3d60-1147">Zadejte `LogLevel` hodnotu, která určuje minimální úroveň zpráv protokolu, které mají být vyprodukovány.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1147">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="b3d60-1148">Protokoly se zapisují do okna konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1148">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="b3d60-1149">Chcete-li protokolování zcela zakázat, zadejte `signalR.LogLevel.None` v `configureLogging` metodě.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1149">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="b3d60-1150">Další informace o protokolování naleznete v dokumentaci k [ SignalR diagnostice](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="b3d60-1150">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="b3d60-1151">SignalRKlient Java používá k protokolování knihovnu [SLF4J](https://www.slf4j.org/) .</span><span class="sxs-lookup"><span data-stu-id="b3d60-1151">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="b3d60-1152">Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1152">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="b3d60-1153">Následující fragment kódu ukazuje, jak používat `java.util.logging` s SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1153">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="b3d60-1154">Pokud ve svých závislostech nenakonfigurujete protokolování, SLF4J načte výchozí protokolovací nástroj No-operation s následující zprávou upozornění:</span><span class="sxs-lookup"><span data-stu-id="b3d60-1154">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="b3d60-1155">Tuto možnost lze bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1155">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="b3d60-1156">Konfigurace povolených přenosů</span><span class="sxs-lookup"><span data-stu-id="b3d60-1156">Configure allowed transports</span></span>

<span data-ttu-id="b3d60-1157">Přenosy používané nástrojem SignalR lze konfigurovat ve `WithUrl` volání ( `withUrl` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b3d60-1157">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="b3d60-1158">Bitové nebo hodnoty `HttpTransportType` lze použít k omezení klienta na používání pouze zadaných přenosů.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1158">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="b3d60-1159">Ve výchozím nastavení jsou povolené všechny přenosy.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1159">All transports are enabled by default.</span></span>

<span data-ttu-id="b3d60-1160">Pokud třeba chcete zakázat přenos událostí odeslaných serverem, ale povolit WebSockets a dlouhá připojení s dotazem:</span><span class="sxs-lookup"><span data-stu-id="b3d60-1160">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="b3d60-1161">V klientu jazyka JavaScript jsou přenosy konfigurovány nastavením `transport` pole v objektu Options, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="b3d60-1161">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="b3d60-1162">Konfigurace ověřování nosiče</span><span class="sxs-lookup"><span data-stu-id="b3d60-1162">Configure bearer authentication</span></span>

<span data-ttu-id="b3d60-1163">K poskytnutí ověřovacích dat spolu s SignalR požadavky použijte `AccessTokenProvider` možnost ( `accessTokenFactory` v jazyce JavaScript) k určení funkce, která vrací požadovaný přístupový token.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1163">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="b3d60-1164">V klientovi .NET se tento přístupový token předává jako token ověřování HTTP "Bearer" (s použitím `Authorization` hlavičky s typem `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="b3d60-1164">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="b3d60-1165">V klientu jazyka JavaScript se přístupový token používá jako nosný token, **s výjimkou** případů, kdy rozhraní API prohlížeče omezuje možnost použít hlavičky (konkrétně v požadavcích na události odeslané serverem a objekty WebSockets).</span><span class="sxs-lookup"><span data-stu-id="b3d60-1165">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="b3d60-1166">V těchto případech je přístupový token k dispozici jako hodnota řetězce dotazu `access_token` .</span><span class="sxs-lookup"><span data-stu-id="b3d60-1166">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="b3d60-1167">V klientu .NET `AccessTokenProvider` lze možnost zadat pomocí delegáta možnosti v `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="b3d60-1167">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="b3d60-1168">V klientovi JavaScriptu je přístupový token nakonfigurovaný nastavením `accessTokenFactory` pole v objektu Options v `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="b3d60-1168">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="b3d60-1169">V SignalR klientovi Java můžete nakonfigurovat nosný token, který se bude používat pro ověřování, a to poskytnutím továrny přístupového tokenu [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="b3d60-1169">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="b3d60-1170">Pomocí [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) můžete zadat [RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="b3d60-1170">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="b3d60-1171">Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1171">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="b3d60-1172">Konfigurace možností timeout a Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="b3d60-1172">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="b3d60-1173">Další možnosti konfigurace časového limitu a chování funkce Keep-Alive jsou k dispozici na `HubConnection` samotném objektu:</span><span class="sxs-lookup"><span data-stu-id="b3d60-1173">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="b3d60-1174">.NET</span><span class="sxs-lookup"><span data-stu-id="b3d60-1174">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="b3d60-1175">Možnost</span><span class="sxs-lookup"><span data-stu-id="b3d60-1175">Option</span></span> | <span data-ttu-id="b3d60-1176">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-1176">Default value</span></span> | <span data-ttu-id="b3d60-1177">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-1177">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="b3d60-1178">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="b3d60-1178">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b3d60-1179">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1179">Timeout for server activity.</span></span> <span data-ttu-id="b3d60-1180">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí `Closed` událost ( `onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="b3d60-1180">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="b3d60-1181">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1181">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b3d60-1182">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1182">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="b3d60-1183">15 sekund</span><span class="sxs-lookup"><span data-stu-id="b3d60-1183">15 seconds</span></span> | <span data-ttu-id="b3d60-1184">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1184">Timeout for initial server handshake.</span></span> <span data-ttu-id="b3d60-1185">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a spustí `Closed` událost ( `onclose` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="b3d60-1185">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="b3d60-1186">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1186">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b3d60-1187">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b3d60-1187">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="b3d60-1188">V klientovi .NET jsou hodnoty časového limitu zadány jako `TimeSpan` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1188">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="b3d60-1189">JavaScript</span><span class="sxs-lookup"><span data-stu-id="b3d60-1189">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="b3d60-1190">Možnost</span><span class="sxs-lookup"><span data-stu-id="b3d60-1190">Option</span></span> | <span data-ttu-id="b3d60-1191">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-1191">Default value</span></span> | <span data-ttu-id="b3d60-1192">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-1192">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="b3d60-1193">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="b3d60-1193">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b3d60-1194">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1194">Timeout for server activity.</span></span> <span data-ttu-id="b3d60-1195">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onclose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1195">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="b3d60-1196">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1196">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b3d60-1197">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1197">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="b3d60-1198">Java</span><span class="sxs-lookup"><span data-stu-id="b3d60-1198">Java</span></span>](#tab/java)

| <span data-ttu-id="b3d60-1199">Možnost</span><span class="sxs-lookup"><span data-stu-id="b3d60-1199">Option</span></span> | <span data-ttu-id="b3d60-1200">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-1200">Default value</span></span> | <span data-ttu-id="b3d60-1201">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-1201">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="b3d60-1202">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="b3d60-1202">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="b3d60-1203">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="b3d60-1203">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="b3d60-1204">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1204">Timeout for server activity.</span></span> <span data-ttu-id="b3d60-1205">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onClose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1205">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="b3d60-1206">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1206">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="b3d60-1207">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit k příkazům k zadání času.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1207">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="b3d60-1208">15 sekund</span><span class="sxs-lookup"><span data-stu-id="b3d60-1208">15 seconds</span></span> | <span data-ttu-id="b3d60-1209">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1209">Timeout for initial server handshake.</span></span> <span data-ttu-id="b3d60-1210">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje `onClose` událost.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1210">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="b3d60-1211">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1211">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="b3d60-1212">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="b3d60-1212">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="b3d60-1213">Konfigurace dalších možností</span><span class="sxs-lookup"><span data-stu-id="b3d60-1213">Configure additional options</span></span>

<span data-ttu-id="b3d60-1214">Další možnosti lze konfigurovat v `WithUrl` `withUrl` metodě (v jazyce JavaScript) na `HubConnectionBuilder` různých konfiguračních rozhraních API v `HttpHubConnectionBuilder` klientovi Java:</span><span class="sxs-lookup"><span data-stu-id="b3d60-1214">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="b3d60-1215">.NET</span><span class="sxs-lookup"><span data-stu-id="b3d60-1215">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="b3d60-1216">Možnost .NET</span><span class="sxs-lookup"><span data-stu-id="b3d60-1216">.NET Option</span></span> |  <span data-ttu-id="b3d60-1217">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-1217">Default value</span></span> | <span data-ttu-id="b3d60-1218">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-1218">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="b3d60-1219">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1219">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="b3d60-1220">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1220">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b3d60-1221">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1221">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b3d60-1222">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="b3d60-1222">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="b3d60-1223">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="b3d60-1223">Empty</span></span> | <span data-ttu-id="b3d60-1224">Kolekce certifikátů TLS pro odeslání na požadavky na ověření.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1224">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="b3d60-1225">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="b3d60-1225">Empty</span></span> | <span data-ttu-id="b3d60-1226">Kolekce HTTP cookie s pro odeslání s každou žádostí http</span><span class="sxs-lookup"><span data-stu-id="b3d60-1226">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="b3d60-1227">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="b3d60-1227">Empty</span></span> | <span data-ttu-id="b3d60-1228">Přihlašovací údaje, které se mají poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="b3d60-1228">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="b3d60-1229">5 sekund</span><span class="sxs-lookup"><span data-stu-id="b3d60-1229">5 seconds</span></span> | <span data-ttu-id="b3d60-1230">Jenom objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1230">WebSockets only.</span></span> <span data-ttu-id="b3d60-1231">Maximální doba, po jejímž uplynutí bude klient čekat na ukončení žádosti o uzavření.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1231">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="b3d60-1232">Pokud server nepotvrdí zavření v této době, klient se odpojí.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1232">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="b3d60-1233">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="b3d60-1233">Empty</span></span> | <span data-ttu-id="b3d60-1234">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="b3d60-1234">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="b3d60-1235">Delegát, který lze použít ke konfiguraci nebo nahrazení `HttpMessageHandler` používaného k odeslání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1235">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="b3d60-1236">Nepoužívá se pro připojení pomocí protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1236">Not used for WebSocket connections.</span></span> <span data-ttu-id="b3d60-1237">Tento delegát musí vracet hodnotu, která není null, a obdrží výchozí hodnotu jako parametr.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1237">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="b3d60-1238">Buď změňte nastavení u této výchozí hodnoty a vraťte je, nebo vraťte novou `HttpMessageHandler` instanci.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1238">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="b3d60-1239">**Při nahrazování obslužné rutiny Nezapomeňte zkopírovat nastavení, která chcete zachovat od poskytnuté obslužné rutiny, jinak konfigurované možnosti (například Cookie s a záhlaví) nebudou použity pro novou obslužnou rutinu.**</span><span class="sxs-lookup"><span data-stu-id="b3d60-1239">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="b3d60-1240">Proxy server HTTP, který se má použít při odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1240">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="b3d60-1241">Nastavte tuto logickou hodnotu pro odeslání výchozích přihlašovacích údajů pro požadavky HTTP a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1241">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="b3d60-1242">To umožňuje použití ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1242">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="b3d60-1243">Delegát, který lze použít ke konfiguraci dalších možností protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1243">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="b3d60-1244">Přijímá instanci [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , která se dá použít ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1244">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="b3d60-1245">JavaScript</span><span class="sxs-lookup"><span data-stu-id="b3d60-1245">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="b3d60-1246">Možnost JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="b3d60-1246">JavaScript Option</span></span> | <span data-ttu-id="b3d60-1247">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-1247">Default Value</span></span> | <span data-ttu-id="b3d60-1248">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-1248">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="b3d60-1249">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1249">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `logMessageContent` | `null` | <span data-ttu-id="b3d60-1250">Nastavte na `true` protokolování bajtů/znaků zpráv odesílaných a přijímaných klientem.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1250">Set to `true` to log the bytes/chars of messages sent and received by the client.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="b3d60-1251">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1251">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b3d60-1252">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1252">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b3d60-1253">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="b3d60-1253">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="b3d60-1254">Java</span><span class="sxs-lookup"><span data-stu-id="b3d60-1254">Java</span></span>](#tab/java)

| <span data-ttu-id="b3d60-1255">Možnost jazyka Java</span><span class="sxs-lookup"><span data-stu-id="b3d60-1255">Java Option</span></span> | <span data-ttu-id="b3d60-1256">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="b3d60-1256">Default Value</span></span> | <span data-ttu-id="b3d60-1257">Popis</span><span class="sxs-lookup"><span data-stu-id="b3d60-1257">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="b3d60-1258">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1258">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="b3d60-1259">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1259">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="b3d60-1260">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="b3d60-1260">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="b3d60-1261">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="b3d60-1261">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="b3d60-1262">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="b3d60-1262">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="b3d60-1263">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="b3d60-1263">Empty</span></span> | <span data-ttu-id="b3d60-1264">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="b3d60-1264">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="b3d60-1265">V klientu .NET můžete tyto možnosti upravit pomocí delegáta možností, který je k dispozici pro `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="b3d60-1265">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="b3d60-1266">V klientovi JavaScriptu lze tyto možnosti poskytnout v objektu jazyka JavaScript, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="b3d60-1266">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="b3d60-1267">V klientovi Java lze tyto možnosti nakonfigurovat s metodami `HttpHubConnectionBuilder` vrácenými z `HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="b3d60-1267">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="b3d60-1268">Další materiály</span><span class="sxs-lookup"><span data-stu-id="b3d60-1268">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
