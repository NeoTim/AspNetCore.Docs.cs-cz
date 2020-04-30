---
title: Konfigurace SignalR ASP.NET Core
author: bradygaster
description: Naučte se konfigurovat aplikace SignalR ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/12/2020
no-loc:
- SignalR
uid: signalr/configuration
ms.openlocfilehash: 7e0cd952fd152ff6adb6e0a7c56214d70d3c7b86
ms.sourcegitcommit: f9a5069577e8f7c53f8bcec9e13e117950f4f033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "82558994"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="6e8d5-103">Konfigurace signalizace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6e8d5-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="6e8d5-104">Možnosti serializace JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="6e8d5-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="6e8d5-105">ASP.NET Core Signal podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="6e8d5-106">Každý protokol má možnosti konfigurace serializace.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="6e8d5-107">Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6e8d5-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="6e8d5-108">`AddJsonProtocol`lze přidat po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6e8d5-109">`AddJsonProtocol` Metoda přebírá delegáta, který přijímá `options` objekt.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="6e8d5-110">Vlastnost [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) tohoto objektu je `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objekt, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="6e8d5-111">Další informace naleznete v [dokumentaci System. text. JSON](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="6e8d5-112">Chcete-li například nakonfigurovat serializátor, aby neměnil velká a malá písmena názvů vlastností namísto výchozích názvů "camelCase", použijte následující kód v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="6e8d5-113">V klientovi .NET existuje stejná `AddJsonProtocol` rozšiřující metoda v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="6e8d5-114">`Microsoft.Extensions.DependencyInjection` Obor názvů musí být importován, aby bylo možné přeložit metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="6e8d5-115">V tuto chvíli není možné konfigurovat serializaci JSON v klientovi jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="6e8d5-116">Přepnout na Newtonsoft. JSON</span><span class="sxs-lookup"><span data-stu-id="6e8d5-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="6e8d5-117">Pokud potřebujete funkce nástroje `Newtonsoft.Json` , které nejsou podporovány v `System.Text.Json`systému, přečtěte si téma [Switch to Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="6e8d5-118">Možnosti serializace MessagePack</span><span class="sxs-lookup"><span data-stu-id="6e8d5-118">MessagePack serialization options</span></span>

<span data-ttu-id="6e8d5-119">Serializaci MessagePack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6e8d5-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="6e8d5-120">Další podrobnosti najdete [v tématu MessagePack v nástroji Signal](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6e8d5-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="6e8d5-121">V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi v jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="6e8d5-122">Konfigurovat možnosti serveru</span><span class="sxs-lookup"><span data-stu-id="6e8d5-122">Configure server options</span></span>

<span data-ttu-id="6e8d5-123">Následující tabulka obsahuje popis možností konfigurace Center pro signály:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="6e8d5-124">Možnost</span><span class="sxs-lookup"><span data-stu-id="6e8d5-124">Option</span></span> | <span data-ttu-id="6e8d5-125">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-125">Default Value</span></span> | <span data-ttu-id="6e8d5-126">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="6e8d5-127">30 sekund</span><span class="sxs-lookup"><span data-stu-id="6e8d5-127">30 seconds</span></span> | <span data-ttu-id="6e8d5-128">Server bude předpokládat, že je klient odpojený, pokud v tomto intervalu neobdržel zprávu (včetně Keep-Alive).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="6e8d5-129">Může trvat delší dobu, než je tento časový limit, aby bylo možné klienta ve skutečnosti označit jako odpojený, protože to je implementováno.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="6e8d5-130">Doporučená hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="6e8d5-131">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e8d5-131">15 seconds</span></span> | <span data-ttu-id="6e8d5-132">Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="6e8d5-133">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6e8d5-134">Další informace o procesu handshake najdete v tématu [specifikace protokolu centra signalizace](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="6e8d5-135">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e8d5-135">15 seconds</span></span> | <span data-ttu-id="6e8d5-136">Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="6e8d5-137">Při změně `KeepAliveInterval` `serverTimeoutInMilliseconds` nastavení změňte `ServerTimeout` / nastavení v klientovi.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="6e8d5-138">`ServerTimeout` / Doporučená `serverTimeoutInMilliseconds` hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="6e8d5-139">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="6e8d5-139">All installed protocols</span></span> | <span data-ttu-id="6e8d5-140">Protokoly podporované tímto rozbočovačem</span><span class="sxs-lookup"><span data-stu-id="6e8d5-140">Protocols supported by this hub.</span></span> <span data-ttu-id="6e8d5-141">Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="6e8d5-142">Pokud `true`se do klientů vrátí podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="6e8d5-143">Výchozí hodnota je `false`, protože tyto zprávy o výjimce můžou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="6e8d5-144">Maximální počet položek, které lze uložit do vyrovnávací paměti pro datové proudy pro odeslání klienta.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="6e8d5-145">Pokud je dosaženo tohoto limitu, zpracování volání je blokováno, dokud server nezpracovává položky datového proudu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="6e8d5-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="6e8d5-146">32 KB</span></span> | <span data-ttu-id="6e8d5-147">Maximální velikost jedné příchozí zprávy centra</span><span class="sxs-lookup"><span data-stu-id="6e8d5-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="6e8d5-148">Možnosti lze nakonfigurovat pro všechna centra tím, že poskytnete možnosti delegáta `AddSignalR` pro volání `Startup.ConfigureServices`v.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="6e8d5-149">Možnosti pro jeden rozbočovač přepíší globální možnosti poskytované v `AddSignalR` a můžou být nakonfigurované pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="6e8d5-150">Rozšířené možnosti konfigurace protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="6e8d5-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="6e8d5-151">Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="6e8d5-152">Tyto možnosti jsou nakonfigurovány předáním delegáta [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="6e8d5-153">Následující tabulka popisuje možnosti konfigurace upřesňujících možností protokolu HTTP pro ASP.NET Core signalizaci:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="6e8d5-154">Možnost</span><span class="sxs-lookup"><span data-stu-id="6e8d5-154">Option</span></span> | <span data-ttu-id="6e8d5-155">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-155">Default Value</span></span> | <span data-ttu-id="6e8d5-156">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="6e8d5-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="6e8d5-157">32 KB</span></span> | <span data-ttu-id="6e8d5-158">Maximální počet bajtů přijatých od klienta, které jsou ukládány do vyrovnávací paměti serveru před použitím protitlaku.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="6e8d5-159">Zvýšením této hodnoty umožníte, aby server přijímal větší zprávy rychleji bez použití zatížení, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="6e8d5-160">Data se `Authorize` automaticky shromažďují z atributů použitých pro třídu centra.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="6e8d5-161">Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru</span><span class="sxs-lookup"><span data-stu-id="6e8d5-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="6e8d5-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="6e8d5-162">32 KB</span></span> | <span data-ttu-id="6e8d5-163">Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru před pozorováním protitlaku.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="6e8d5-164">Zvýšením této hodnoty umožníte serveru rychleji ukládat větší zprávy bez nutnosti očekávat zatížení, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="6e8d5-165">Všechny přenosy jsou povolené.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-165">All Transports are enabled.</span></span> | <span data-ttu-id="6e8d5-166">Bitové příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenos, který může klient použít pro připojení.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="6e8d5-167">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-167">See below.</span></span> | <span data-ttu-id="6e8d5-168">Další možnosti specifické pro přenos dlouhého cyklického dotazování.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="6e8d5-169">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-169">See below.</span></span> | <span data-ttu-id="6e8d5-170">Další možnosti specifické pro přenos pomocí protokolu WebSockets</span><span class="sxs-lookup"><span data-stu-id="6e8d5-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="6e8d5-171">0</span><span class="sxs-lookup"><span data-stu-id="6e8d5-171">0</span></span> | <span data-ttu-id="6e8d5-172">Zadejte minimální verzi protokolu Negotiate.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="6e8d5-173">Slouží k omezení klientů na novější verze.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="6e8d5-174">Přenos dlouhého cyklického dotazování má další možnosti, které je možné `LongPolling` konfigurovat pomocí vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="6e8d5-175">Možnost</span><span class="sxs-lookup"><span data-stu-id="6e8d5-175">Option</span></span> | <span data-ttu-id="6e8d5-176">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-176">Default Value</span></span> | <span data-ttu-id="6e8d5-177">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="6e8d5-178">90 sekund</span><span class="sxs-lookup"><span data-stu-id="6e8d5-178">90 seconds</span></span> | <span data-ttu-id="6e8d5-179">Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jedné žádosti o cyklické dotazování.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="6e8d5-180">Snížení této hodnoty způsobí, že klient bude vydávat nové požadavky na dotaz častěji.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="6e8d5-181">Přenos protokolu WebSocket má další možnosti, které je možné konfigurovat pomocí `WebSockets` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="6e8d5-182">Možnost</span><span class="sxs-lookup"><span data-stu-id="6e8d5-182">Option</span></span> | <span data-ttu-id="6e8d5-183">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-183">Default Value</span></span> | <span data-ttu-id="6e8d5-184">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="6e8d5-185">5 sekund</span><span class="sxs-lookup"><span data-stu-id="6e8d5-185">5 seconds</span></span> | <span data-ttu-id="6e8d5-186">Pokud se po ukončení serveru aplikace v tomto časovém intervalu nepovede zavřít, připojení se ukončí.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="6e8d5-187">Delegát, který lze použít k nastavení `Sec-WebSocket-Protocol` záhlaví na vlastní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="6e8d5-188">Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že se vrátí požadovaná hodnota.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="6e8d5-189">Konfigurace možností klienta</span><span class="sxs-lookup"><span data-stu-id="6e8d5-189">Configure client options</span></span>

<span data-ttu-id="6e8d5-190">Možnosti klienta lze nakonfigurovat pro `HubConnectionBuilder` typ (k dispozici v klientech rozhraní .NET a v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="6e8d5-191">Je také k dispozici v klientovi Java, ale `HttpHubConnectionBuilder` podtřídou je to, co obsahuje možnosti konfigurace tvůrce a také na `HubConnection` sebe sama.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="6e8d5-192">Konfigurovat protokolování</span><span class="sxs-lookup"><span data-stu-id="6e8d5-192">Configure logging</span></span>

<span data-ttu-id="6e8d5-193">Protokolování je konfigurováno v klientovi .NET pomocí `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="6e8d5-194">Zprostředkovatelé protokolování a filtry je možné zaregistrovat stejným způsobem jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="6e8d5-195">Další informace najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="6e8d5-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="6e8d5-196">Aby bylo možné registrovat poskytovatele protokolování, je nutné nainstalovat potřebné balíčky.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="6e8d5-197">Úplný seznam najdete v části [předdefinovaná zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="6e8d5-198">Chcete-li například povolit protokolování konzoly, nainstalujte balíček `Microsoft.Extensions.Logging.Console` NuGet.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="6e8d5-199">Zavolejte metodu `AddConsole` rozšíření:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="6e8d5-200">V klientovi jazyka JavaScript existuje podobná `configureLogging` metoda.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="6e8d5-201">Zadejte `LogLevel` hodnotu, která určuje minimální úroveň zpráv protokolu, které mají být vyprodukovány.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="6e8d5-202">Protokoly se zapisují do okna konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="6e8d5-203">Místo `LogLevel` hodnoty můžete zadat také `string` hodnotu představující název úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="6e8d5-204">To je užitečné při konfiguraci protokolování signálu v prostředích, kde nemáte přístup k `LogLevel` konstantám.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="6e8d5-205">V následující tabulce jsou uvedeny dostupné úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-205">The following table lists the available log levels.</span></span> <span data-ttu-id="6e8d5-206">Hodnota, kterou zadáte `configureLogging` , nastaví **minimální** úroveň protokolu, která se bude protokolovat.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="6e8d5-207">Zprávy zaznamenané na této úrovni **nebo úrovně uvedené za ní v tabulce**budou protokolovány.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="6e8d5-208">Řetězec</span><span class="sxs-lookup"><span data-stu-id="6e8d5-208">String</span></span>                      | <span data-ttu-id="6e8d5-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="6e8d5-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="6e8d5-210">`info`**nebo**`information`</span><span class="sxs-lookup"><span data-stu-id="6e8d5-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="6e8d5-211">`warn`**nebo**`warning`</span><span class="sxs-lookup"><span data-stu-id="6e8d5-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="6e8d5-212">Chcete-li protokolování zcela zakázat `signalR.LogLevel.None` , zadejte `configureLogging` v metodě.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="6e8d5-213">Další informace o protokolování najdete v [dokumentaci k nástroji pro diagnostiku signálu](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="6e8d5-214">Klient Java Signal používá knihovnu [SLF4J](https://www.slf4j.org/) k protokolování.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="6e8d5-215">Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="6e8d5-216">Následující fragment kódu ukazuje, jak používat `java.util.logging` s klientem nástroje pro signalizaci v jazyce Java.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="6e8d5-217">Pokud ve svých závislostech nenakonfigurujete protokolování, SLF4J načte výchozí protokolovací nástroj No-operation s následující zprávou upozornění:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="6e8d5-218">Tuto možnost lze bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="6e8d5-219">Konfigurace povolených přenosů</span><span class="sxs-lookup"><span data-stu-id="6e8d5-219">Configure allowed transports</span></span>

<span data-ttu-id="6e8d5-220">Přenosy používané signálem může být nakonfigurována při `WithUrl` volání (`withUrl` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="6e8d5-221">Bitové nebo hodnoty `HttpTransportType` lze použít k omezení klienta na používání pouze zadaných přenosů.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="6e8d5-222">Ve výchozím nastavení jsou povolené všechny přenosy.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-222">All transports are enabled by default.</span></span>

<span data-ttu-id="6e8d5-223">Pokud třeba chcete zakázat přenos událostí odeslaných serverem, ale povolit WebSockets a dlouhá připojení s dotazem:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="6e8d5-224">V klientu jazyka JavaScript jsou přenosy konfigurovány nastavením `transport` pole v objektu Options, který je k dispozici `withUrl`pro:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="6e8d5-225">V této verzi je jediným dostupným přenosem klientský WebSocket v jazyce Java.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="6e8d5-226">V klientovi Java se přenos vybere s `withTransport` metodou na. `HttpHubConnectionBuilder`</span><span class="sxs-lookup"><span data-stu-id="6e8d5-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="6e8d5-227">Klient Java standardně používá přenos pomocí protokolu WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="6e8d5-228">Klient Java Signale ještě nepodporuje záložní přenos.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="6e8d5-229">Konfigurace ověřování nosiče</span><span class="sxs-lookup"><span data-stu-id="6e8d5-229">Configure bearer authentication</span></span>

<span data-ttu-id="6e8d5-230">Chcete-li poskytnout ověřovací data společně s požadavky na signalizaci `AccessTokenProvider` , použijte`accessTokenFactory` možnost (v jazyce JavaScript) k určení funkce, která vrací požadovaný přístupový token.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="6e8d5-231">V klientovi .NET se tento přístupový token předává jako token ověřování HTTP "Bearer" (s použitím `Authorization` hlavičky s typem `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="6e8d5-232">V klientu jazyka JavaScript se přístupový token používá jako nosný token, **s výjimkou** případů, kdy rozhraní API prohlížeče omezuje možnost použít hlavičky (konkrétně v požadavcích na události odeslané serverem a objekty WebSockets).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="6e8d5-233">V těchto případech je přístupový token k dispozici jako hodnota `access_token`řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="6e8d5-234">V klientu .NET lze `AccessTokenProvider` možnost zadat pomocí delegáta možnosti v: `WithUrl`</span><span class="sxs-lookup"><span data-stu-id="6e8d5-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="6e8d5-235">V klientovi JavaScriptu je přístupový token nakonfigurovaný nastavením `accessTokenFactory` pole v objektu Options v: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="6e8d5-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="6e8d5-236">V klientovi pro signalizaci Java můžete nakonfigurovat nosný token, který se použije pro ověřování, a to tak, že do [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)poskytne továrnu přístupového tokenu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="6e8d5-237">Použijte [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) k poskytnutí [>jednoho\<řetězce ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava) .</span><span class="sxs-lookup"><span data-stu-id="6e8d5-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="6e8d5-238">Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="6e8d5-239">Konfigurace možností timeout a Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="6e8d5-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="6e8d5-240">Další možnosti konfigurace časového limitu a chování funkce Keep-Alive jsou k `HubConnection` dispozici na samotném objektu:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="6e8d5-241">.NET</span><span class="sxs-lookup"><span data-stu-id="6e8d5-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6e8d5-242">Možnost</span><span class="sxs-lookup"><span data-stu-id="6e8d5-242">Option</span></span> | <span data-ttu-id="6e8d5-243">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-243">Default value</span></span> | <span data-ttu-id="6e8d5-244">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="6e8d5-245">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="6e8d5-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6e8d5-246">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-246">Timeout for server activity.</span></span> <span data-ttu-id="6e8d5-247">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí `Closed` událost (`onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6e8d5-248">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6e8d5-249">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="6e8d5-250">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e8d5-250">15 seconds</span></span> | <span data-ttu-id="6e8d5-251">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="6e8d5-252">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a spustí `Closed` událost (`onclose` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6e8d5-253">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6e8d5-254">Další informace o procesu handshake najdete v tématu [specifikace protokolu centra signalizace](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="6e8d5-255">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e8d5-255">15 seconds</span></span> | <span data-ttu-id="6e8d5-256">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6e8d5-257">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6e8d5-258">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="6e8d5-259">V klientovi .NET jsou hodnoty časového limitu zadány `TimeSpan` jako hodnoty.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="6e8d5-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6e8d5-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6e8d5-261">Možnost</span><span class="sxs-lookup"><span data-stu-id="6e8d5-261">Option</span></span> | <span data-ttu-id="6e8d5-262">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-262">Default value</span></span> | <span data-ttu-id="6e8d5-263">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="6e8d5-264">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="6e8d5-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6e8d5-265">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-265">Timeout for server activity.</span></span> <span data-ttu-id="6e8d5-266">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onclose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="6e8d5-267">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6e8d5-268">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="6e8d5-269">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="6e8d5-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="6e8d5-270">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6e8d5-271">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6e8d5-272">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="6e8d5-273">Java</span><span class="sxs-lookup"><span data-stu-id="6e8d5-273">Java</span></span>](#tab/java)

| <span data-ttu-id="6e8d5-274">Možnost</span><span class="sxs-lookup"><span data-stu-id="6e8d5-274">Option</span></span> | <span data-ttu-id="6e8d5-275">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-275">Default value</span></span> | <span data-ttu-id="6e8d5-276">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="6e8d5-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="6e8d5-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="6e8d5-278">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="6e8d5-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6e8d5-279">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-279">Timeout for server activity.</span></span> <span data-ttu-id="6e8d5-280">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onClose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="6e8d5-281">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6e8d5-282">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="6e8d5-283">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e8d5-283">15 seconds</span></span> | <span data-ttu-id="6e8d5-284">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="6e8d5-285">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje `onClose` událost.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="6e8d5-286">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6e8d5-287">Další informace o procesu handshake najdete v tématu [specifikace protokolu centra signalizace](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="6e8d5-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="6e8d5-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="6e8d5-289">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="6e8d5-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="6e8d5-290">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6e8d5-291">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6e8d5-292">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="6e8d5-293">Konfigurace dalších možností</span><span class="sxs-lookup"><span data-stu-id="6e8d5-293">Configure additional options</span></span>

<span data-ttu-id="6e8d5-294">Další možnosti lze konfigurovat v `WithUrl` metodě (`withUrl` v jazyce JavaScript) na `HubConnectionBuilder` různých konfiguračních rozhraních API `HttpHubConnectionBuilder` v klientovi Java:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="6e8d5-295">.NET</span><span class="sxs-lookup"><span data-stu-id="6e8d5-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6e8d5-296">Možnost .NET</span><span class="sxs-lookup"><span data-stu-id="6e8d5-296">.NET Option</span></span> |  <span data-ttu-id="6e8d5-297">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-297">Default value</span></span> | <span data-ttu-id="6e8d5-298">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="6e8d5-299">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="6e8d5-300">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6e8d5-301">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6e8d5-302">Toto nastavení se nedá povolit při použití služby signalizace Azure.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="6e8d5-303">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="6e8d5-303">Empty</span></span> | <span data-ttu-id="6e8d5-304">Kolekce certifikátů TLS pro odeslání na požadavky na ověření.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="6e8d5-305">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="6e8d5-305">Empty</span></span> | <span data-ttu-id="6e8d5-306">Kolekce souborů cookie protokolu HTTP, které se mají odeslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="6e8d5-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="6e8d5-307">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="6e8d5-307">Empty</span></span> | <span data-ttu-id="6e8d5-308">Přihlašovací údaje, které se mají poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="6e8d5-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="6e8d5-309">5 sekund</span><span class="sxs-lookup"><span data-stu-id="6e8d5-309">5 seconds</span></span> | <span data-ttu-id="6e8d5-310">Jenom objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-310">WebSockets only.</span></span> <span data-ttu-id="6e8d5-311">Maximální doba, po jejímž uplynutí bude klient čekat na ukončení žádosti o uzavření.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="6e8d5-312">Pokud server nepotvrdí zavření v této době, klient se odpojí.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="6e8d5-313">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="6e8d5-313">Empty</span></span> | <span data-ttu-id="6e8d5-314">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="6e8d5-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="6e8d5-315">Delegát, který lze použít ke konfiguraci nebo nahrazení `HttpMessageHandler` používaného k odeslání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="6e8d5-316">Nepoužívá se pro připojení pomocí protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="6e8d5-317">Tento delegát musí vracet hodnotu, která není null, a obdrží výchozí hodnotu jako parametr.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="6e8d5-318">Buď změňte nastavení u této výchozí hodnoty a vraťte je, nebo vraťte novou `HttpMessageHandler` instanci.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="6e8d5-319">**Při nahrazování obslužné rutiny Nezapomeňte zkopírovat nastavení, která chcete zachovat, od poskytnuté obslužné rutiny, jinak konfigurované možnosti (například soubory cookie a záhlaví) nebudou použity pro novou obslužnou rutinu.**</span><span class="sxs-lookup"><span data-stu-id="6e8d5-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="6e8d5-320">Proxy server HTTP, který se má použít při odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="6e8d5-321">Nastavte tuto logickou hodnotu pro odeslání výchozích přihlašovacích údajů pro požadavky HTTP a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="6e8d5-322">To umožňuje použití ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="6e8d5-323">Delegát, který lze použít ke konfiguraci dalších možností protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="6e8d5-324">Přijímá instanci [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , která se dá použít ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="6e8d5-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6e8d5-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6e8d5-326">Možnost JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="6e8d5-326">JavaScript Option</span></span> | <span data-ttu-id="6e8d5-327">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-327">Default Value</span></span> | <span data-ttu-id="6e8d5-328">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="6e8d5-329">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="6e8d5-330">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-330">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6e8d5-331">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-331">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6e8d5-332">Toto nastavení se nedá povolit při použití služby signalizace Azure.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-332">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="6e8d5-333">Určuje, jestli se budou odesílat přihlašovací údaje s požadavkem CORS.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-333">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="6e8d5-334">Azure App Service používá pro rychlé relace soubory cookie a vyžaduje, aby tato možnost fungovala správně.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-334">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="6e8d5-335">Další informace o CORS s nástrojem Signal najdete v tématu <xref:signalr/security#cross-origin-resource-sharing>.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-335">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="6e8d5-336">Java</span><span class="sxs-lookup"><span data-stu-id="6e8d5-336">Java</span></span>](#tab/java)

| <span data-ttu-id="6e8d5-337">Možnost jazyka Java</span><span class="sxs-lookup"><span data-stu-id="6e8d5-337">Java Option</span></span> | <span data-ttu-id="6e8d5-338">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-338">Default Value</span></span> | <span data-ttu-id="6e8d5-339">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-339">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="6e8d5-340">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-340">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="6e8d5-341">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-341">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6e8d5-342">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-342">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6e8d5-343">Toto nastavení se nedá povolit při použití služby signalizace Azure.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-343">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="6e8d5-344">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="6e8d5-344">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="6e8d5-345">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="6e8d5-345">Empty</span></span> | <span data-ttu-id="6e8d5-346">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="6e8d5-346">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="6e8d5-347">V klientu .NET můžete tyto možnosti upravit pomocí delegáta možností, který je k `WithUrl`dispozici pro:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-347">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="6e8d5-348">V klientovi JavaScriptu lze tyto možnosti poskytnout v objektu jazyka JavaScript, který je k dispozici pro `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-348">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="6e8d5-349">V klientovi Java lze tyto možnosti nakonfigurovat s metodami `HttpHubConnectionBuilder` vrácenými z`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="6e8d5-349">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="6e8d5-350">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="6e8d5-350">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="6e8d5-351">Možnosti serializace JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="6e8d5-351">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="6e8d5-352">ASP.NET Core Signal podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-352">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="6e8d5-353">Každý protokol má možnosti konfigurace serializace.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-353">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="6e8d5-354">Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6e8d5-354">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="6e8d5-355">`AddJsonProtocol`lze přidat po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-355">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6e8d5-356">`AddJsonProtocol` Metoda přebírá delegáta, který přijímá `options` objekt.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-356">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="6e8d5-357">Vlastnost [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) tohoto objektu je `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objekt, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-357">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="6e8d5-358">Další informace naleznete v [dokumentaci System. text. JSON](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-358">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="6e8d5-359">Chcete-li například nakonfigurovat serializátor, aby neměnil velká a malá písmena názvů vlastností namísto výchozích názvů "camelCase", použijte následující kód v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-359">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="6e8d5-360">V klientovi .NET existuje stejná `AddJsonProtocol` rozšiřující metoda v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-360">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="6e8d5-361">`Microsoft.Extensions.DependencyInjection` Obor názvů musí být importován, aby bylo možné přeložit metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-361">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="6e8d5-362">V tuto chvíli není možné konfigurovat serializaci JSON v klientovi jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-362">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="6e8d5-363">Přepnout na Newtonsoft. JSON</span><span class="sxs-lookup"><span data-stu-id="6e8d5-363">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="6e8d5-364">Pokud potřebujete funkce nástroje `Newtonsoft.Json` , které nejsou podporovány v `System.Text.Json`systému, přečtěte si téma [Switch to Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-364">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="6e8d5-365">Možnosti serializace MessagePack</span><span class="sxs-lookup"><span data-stu-id="6e8d5-365">MessagePack serialization options</span></span>

<span data-ttu-id="6e8d5-366">Serializaci MessagePack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6e8d5-366">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="6e8d5-367">Další podrobnosti najdete [v tématu MessagePack v nástroji Signal](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6e8d5-367">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="6e8d5-368">V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi v jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-368">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="6e8d5-369">Konfigurovat možnosti serveru</span><span class="sxs-lookup"><span data-stu-id="6e8d5-369">Configure server options</span></span>

<span data-ttu-id="6e8d5-370">Následující tabulka obsahuje popis možností konfigurace Center pro signály:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-370">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="6e8d5-371">Možnost</span><span class="sxs-lookup"><span data-stu-id="6e8d5-371">Option</span></span> | <span data-ttu-id="6e8d5-372">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-372">Default Value</span></span> | <span data-ttu-id="6e8d5-373">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-373">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="6e8d5-374">30 sekund</span><span class="sxs-lookup"><span data-stu-id="6e8d5-374">30 seconds</span></span> | <span data-ttu-id="6e8d5-375">Server bude předpokládat, že je klient odpojený, pokud v tomto intervalu neobdržel zprávu (včetně Keep-Alive).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-375">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="6e8d5-376">Může trvat delší dobu, než je tento časový limit, aby bylo možné klienta ve skutečnosti označit jako odpojený, protože to je implementováno.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-376">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="6e8d5-377">Doporučená hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-377">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="6e8d5-378">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e8d5-378">15 seconds</span></span> | <span data-ttu-id="6e8d5-379">Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-379">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="6e8d5-380">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-380">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6e8d5-381">Další informace o procesu handshake najdete v tématu [specifikace protokolu centra signalizace](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-381">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="6e8d5-382">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e8d5-382">15 seconds</span></span> | <span data-ttu-id="6e8d5-383">Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-383">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="6e8d5-384">Při změně `KeepAliveInterval` `serverTimeoutInMilliseconds` nastavení změňte `ServerTimeout` / nastavení v klientovi.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-384">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="6e8d5-385">`ServerTimeout` / Doporučená `serverTimeoutInMilliseconds` hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-385">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="6e8d5-386">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="6e8d5-386">All installed protocols</span></span> | <span data-ttu-id="6e8d5-387">Protokoly podporované tímto rozbočovačem</span><span class="sxs-lookup"><span data-stu-id="6e8d5-387">Protocols supported by this hub.</span></span> <span data-ttu-id="6e8d5-388">Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-388">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="6e8d5-389">Pokud `true`se do klientů vrátí podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-389">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="6e8d5-390">Výchozí hodnota je `false`, protože tyto zprávy o výjimce můžou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-390">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="6e8d5-391">Maximální počet položek, které lze uložit do vyrovnávací paměti pro datové proudy pro odeslání klienta.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-391">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="6e8d5-392">Pokud je dosaženo tohoto limitu, zpracování volání je blokováno, dokud server nezpracovává položky datového proudu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-392">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="6e8d5-393">32 KB</span><span class="sxs-lookup"><span data-stu-id="6e8d5-393">32 KB</span></span> | <span data-ttu-id="6e8d5-394">Maximální velikost jedné příchozí zprávy centra</span><span class="sxs-lookup"><span data-stu-id="6e8d5-394">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="6e8d5-395">Možnosti lze nakonfigurovat pro všechna centra tím, že poskytnete možnosti delegáta `AddSignalR` pro volání `Startup.ConfigureServices`v.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-395">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="6e8d5-396">Možnosti pro jeden rozbočovač přepíší globální možnosti poskytované v `AddSignalR` a můžou být nakonfigurované pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-396">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="6e8d5-397">Rozšířené možnosti konfigurace protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="6e8d5-397">Advanced HTTP configuration options</span></span>

<span data-ttu-id="6e8d5-398">Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-398">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="6e8d5-399">Tyto možnosti jsou nakonfigurovány předáním delegáta [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-399">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="6e8d5-400">Následující tabulka popisuje možnosti konfigurace upřesňujících možností protokolu HTTP pro ASP.NET Core signalizaci:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-400">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="6e8d5-401">Možnost</span><span class="sxs-lookup"><span data-stu-id="6e8d5-401">Option</span></span> | <span data-ttu-id="6e8d5-402">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-402">Default Value</span></span> | <span data-ttu-id="6e8d5-403">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-403">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="6e8d5-404">32 KB</span><span class="sxs-lookup"><span data-stu-id="6e8d5-404">32 KB</span></span> | <span data-ttu-id="6e8d5-405">Maximální počet bajtů přijatých od klienta, které jsou ukládány do vyrovnávací paměti serveru před použitím protitlaku.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-405">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="6e8d5-406">Zvýšením této hodnoty umožníte, aby server přijímal větší zprávy rychleji bez použití zatížení, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-406">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="6e8d5-407">Data se `Authorize` automaticky shromažďují z atributů použitých pro třídu centra.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-407">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="6e8d5-408">Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru</span><span class="sxs-lookup"><span data-stu-id="6e8d5-408">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="6e8d5-409">32 KB</span><span class="sxs-lookup"><span data-stu-id="6e8d5-409">32 KB</span></span> | <span data-ttu-id="6e8d5-410">Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru před pozorováním protitlaku.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-410">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="6e8d5-411">Zvýšením této hodnoty umožníte serveru rychleji ukládat větší zprávy bez nutnosti očekávat zatížení, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-411">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="6e8d5-412">Všechny přenosy jsou povolené.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-412">All Transports are enabled.</span></span> | <span data-ttu-id="6e8d5-413">Bitové příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenos, který může klient použít pro připojení.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-413">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="6e8d5-414">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-414">See below.</span></span> | <span data-ttu-id="6e8d5-415">Další možnosti specifické pro přenos dlouhého cyklického dotazování.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-415">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="6e8d5-416">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-416">See below.</span></span> | <span data-ttu-id="6e8d5-417">Další možnosti specifické pro přenos pomocí protokolu WebSockets</span><span class="sxs-lookup"><span data-stu-id="6e8d5-417">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="6e8d5-418">0</span><span class="sxs-lookup"><span data-stu-id="6e8d5-418">0</span></span> | <span data-ttu-id="6e8d5-419">Zadejte minimální verzi protokolu Negotiate.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-419">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="6e8d5-420">Slouží k omezení klientů na novější verze.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-420">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="6e8d5-421">Přenos dlouhého cyklického dotazování má další možnosti, které je možné `LongPolling` konfigurovat pomocí vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-421">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="6e8d5-422">Možnost</span><span class="sxs-lookup"><span data-stu-id="6e8d5-422">Option</span></span> | <span data-ttu-id="6e8d5-423">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-423">Default Value</span></span> | <span data-ttu-id="6e8d5-424">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-424">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="6e8d5-425">90 sekund</span><span class="sxs-lookup"><span data-stu-id="6e8d5-425">90 seconds</span></span> | <span data-ttu-id="6e8d5-426">Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jedné žádosti o cyklické dotazování.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-426">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="6e8d5-427">Snížení této hodnoty způsobí, že klient bude vydávat nové požadavky na dotaz častěji.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-427">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="6e8d5-428">Přenos protokolu WebSocket má další možnosti, které je možné konfigurovat pomocí `WebSockets` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-428">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="6e8d5-429">Možnost</span><span class="sxs-lookup"><span data-stu-id="6e8d5-429">Option</span></span> | <span data-ttu-id="6e8d5-430">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-430">Default Value</span></span> | <span data-ttu-id="6e8d5-431">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-431">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="6e8d5-432">5 sekund</span><span class="sxs-lookup"><span data-stu-id="6e8d5-432">5 seconds</span></span> | <span data-ttu-id="6e8d5-433">Pokud se po ukončení serveru aplikace v tomto časovém intervalu nepovede zavřít, připojení se ukončí.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-433">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="6e8d5-434">Delegát, který lze použít k nastavení `Sec-WebSocket-Protocol` záhlaví na vlastní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-434">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="6e8d5-435">Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že se vrátí požadovaná hodnota.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-435">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="6e8d5-436">Konfigurace možností klienta</span><span class="sxs-lookup"><span data-stu-id="6e8d5-436">Configure client options</span></span>

<span data-ttu-id="6e8d5-437">Možnosti klienta lze nakonfigurovat pro `HubConnectionBuilder` typ (k dispozici v klientech rozhraní .NET a v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-437">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="6e8d5-438">Je také k dispozici v klientovi Java, ale `HttpHubConnectionBuilder` podtřídou je to, co obsahuje možnosti konfigurace tvůrce a také na `HubConnection` sebe sama.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-438">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="6e8d5-439">Konfigurovat protokolování</span><span class="sxs-lookup"><span data-stu-id="6e8d5-439">Configure logging</span></span>

<span data-ttu-id="6e8d5-440">Protokolování je konfigurováno v klientovi .NET pomocí `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-440">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="6e8d5-441">Zprostředkovatelé protokolování a filtry je možné zaregistrovat stejným způsobem jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-441">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="6e8d5-442">Další informace najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="6e8d5-442">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="6e8d5-443">Aby bylo možné registrovat poskytovatele protokolování, je nutné nainstalovat potřebné balíčky.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-443">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="6e8d5-444">Úplný seznam najdete v části [předdefinovaná zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-444">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="6e8d5-445">Chcete-li například povolit protokolování konzoly, nainstalujte balíček `Microsoft.Extensions.Logging.Console` NuGet.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-445">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="6e8d5-446">Zavolejte metodu `AddConsole` rozšíření:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-446">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="6e8d5-447">V klientovi jazyka JavaScript existuje podobná `configureLogging` metoda.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-447">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="6e8d5-448">Zadejte `LogLevel` hodnotu, která určuje minimální úroveň zpráv protokolu, které mají být vyprodukovány.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-448">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="6e8d5-449">Protokoly se zapisují do okna konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-449">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="6e8d5-450">Místo `LogLevel` hodnoty můžete zadat také `string` hodnotu představující název úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-450">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="6e8d5-451">To je užitečné při konfiguraci protokolování signálu v prostředích, kde nemáte přístup k `LogLevel` konstantám.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-451">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="6e8d5-452">V následující tabulce jsou uvedeny dostupné úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-452">The following table lists the available log levels.</span></span> <span data-ttu-id="6e8d5-453">Hodnota, kterou zadáte `configureLogging` , nastaví **minimální** úroveň protokolu, která se bude protokolovat.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-453">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="6e8d5-454">Zprávy zaznamenané na této úrovni **nebo úrovně uvedené za ní v tabulce**budou protokolovány.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-454">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="6e8d5-455">Řetězec</span><span class="sxs-lookup"><span data-stu-id="6e8d5-455">String</span></span>                      | <span data-ttu-id="6e8d5-456">LogLevel</span><span class="sxs-lookup"><span data-stu-id="6e8d5-456">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="6e8d5-457">`info`**nebo**`information`</span><span class="sxs-lookup"><span data-stu-id="6e8d5-457">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="6e8d5-458">`warn`**nebo**`warning`</span><span class="sxs-lookup"><span data-stu-id="6e8d5-458">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="6e8d5-459">Chcete-li protokolování zcela zakázat `signalR.LogLevel.None` , zadejte `configureLogging` v metodě.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-459">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="6e8d5-460">Další informace o protokolování najdete v [dokumentaci k nástroji pro diagnostiku signálu](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-460">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="6e8d5-461">Klient Java Signal používá knihovnu [SLF4J](https://www.slf4j.org/) k protokolování.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-461">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="6e8d5-462">Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-462">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="6e8d5-463">Následující fragment kódu ukazuje, jak používat `java.util.logging` s klientem nástroje pro signalizaci v jazyce Java.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-463">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="6e8d5-464">Pokud ve svých závislostech nenakonfigurujete protokolování, SLF4J načte výchozí protokolovací nástroj No-operation s následující zprávou upozornění:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-464">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="6e8d5-465">Tuto možnost lze bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-465">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="6e8d5-466">Konfigurace povolených přenosů</span><span class="sxs-lookup"><span data-stu-id="6e8d5-466">Configure allowed transports</span></span>

<span data-ttu-id="6e8d5-467">Přenosy používané signálem může být nakonfigurována při `WithUrl` volání (`withUrl` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-467">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="6e8d5-468">Bitové nebo hodnoty `HttpTransportType` lze použít k omezení klienta na používání pouze zadaných přenosů.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-468">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="6e8d5-469">Ve výchozím nastavení jsou povolené všechny přenosy.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-469">All transports are enabled by default.</span></span>

<span data-ttu-id="6e8d5-470">Pokud třeba chcete zakázat přenos událostí odeslaných serverem, ale povolit WebSockets a dlouhá připojení s dotazem:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-470">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="6e8d5-471">V klientu jazyka JavaScript jsou přenosy konfigurovány nastavením `transport` pole v objektu Options, který je k dispozici `withUrl`pro:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-471">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="6e8d5-472">V této verzi je jediným dostupným přenosem klientský WebSocket v jazyce Java.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-472">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="6e8d5-473">V klientovi Java se přenos vybere s `withTransport` metodou na. `HttpHubConnectionBuilder`</span><span class="sxs-lookup"><span data-stu-id="6e8d5-473">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="6e8d5-474">Klient Java standardně používá přenos pomocí protokolu WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-474">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="6e8d5-475">Klient Java Signale ještě nepodporuje záložní přenos.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-475">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="6e8d5-476">Konfigurace ověřování nosiče</span><span class="sxs-lookup"><span data-stu-id="6e8d5-476">Configure bearer authentication</span></span>

<span data-ttu-id="6e8d5-477">Chcete-li poskytnout ověřovací data společně s požadavky na signalizaci `AccessTokenProvider` , použijte`accessTokenFactory` možnost (v jazyce JavaScript) k určení funkce, která vrací požadovaný přístupový token.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-477">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="6e8d5-478">V klientovi .NET se tento přístupový token předává jako token ověřování HTTP "Bearer" (s použitím `Authorization` hlavičky s typem `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-478">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="6e8d5-479">V klientu jazyka JavaScript se přístupový token používá jako nosný token, **s výjimkou** případů, kdy rozhraní API prohlížeče omezuje možnost použít hlavičky (konkrétně v požadavcích na události odeslané serverem a objekty WebSockets).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-479">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="6e8d5-480">V těchto případech je přístupový token k dispozici jako hodnota `access_token`řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-480">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="6e8d5-481">V klientu .NET lze `AccessTokenProvider` možnost zadat pomocí delegáta možnosti v: `WithUrl`</span><span class="sxs-lookup"><span data-stu-id="6e8d5-481">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="6e8d5-482">V klientovi JavaScriptu je přístupový token nakonfigurovaný nastavením `accessTokenFactory` pole v objektu Options v: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="6e8d5-482">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="6e8d5-483">V klientovi pro signalizaci Java můžete nakonfigurovat nosný token, který se použije pro ověřování, a to tak, že do [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)poskytne továrnu přístupového tokenu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-483">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="6e8d5-484">Použijte [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) k poskytnutí [>jednoho\<řetězce ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava) .</span><span class="sxs-lookup"><span data-stu-id="6e8d5-484">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="6e8d5-485">Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-485">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="6e8d5-486">Konfigurace možností timeout a Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="6e8d5-486">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="6e8d5-487">Další možnosti konfigurace časového limitu a chování funkce Keep-Alive jsou k `HubConnection` dispozici na samotném objektu:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-487">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="6e8d5-488">.NET</span><span class="sxs-lookup"><span data-stu-id="6e8d5-488">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6e8d5-489">Možnost</span><span class="sxs-lookup"><span data-stu-id="6e8d5-489">Option</span></span> | <span data-ttu-id="6e8d5-490">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-490">Default value</span></span> | <span data-ttu-id="6e8d5-491">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-491">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="6e8d5-492">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="6e8d5-492">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6e8d5-493">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-493">Timeout for server activity.</span></span> <span data-ttu-id="6e8d5-494">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí `Closed` událost (`onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-494">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6e8d5-495">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-495">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6e8d5-496">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-496">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="6e8d5-497">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e8d5-497">15 seconds</span></span> | <span data-ttu-id="6e8d5-498">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-498">Timeout for initial server handshake.</span></span> <span data-ttu-id="6e8d5-499">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a spustí `Closed` událost (`onclose` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-499">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6e8d5-500">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-500">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6e8d5-501">Další informace o procesu handshake najdete v tématu [specifikace protokolu centra signalizace](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-501">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="6e8d5-502">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e8d5-502">15 seconds</span></span> | <span data-ttu-id="6e8d5-503">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-503">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6e8d5-504">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-504">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6e8d5-505">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-505">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="6e8d5-506">V klientovi .NET jsou hodnoty časového limitu zadány `TimeSpan` jako hodnoty.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-506">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="6e8d5-507">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6e8d5-507">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6e8d5-508">Možnost</span><span class="sxs-lookup"><span data-stu-id="6e8d5-508">Option</span></span> | <span data-ttu-id="6e8d5-509">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-509">Default value</span></span> | <span data-ttu-id="6e8d5-510">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-510">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="6e8d5-511">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="6e8d5-511">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6e8d5-512">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-512">Timeout for server activity.</span></span> <span data-ttu-id="6e8d5-513">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onclose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-513">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="6e8d5-514">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-514">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6e8d5-515">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-515">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="6e8d5-516">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="6e8d5-516">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="6e8d5-517">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-517">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6e8d5-518">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-518">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6e8d5-519">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-519">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="6e8d5-520">Java</span><span class="sxs-lookup"><span data-stu-id="6e8d5-520">Java</span></span>](#tab/java)

| <span data-ttu-id="6e8d5-521">Možnost</span><span class="sxs-lookup"><span data-stu-id="6e8d5-521">Option</span></span> | <span data-ttu-id="6e8d5-522">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-522">Default value</span></span> | <span data-ttu-id="6e8d5-523">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-523">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="6e8d5-524">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="6e8d5-524">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="6e8d5-525">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="6e8d5-525">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6e8d5-526">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-526">Timeout for server activity.</span></span> <span data-ttu-id="6e8d5-527">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onClose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-527">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="6e8d5-528">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-528">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6e8d5-529">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-529">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="6e8d5-530">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e8d5-530">15 seconds</span></span> | <span data-ttu-id="6e8d5-531">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-531">Timeout for initial server handshake.</span></span> <span data-ttu-id="6e8d5-532">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje `onClose` událost.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-532">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="6e8d5-533">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-533">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6e8d5-534">Další informace o procesu handshake najdete v tématu [specifikace protokolu centra signalizace](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-534">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="6e8d5-535">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="6e8d5-535">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="6e8d5-536">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="6e8d5-536">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="6e8d5-537">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-537">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6e8d5-538">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-538">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6e8d5-539">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-539">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="6e8d5-540">Konfigurace dalších možností</span><span class="sxs-lookup"><span data-stu-id="6e8d5-540">Configure additional options</span></span>

<span data-ttu-id="6e8d5-541">Další možnosti lze konfigurovat v `WithUrl` metodě (`withUrl` v jazyce JavaScript) na `HubConnectionBuilder` různých konfiguračních rozhraních API `HttpHubConnectionBuilder` v klientovi Java:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-541">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="6e8d5-542">.NET</span><span class="sxs-lookup"><span data-stu-id="6e8d5-542">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6e8d5-543">Možnost .NET</span><span class="sxs-lookup"><span data-stu-id="6e8d5-543">.NET Option</span></span> |  <span data-ttu-id="6e8d5-544">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-544">Default value</span></span> | <span data-ttu-id="6e8d5-545">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-545">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="6e8d5-546">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-546">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="6e8d5-547">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-547">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6e8d5-548">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-548">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6e8d5-549">Toto nastavení se nedá povolit při použití služby signalizace Azure.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-549">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="6e8d5-550">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="6e8d5-550">Empty</span></span> | <span data-ttu-id="6e8d5-551">Kolekce certifikátů TLS pro odeslání na požadavky na ověření.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-551">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="6e8d5-552">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="6e8d5-552">Empty</span></span> | <span data-ttu-id="6e8d5-553">Kolekce souborů cookie protokolu HTTP, které se mají odeslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="6e8d5-553">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="6e8d5-554">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="6e8d5-554">Empty</span></span> | <span data-ttu-id="6e8d5-555">Přihlašovací údaje, které se mají poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="6e8d5-555">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="6e8d5-556">5 sekund</span><span class="sxs-lookup"><span data-stu-id="6e8d5-556">5 seconds</span></span> | <span data-ttu-id="6e8d5-557">Jenom objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-557">WebSockets only.</span></span> <span data-ttu-id="6e8d5-558">Maximální doba, po jejímž uplynutí bude klient čekat na ukončení žádosti o uzavření.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-558">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="6e8d5-559">Pokud server nepotvrdí zavření v této době, klient se odpojí.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-559">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="6e8d5-560">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="6e8d5-560">Empty</span></span> | <span data-ttu-id="6e8d5-561">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="6e8d5-561">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="6e8d5-562">Delegát, který lze použít ke konfiguraci nebo nahrazení `HttpMessageHandler` používaného k odeslání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-562">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="6e8d5-563">Nepoužívá se pro připojení pomocí protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-563">Not used for WebSocket connections.</span></span> <span data-ttu-id="6e8d5-564">Tento delegát musí vracet hodnotu, která není null, a obdrží výchozí hodnotu jako parametr.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-564">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="6e8d5-565">Buď změňte nastavení u této výchozí hodnoty a vraťte je, nebo vraťte novou `HttpMessageHandler` instanci.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-565">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="6e8d5-566">**Při nahrazování obslužné rutiny Nezapomeňte zkopírovat nastavení, která chcete zachovat, od poskytnuté obslužné rutiny, jinak konfigurované možnosti (například soubory cookie a záhlaví) nebudou použity pro novou obslužnou rutinu.**</span><span class="sxs-lookup"><span data-stu-id="6e8d5-566">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="6e8d5-567">Proxy server HTTP, který se má použít při odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-567">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="6e8d5-568">Nastavte tuto logickou hodnotu pro odeslání výchozích přihlašovacích údajů pro požadavky HTTP a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-568">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="6e8d5-569">To umožňuje použití ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-569">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="6e8d5-570">Delegát, který lze použít ke konfiguraci dalších možností protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-570">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="6e8d5-571">Přijímá instanci [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , která se dá použít ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-571">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="6e8d5-572">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6e8d5-572">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6e8d5-573">Možnost JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="6e8d5-573">JavaScript Option</span></span> | <span data-ttu-id="6e8d5-574">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-574">Default Value</span></span> | <span data-ttu-id="6e8d5-575">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-575">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="6e8d5-576">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-576">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="6e8d5-577">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-577">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6e8d5-578">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-578">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6e8d5-579">Toto nastavení se nedá povolit při použití služby signalizace Azure.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-579">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="6e8d5-580">Java</span><span class="sxs-lookup"><span data-stu-id="6e8d5-580">Java</span></span>](#tab/java)

| <span data-ttu-id="6e8d5-581">Možnost jazyka Java</span><span class="sxs-lookup"><span data-stu-id="6e8d5-581">Java Option</span></span> | <span data-ttu-id="6e8d5-582">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-582">Default Value</span></span> | <span data-ttu-id="6e8d5-583">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-583">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="6e8d5-584">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-584">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="6e8d5-585">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-585">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6e8d5-586">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-586">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6e8d5-587">Toto nastavení se nedá povolit při použití služby signalizace Azure.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-587">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="6e8d5-588">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="6e8d5-588">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="6e8d5-589">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="6e8d5-589">Empty</span></span> | <span data-ttu-id="6e8d5-590">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="6e8d5-590">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="6e8d5-591">V klientu .NET můžete tyto možnosti upravit pomocí delegáta možností, který je k `WithUrl`dispozici pro:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-591">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="6e8d5-592">V klientovi JavaScriptu lze tyto možnosti poskytnout v objektu jazyka JavaScript, který je k dispozici pro `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-592">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="6e8d5-593">V klientovi Java lze tyto možnosti nakonfigurovat s metodami `HttpHubConnectionBuilder` vrácenými z`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="6e8d5-593">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="6e8d5-594">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="6e8d5-594">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="6e8d5-595">Možnosti serializace JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="6e8d5-595">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="6e8d5-596">ASP.NET Core Signal podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-596">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="6e8d5-597">Každý protokol má možnosti konfigurace serializace.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-597">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="6e8d5-598">Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6e8d5-598">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="6e8d5-599">`AddJsonProtocol`lze přidat po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-599">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="6e8d5-600">`AddJsonProtocol` Metoda přebírá delegáta, který přijímá `options` objekt.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-600">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="6e8d5-601">Vlastnost [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) tohoto objektu je `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objekt, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-601">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="6e8d5-602">Další informace naleznete v [dokumentaci System. text. JSON](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-602">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="6e8d5-603">Chcete-li například nakonfigurovat serializátor, aby neměnil velká a malá písmena názvů vlastností namísto výchozích názvů "camelCase", použijte následující kód v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-603">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="6e8d5-604">V klientovi .NET existuje stejná `AddJsonProtocol` rozšiřující metoda v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-604">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="6e8d5-605">`Microsoft.Extensions.DependencyInjection` Obor názvů musí být importován, aby bylo možné přeložit metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-605">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="6e8d5-606">V tuto chvíli není možné konfigurovat serializaci JSON v klientovi jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-606">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="6e8d5-607">Přepnout na Newtonsoft. JSON</span><span class="sxs-lookup"><span data-stu-id="6e8d5-607">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="6e8d5-608">Pokud potřebujete funkce nástroje `Newtonsoft.Json` , které nejsou podporovány v `System.Text.Json`systému, přečtěte si téma [Switch to Newtonsoft. JSON](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-608">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="6e8d5-609">Možnosti serializace MessagePack</span><span class="sxs-lookup"><span data-stu-id="6e8d5-609">MessagePack serialization options</span></span>

<span data-ttu-id="6e8d5-610">Serializaci MessagePack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6e8d5-610">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="6e8d5-611">Další podrobnosti najdete [v tématu MessagePack v nástroji Signal](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6e8d5-611">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="6e8d5-612">V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi v jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-612">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="6e8d5-613">Konfigurovat možnosti serveru</span><span class="sxs-lookup"><span data-stu-id="6e8d5-613">Configure server options</span></span>

<span data-ttu-id="6e8d5-614">Následující tabulka obsahuje popis možností konfigurace Center pro signály:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-614">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="6e8d5-615">Možnost</span><span class="sxs-lookup"><span data-stu-id="6e8d5-615">Option</span></span> | <span data-ttu-id="6e8d5-616">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-616">Default Value</span></span> | <span data-ttu-id="6e8d5-617">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-617">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="6e8d5-618">30 sekund</span><span class="sxs-lookup"><span data-stu-id="6e8d5-618">30 seconds</span></span> | <span data-ttu-id="6e8d5-619">Server bude předpokládat, že je klient odpojený, pokud v tomto intervalu neobdržel zprávu (včetně Keep-Alive).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-619">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="6e8d5-620">Může trvat delší dobu, než je tento časový limit, aby bylo možné klienta ve skutečnosti označit jako odpojený, protože to je implementováno.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-620">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="6e8d5-621">Doporučená hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-621">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="6e8d5-622">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e8d5-622">15 seconds</span></span> | <span data-ttu-id="6e8d5-623">Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-623">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="6e8d5-624">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-624">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6e8d5-625">Další informace o procesu handshake najdete v tématu [specifikace protokolu centra signalizace](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-625">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="6e8d5-626">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e8d5-626">15 seconds</span></span> | <span data-ttu-id="6e8d5-627">Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-627">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="6e8d5-628">Při změně `KeepAliveInterval` `serverTimeoutInMilliseconds` nastavení změňte `ServerTimeout` / nastavení v klientovi.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-628">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="6e8d5-629">`ServerTimeout` / Doporučená `serverTimeoutInMilliseconds` hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-629">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="6e8d5-630">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="6e8d5-630">All installed protocols</span></span> | <span data-ttu-id="6e8d5-631">Protokoly podporované tímto rozbočovačem</span><span class="sxs-lookup"><span data-stu-id="6e8d5-631">Protocols supported by this hub.</span></span> <span data-ttu-id="6e8d5-632">Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-632">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="6e8d5-633">Pokud `true`se do klientů vrátí podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-633">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="6e8d5-634">Výchozí hodnota je `false`, protože tyto zprávy o výjimce můžou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-634">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="6e8d5-635">Maximální počet položek, které lze uložit do vyrovnávací paměti pro datové proudy pro odeslání klienta.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-635">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="6e8d5-636">Pokud je dosaženo tohoto limitu, zpracování volání je blokováno, dokud server nezpracovává položky datového proudu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-636">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="6e8d5-637">32 KB</span><span class="sxs-lookup"><span data-stu-id="6e8d5-637">32 KB</span></span> | <span data-ttu-id="6e8d5-638">Maximální velikost jedné příchozí zprávy centra</span><span class="sxs-lookup"><span data-stu-id="6e8d5-638">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="6e8d5-639">Možnosti lze nakonfigurovat pro všechna centra tím, že poskytnete možnosti delegáta `AddSignalR` pro volání `Startup.ConfigureServices`v.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-639">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="6e8d5-640">Možnosti pro jeden rozbočovač přepíší globální možnosti poskytované v `AddSignalR` a můžou být nakonfigurované pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-640">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="6e8d5-641">Rozšířené možnosti konfigurace protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="6e8d5-641">Advanced HTTP configuration options</span></span>

<span data-ttu-id="6e8d5-642">Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-642">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="6e8d5-643">Tyto možnosti jsou nakonfigurovány předáním delegáta [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-643">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="6e8d5-644">Následující tabulka popisuje možnosti konfigurace upřesňujících možností protokolu HTTP pro ASP.NET Core signalizaci:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-644">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="6e8d5-645">Možnost</span><span class="sxs-lookup"><span data-stu-id="6e8d5-645">Option</span></span> | <span data-ttu-id="6e8d5-646">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-646">Default Value</span></span> | <span data-ttu-id="6e8d5-647">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-647">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="6e8d5-648">32 KB</span><span class="sxs-lookup"><span data-stu-id="6e8d5-648">32 KB</span></span> | <span data-ttu-id="6e8d5-649">Maximální počet bajtů přijatých od klienta, které jsou ukládány do vyrovnávací paměti serveru před použitím protitlaku.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-649">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="6e8d5-650">Zvýšením této hodnoty umožníte, aby server přijímal větší zprávy rychleji bez použití zatížení, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-650">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="6e8d5-651">Data se `Authorize` automaticky shromažďují z atributů použitých pro třídu centra.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-651">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="6e8d5-652">Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru</span><span class="sxs-lookup"><span data-stu-id="6e8d5-652">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="6e8d5-653">32 KB</span><span class="sxs-lookup"><span data-stu-id="6e8d5-653">32 KB</span></span> | <span data-ttu-id="6e8d5-654">Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru před pozorováním protitlaku.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-654">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="6e8d5-655">Zvýšením této hodnoty umožníte serveru rychleji ukládat větší zprávy bez nutnosti očekávat zatížení, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-655">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="6e8d5-656">Všechny přenosy jsou povolené.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-656">All Transports are enabled.</span></span> | <span data-ttu-id="6e8d5-657">Bitové příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenos, který může klient použít pro připojení.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-657">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="6e8d5-658">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-658">See below.</span></span> | <span data-ttu-id="6e8d5-659">Další možnosti specifické pro přenos dlouhého cyklického dotazování.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-659">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="6e8d5-660">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-660">See below.</span></span> | <span data-ttu-id="6e8d5-661">Další možnosti specifické pro přenos pomocí protokolu WebSockets</span><span class="sxs-lookup"><span data-stu-id="6e8d5-661">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="6e8d5-662">Přenos dlouhého cyklického dotazování má další možnosti, které je možné `LongPolling` konfigurovat pomocí vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-662">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="6e8d5-663">Možnost</span><span class="sxs-lookup"><span data-stu-id="6e8d5-663">Option</span></span> | <span data-ttu-id="6e8d5-664">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-664">Default Value</span></span> | <span data-ttu-id="6e8d5-665">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-665">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="6e8d5-666">90 sekund</span><span class="sxs-lookup"><span data-stu-id="6e8d5-666">90 seconds</span></span> | <span data-ttu-id="6e8d5-667">Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jedné žádosti o cyklické dotazování.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-667">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="6e8d5-668">Snížení této hodnoty způsobí, že klient bude vydávat nové požadavky na dotaz častěji.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-668">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="6e8d5-669">Přenos protokolu WebSocket má další možnosti, které je možné konfigurovat pomocí `WebSockets` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-669">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="6e8d5-670">Možnost</span><span class="sxs-lookup"><span data-stu-id="6e8d5-670">Option</span></span> | <span data-ttu-id="6e8d5-671">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-671">Default Value</span></span> | <span data-ttu-id="6e8d5-672">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-672">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="6e8d5-673">5 sekund</span><span class="sxs-lookup"><span data-stu-id="6e8d5-673">5 seconds</span></span> | <span data-ttu-id="6e8d5-674">Pokud se po ukončení serveru aplikace v tomto časovém intervalu nepovede zavřít, připojení se ukončí.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-674">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="6e8d5-675">Delegát, který lze použít k nastavení `Sec-WebSocket-Protocol` záhlaví na vlastní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-675">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="6e8d5-676">Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že se vrátí požadovaná hodnota.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-676">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="6e8d5-677">Konfigurace možností klienta</span><span class="sxs-lookup"><span data-stu-id="6e8d5-677">Configure client options</span></span>

<span data-ttu-id="6e8d5-678">Možnosti klienta lze nakonfigurovat pro `HubConnectionBuilder` typ (k dispozici v klientech rozhraní .NET a v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-678">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="6e8d5-679">Je také k dispozici v klientovi Java, ale `HttpHubConnectionBuilder` podtřídou je to, co obsahuje možnosti konfigurace tvůrce a také na `HubConnection` sebe sama.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-679">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="6e8d5-680">Konfigurovat protokolování</span><span class="sxs-lookup"><span data-stu-id="6e8d5-680">Configure logging</span></span>

<span data-ttu-id="6e8d5-681">Protokolování je konfigurováno v klientovi .NET pomocí `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-681">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="6e8d5-682">Zprostředkovatelé protokolování a filtry je možné zaregistrovat stejným způsobem jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-682">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="6e8d5-683">Další informace najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="6e8d5-683">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="6e8d5-684">Aby bylo možné registrovat poskytovatele protokolování, je nutné nainstalovat potřebné balíčky.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-684">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="6e8d5-685">Úplný seznam najdete v části [předdefinovaná zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-685">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="6e8d5-686">Chcete-li například povolit protokolování konzoly, nainstalujte balíček `Microsoft.Extensions.Logging.Console` NuGet.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-686">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="6e8d5-687">Zavolejte metodu `AddConsole` rozšíření:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-687">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="6e8d5-688">V klientovi jazyka JavaScript existuje podobná `configureLogging` metoda.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-688">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="6e8d5-689">Zadejte `LogLevel` hodnotu, která určuje minimální úroveň zpráv protokolu, které mají být vyprodukovány.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-689">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="6e8d5-690">Protokoly se zapisují do okna konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-690">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="6e8d5-691">Místo `LogLevel` hodnoty můžete zadat také `string` hodnotu představující název úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-691">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="6e8d5-692">To je užitečné při konfiguraci protokolování signálu v prostředích, kde nemáte přístup k `LogLevel` konstantám.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-692">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="6e8d5-693">V následující tabulce jsou uvedeny dostupné úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-693">The following table lists the available log levels.</span></span> <span data-ttu-id="6e8d5-694">Hodnota, kterou zadáte `configureLogging` , nastaví **minimální** úroveň protokolu, která se bude protokolovat.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-694">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="6e8d5-695">Zprávy zaznamenané na této úrovni **nebo úrovně uvedené za ní v tabulce**budou protokolovány.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-695">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="6e8d5-696">Řetězec</span><span class="sxs-lookup"><span data-stu-id="6e8d5-696">String</span></span>                      | <span data-ttu-id="6e8d5-697">LogLevel</span><span class="sxs-lookup"><span data-stu-id="6e8d5-697">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="6e8d5-698">`info`**nebo**`information`</span><span class="sxs-lookup"><span data-stu-id="6e8d5-698">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="6e8d5-699">`warn`**nebo**`warning`</span><span class="sxs-lookup"><span data-stu-id="6e8d5-699">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="6e8d5-700">Chcete-li protokolování zcela zakázat `signalR.LogLevel.None` , zadejte `configureLogging` v metodě.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-700">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="6e8d5-701">Další informace o protokolování najdete v [dokumentaci k nástroji pro diagnostiku signálu](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-701">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="6e8d5-702">Klient Java Signal používá knihovnu [SLF4J](https://www.slf4j.org/) k protokolování.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-702">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="6e8d5-703">Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-703">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="6e8d5-704">Následující fragment kódu ukazuje, jak používat `java.util.logging` s klientem nástroje pro signalizaci v jazyce Java.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-704">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="6e8d5-705">Pokud ve svých závislostech nenakonfigurujete protokolování, SLF4J načte výchozí protokolovací nástroj No-operation s následující zprávou upozornění:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-705">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="6e8d5-706">Tuto možnost lze bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-706">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="6e8d5-707">Konfigurace povolených přenosů</span><span class="sxs-lookup"><span data-stu-id="6e8d5-707">Configure allowed transports</span></span>

<span data-ttu-id="6e8d5-708">Přenosy používané signálem může být nakonfigurována při `WithUrl` volání (`withUrl` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-708">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="6e8d5-709">Bitové nebo hodnoty `HttpTransportType` lze použít k omezení klienta na používání pouze zadaných přenosů.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-709">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="6e8d5-710">Ve výchozím nastavení jsou povolené všechny přenosy.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-710">All transports are enabled by default.</span></span>

<span data-ttu-id="6e8d5-711">Pokud třeba chcete zakázat přenos událostí odeslaných serverem, ale povolit WebSockets a dlouhá připojení s dotazem:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-711">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="6e8d5-712">V klientu jazyka JavaScript jsou přenosy konfigurovány nastavením `transport` pole v objektu Options, který je k dispozici `withUrl`pro:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-712">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="6e8d5-713">V této verzi je jediným dostupným přenosem klientský WebSocket v jazyce Java.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-713">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="6e8d5-714">V klientovi Java se přenos vybere s `withTransport` metodou na. `HttpHubConnectionBuilder`</span><span class="sxs-lookup"><span data-stu-id="6e8d5-714">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="6e8d5-715">Klient Java standardně používá přenos pomocí protokolu WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-715">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="6e8d5-716">Klient Java Signale ještě nepodporuje záložní přenos.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-716">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="6e8d5-717">Konfigurace ověřování nosiče</span><span class="sxs-lookup"><span data-stu-id="6e8d5-717">Configure bearer authentication</span></span>

<span data-ttu-id="6e8d5-718">Chcete-li poskytnout ověřovací data společně s požadavky na signalizaci `AccessTokenProvider` , použijte`accessTokenFactory` možnost (v jazyce JavaScript) k určení funkce, která vrací požadovaný přístupový token.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-718">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="6e8d5-719">V klientovi .NET se tento přístupový token předává jako token ověřování HTTP "Bearer" (s použitím `Authorization` hlavičky s typem `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-719">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="6e8d5-720">V klientu jazyka JavaScript se přístupový token používá jako nosný token, **s výjimkou** případů, kdy rozhraní API prohlížeče omezuje možnost použít hlavičky (konkrétně v požadavcích na události odeslané serverem a objekty WebSockets).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-720">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="6e8d5-721">V těchto případech je přístupový token k dispozici jako hodnota `access_token`řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-721">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="6e8d5-722">V klientu .NET lze `AccessTokenProvider` možnost zadat pomocí delegáta možnosti v: `WithUrl`</span><span class="sxs-lookup"><span data-stu-id="6e8d5-722">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="6e8d5-723">V klientovi JavaScriptu je přístupový token nakonfigurovaný nastavením `accessTokenFactory` pole v objektu Options v: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="6e8d5-723">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="6e8d5-724">V klientovi pro signalizaci Java můžete nakonfigurovat nosný token, který se použije pro ověřování, a to tak, že do [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)poskytne továrnu přístupového tokenu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-724">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="6e8d5-725">Použijte [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) k poskytnutí [>jednoho\<řetězce ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava) .</span><span class="sxs-lookup"><span data-stu-id="6e8d5-725">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="6e8d5-726">Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-726">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="6e8d5-727">Konfigurace možností timeout a Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="6e8d5-727">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="6e8d5-728">Další možnosti konfigurace časového limitu a chování funkce Keep-Alive jsou k `HubConnection` dispozici na samotném objektu:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-728">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="6e8d5-729">.NET</span><span class="sxs-lookup"><span data-stu-id="6e8d5-729">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6e8d5-730">Možnost</span><span class="sxs-lookup"><span data-stu-id="6e8d5-730">Option</span></span> | <span data-ttu-id="6e8d5-731">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-731">Default value</span></span> | <span data-ttu-id="6e8d5-732">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-732">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="6e8d5-733">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="6e8d5-733">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6e8d5-734">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-734">Timeout for server activity.</span></span> <span data-ttu-id="6e8d5-735">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí `Closed` událost (`onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-735">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6e8d5-736">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-736">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6e8d5-737">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-737">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="6e8d5-738">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e8d5-738">15 seconds</span></span> | <span data-ttu-id="6e8d5-739">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-739">Timeout for initial server handshake.</span></span> <span data-ttu-id="6e8d5-740">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a spustí `Closed` událost (`onclose` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-740">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6e8d5-741">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-741">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6e8d5-742">Další informace o procesu handshake najdete v tématu [specifikace protokolu centra signalizace](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-742">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="6e8d5-743">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e8d5-743">15 seconds</span></span> | <span data-ttu-id="6e8d5-744">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-744">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6e8d5-745">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-745">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6e8d5-746">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-746">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="6e8d5-747">V klientovi .NET jsou hodnoty časového limitu zadány `TimeSpan` jako hodnoty.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-747">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="6e8d5-748">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6e8d5-748">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6e8d5-749">Možnost</span><span class="sxs-lookup"><span data-stu-id="6e8d5-749">Option</span></span> | <span data-ttu-id="6e8d5-750">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-750">Default value</span></span> | <span data-ttu-id="6e8d5-751">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-751">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="6e8d5-752">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="6e8d5-752">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6e8d5-753">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-753">Timeout for server activity.</span></span> <span data-ttu-id="6e8d5-754">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onclose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-754">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="6e8d5-755">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-755">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6e8d5-756">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-756">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="6e8d5-757">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="6e8d5-757">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="6e8d5-758">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-758">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6e8d5-759">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-759">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6e8d5-760">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-760">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="6e8d5-761">Java</span><span class="sxs-lookup"><span data-stu-id="6e8d5-761">Java</span></span>](#tab/java)

| <span data-ttu-id="6e8d5-762">Možnost</span><span class="sxs-lookup"><span data-stu-id="6e8d5-762">Option</span></span> | <span data-ttu-id="6e8d5-763">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-763">Default value</span></span> | <span data-ttu-id="6e8d5-764">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-764">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="6e8d5-765">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="6e8d5-765">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="6e8d5-766">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="6e8d5-766">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6e8d5-767">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-767">Timeout for server activity.</span></span> <span data-ttu-id="6e8d5-768">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onClose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-768">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="6e8d5-769">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-769">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6e8d5-770">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-770">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="6e8d5-771">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e8d5-771">15 seconds</span></span> | <span data-ttu-id="6e8d5-772">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-772">Timeout for initial server handshake.</span></span> <span data-ttu-id="6e8d5-773">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje `onClose` událost.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-773">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="6e8d5-774">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-774">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6e8d5-775">Další informace o procesu handshake najdete v tématu [specifikace protokolu centra signalizace](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-775">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="6e8d5-776">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="6e8d5-776">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="6e8d5-777">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="6e8d5-777">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="6e8d5-778">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-778">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6e8d5-779">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-779">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6e8d5-780">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-780">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="6e8d5-781">Konfigurace dalších možností</span><span class="sxs-lookup"><span data-stu-id="6e8d5-781">Configure additional options</span></span>

<span data-ttu-id="6e8d5-782">Další možnosti lze konfigurovat v `WithUrl` metodě (`withUrl` v jazyce JavaScript) na `HubConnectionBuilder` různých konfiguračních rozhraních API `HttpHubConnectionBuilder` v klientovi Java:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-782">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="6e8d5-783">.NET</span><span class="sxs-lookup"><span data-stu-id="6e8d5-783">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6e8d5-784">Možnost .NET</span><span class="sxs-lookup"><span data-stu-id="6e8d5-784">.NET Option</span></span> |  <span data-ttu-id="6e8d5-785">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-785">Default value</span></span> | <span data-ttu-id="6e8d5-786">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-786">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="6e8d5-787">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-787">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="6e8d5-788">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-788">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6e8d5-789">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-789">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6e8d5-790">Toto nastavení se nedá povolit při použití služby signalizace Azure.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-790">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="6e8d5-791">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="6e8d5-791">Empty</span></span> | <span data-ttu-id="6e8d5-792">Kolekce certifikátů TLS pro odeslání na požadavky na ověření.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-792">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="6e8d5-793">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="6e8d5-793">Empty</span></span> | <span data-ttu-id="6e8d5-794">Kolekce souborů cookie protokolu HTTP, které se mají odeslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="6e8d5-794">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="6e8d5-795">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="6e8d5-795">Empty</span></span> | <span data-ttu-id="6e8d5-796">Přihlašovací údaje, které se mají poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="6e8d5-796">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="6e8d5-797">5 sekund</span><span class="sxs-lookup"><span data-stu-id="6e8d5-797">5 seconds</span></span> | <span data-ttu-id="6e8d5-798">Jenom objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-798">WebSockets only.</span></span> <span data-ttu-id="6e8d5-799">Maximální doba, po jejímž uplynutí bude klient čekat na ukončení žádosti o uzavření.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-799">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="6e8d5-800">Pokud server nepotvrdí zavření v této době, klient se odpojí.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-800">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="6e8d5-801">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="6e8d5-801">Empty</span></span> | <span data-ttu-id="6e8d5-802">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="6e8d5-802">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="6e8d5-803">Delegát, který lze použít ke konfiguraci nebo nahrazení `HttpMessageHandler` používaného k odeslání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-803">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="6e8d5-804">Nepoužívá se pro připojení pomocí protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-804">Not used for WebSocket connections.</span></span> <span data-ttu-id="6e8d5-805">Tento delegát musí vracet hodnotu, která není null, a obdrží výchozí hodnotu jako parametr.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-805">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="6e8d5-806">Buď změňte nastavení u této výchozí hodnoty a vraťte je, nebo vraťte novou `HttpMessageHandler` instanci.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-806">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="6e8d5-807">**Při nahrazování obslužné rutiny Nezapomeňte zkopírovat nastavení, která chcete zachovat, od poskytnuté obslužné rutiny, jinak konfigurované možnosti (například soubory cookie a záhlaví) nebudou použity pro novou obslužnou rutinu.**</span><span class="sxs-lookup"><span data-stu-id="6e8d5-807">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="6e8d5-808">Proxy server HTTP, který se má použít při odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-808">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="6e8d5-809">Nastavte tuto logickou hodnotu pro odeslání výchozích přihlašovacích údajů pro požadavky HTTP a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-809">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="6e8d5-810">To umožňuje použití ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-810">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="6e8d5-811">Delegát, který lze použít ke konfiguraci dalších možností protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-811">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="6e8d5-812">Přijímá instanci [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , která se dá použít ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-812">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="6e8d5-813">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6e8d5-813">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6e8d5-814">Možnost JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="6e8d5-814">JavaScript Option</span></span> | <span data-ttu-id="6e8d5-815">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-815">Default Value</span></span> | <span data-ttu-id="6e8d5-816">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-816">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="6e8d5-817">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-817">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="6e8d5-818">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-818">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6e8d5-819">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-819">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6e8d5-820">Toto nastavení se nedá povolit při použití služby signalizace Azure.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-820">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="6e8d5-821">Java</span><span class="sxs-lookup"><span data-stu-id="6e8d5-821">Java</span></span>](#tab/java)

| <span data-ttu-id="6e8d5-822">Možnost jazyka Java</span><span class="sxs-lookup"><span data-stu-id="6e8d5-822">Java Option</span></span> | <span data-ttu-id="6e8d5-823">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-823">Default Value</span></span> | <span data-ttu-id="6e8d5-824">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-824">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="6e8d5-825">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-825">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="6e8d5-826">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-826">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6e8d5-827">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-827">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6e8d5-828">Toto nastavení se nedá povolit při použití služby signalizace Azure.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-828">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="6e8d5-829">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="6e8d5-829">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="6e8d5-830">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="6e8d5-830">Empty</span></span> | <span data-ttu-id="6e8d5-831">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="6e8d5-831">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="6e8d5-832">V klientu .NET můžete tyto možnosti upravit pomocí delegáta možností, který je k `WithUrl`dispozici pro:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-832">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="6e8d5-833">V klientovi JavaScriptu lze tyto možnosti poskytnout v objektu jazyka JavaScript, který je k dispozici pro `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-833">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="6e8d5-834">V klientovi Java lze tyto možnosti nakonfigurovat s metodami `HttpHubConnectionBuilder` vrácenými z`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="6e8d5-834">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="6e8d5-835">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="6e8d5-835">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="6e8d5-836">Možnosti serializace JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="6e8d5-836">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="6e8d5-837">ASP.NET Core Signal podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-837">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="6e8d5-838">Každý protokol má možnosti konfigurace serializace.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-838">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="6e8d5-839">Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , kterou lze přidat po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v `Startup.ConfigureServices` metodě.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-839">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="6e8d5-840">`AddJsonProtocol` Metoda přebírá delegáta, který přijímá `options` objekt.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-840">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="6e8d5-841">Vlastnost [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) tohoto objektu je objekt JSON.NET `JsonSerializerSettings` , který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-841">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="6e8d5-842">Další informace najdete v [dokumentaci k JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-842">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="6e8d5-843">Chcete-li například nakonfigurovat serializátor pro použití názvů vlastností "PascalCase" namísto výchozích názvů "camelCase", použijte následující kód v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-843">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="6e8d5-844">V klientovi .NET existuje stejná `AddJsonProtocol` rozšiřující metoda v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-844">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="6e8d5-845">`Microsoft.Extensions.DependencyInjection` Obor názvů musí být importován, aby bylo možné přeložit metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-845">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="6e8d5-846">V tuto chvíli není možné konfigurovat serializaci JSON v klientovi jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-846">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="6e8d5-847">Možnosti serializace MessagePack</span><span class="sxs-lookup"><span data-stu-id="6e8d5-847">MessagePack serialization options</span></span>

<span data-ttu-id="6e8d5-848">Serializaci MessagePack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6e8d5-848">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="6e8d5-849">Další podrobnosti najdete [v tématu MessagePack v nástroji Signal](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6e8d5-849">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="6e8d5-850">V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi v jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-850">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="6e8d5-851">Konfigurovat možnosti serveru</span><span class="sxs-lookup"><span data-stu-id="6e8d5-851">Configure server options</span></span>

<span data-ttu-id="6e8d5-852">Následující tabulka obsahuje popis možností konfigurace Center pro signály:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-852">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="6e8d5-853">Možnost</span><span class="sxs-lookup"><span data-stu-id="6e8d5-853">Option</span></span> | <span data-ttu-id="6e8d5-854">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-854">Default Value</span></span> | <span data-ttu-id="6e8d5-855">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-855">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="6e8d5-856">30 sekund</span><span class="sxs-lookup"><span data-stu-id="6e8d5-856">30 seconds</span></span> | <span data-ttu-id="6e8d5-857">Server bude předpokládat, že je klient odpojený, pokud v tomto intervalu neobdržel zprávu (včetně Keep-Alive).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-857">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="6e8d5-858">Může trvat delší dobu, než je tento časový limit, aby bylo možné klienta ve skutečnosti označit jako odpojený, protože to je implementováno.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-858">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="6e8d5-859">Doporučená hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-859">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="6e8d5-860">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e8d5-860">15 seconds</span></span> | <span data-ttu-id="6e8d5-861">Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-861">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="6e8d5-862">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-862">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6e8d5-863">Další informace o procesu handshake najdete v tématu [specifikace protokolu centra signalizace](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-863">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="6e8d5-864">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e8d5-864">15 seconds</span></span> | <span data-ttu-id="6e8d5-865">Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-865">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="6e8d5-866">Při změně `KeepAliveInterval` `serverTimeoutInMilliseconds` nastavení změňte `ServerTimeout` / nastavení v klientovi.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-866">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="6e8d5-867">`ServerTimeout` / Doporučená `serverTimeoutInMilliseconds` hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-867">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="6e8d5-868">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="6e8d5-868">All installed protocols</span></span> | <span data-ttu-id="6e8d5-869">Protokoly podporované tímto rozbočovačem</span><span class="sxs-lookup"><span data-stu-id="6e8d5-869">Protocols supported by this hub.</span></span> <span data-ttu-id="6e8d5-870">Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-870">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="6e8d5-871">Pokud `true`se do klientů vrátí podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-871">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="6e8d5-872">Výchozí hodnota je `false`, protože tyto zprávy o výjimce můžou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-872">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="6e8d5-873">Možnosti lze nakonfigurovat pro všechna centra tím, že poskytnete možnosti delegáta `AddSignalR` pro volání `Startup.ConfigureServices`v.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-873">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="6e8d5-874">Možnosti pro jeden rozbočovač přepíší globální možnosti poskytované v `AddSignalR` a můžou být nakonfigurované pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-874">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="6e8d5-875">Rozšířené možnosti konfigurace protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="6e8d5-875">Advanced HTTP configuration options</span></span>

<span data-ttu-id="6e8d5-876">Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-876">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="6e8d5-877">Tyto možnosti jsou nakonfigurovány předáním delegáta [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-877">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="6e8d5-878">Následující tabulka popisuje možnosti konfigurace upřesňujících možností protokolu HTTP pro ASP.NET Core signalizaci:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-878">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="6e8d5-879">Možnost</span><span class="sxs-lookup"><span data-stu-id="6e8d5-879">Option</span></span> | <span data-ttu-id="6e8d5-880">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-880">Default Value</span></span> | <span data-ttu-id="6e8d5-881">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-881">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="6e8d5-882">32 KB</span><span class="sxs-lookup"><span data-stu-id="6e8d5-882">32 KB</span></span> | <span data-ttu-id="6e8d5-883">Maximální počet bajtů přijatých od klienta, které jsou vyrovnávací paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-883">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="6e8d5-884">Zvýšením této hodnoty umožníte serveru přijímat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-884">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="6e8d5-885">Data se `Authorize` automaticky shromažďují z atributů použitých pro třídu centra.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-885">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="6e8d5-886">Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru</span><span class="sxs-lookup"><span data-stu-id="6e8d5-886">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="6e8d5-887">32 KB</span><span class="sxs-lookup"><span data-stu-id="6e8d5-887">32 KB</span></span> | <span data-ttu-id="6e8d5-888">Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-888">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="6e8d5-889">Zvýšením této hodnoty umožníte serveru odesílat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-889">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="6e8d5-890">Všechny přenosy jsou povolené.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-890">All Transports are enabled.</span></span> | <span data-ttu-id="6e8d5-891">Bitové příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenos, který může klient použít pro připojení.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-891">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="6e8d5-892">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-892">See below.</span></span> | <span data-ttu-id="6e8d5-893">Další možnosti specifické pro přenos dlouhého cyklického dotazování.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-893">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="6e8d5-894">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-894">See below.</span></span> | <span data-ttu-id="6e8d5-895">Další možnosti specifické pro přenos pomocí protokolu WebSockets</span><span class="sxs-lookup"><span data-stu-id="6e8d5-895">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="6e8d5-896">Přenos dlouhého cyklického dotazování má další možnosti, které je možné `LongPolling` konfigurovat pomocí vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-896">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="6e8d5-897">Možnost</span><span class="sxs-lookup"><span data-stu-id="6e8d5-897">Option</span></span> | <span data-ttu-id="6e8d5-898">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-898">Default Value</span></span> | <span data-ttu-id="6e8d5-899">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-899">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="6e8d5-900">90 sekund</span><span class="sxs-lookup"><span data-stu-id="6e8d5-900">90 seconds</span></span> | <span data-ttu-id="6e8d5-901">Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jedné žádosti o cyklické dotazování.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-901">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="6e8d5-902">Snížení této hodnoty způsobí, že klient bude vydávat nové požadavky na dotaz častěji.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-902">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="6e8d5-903">Přenos protokolu WebSocket má další možnosti, které je možné konfigurovat pomocí `WebSockets` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-903">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="6e8d5-904">Možnost</span><span class="sxs-lookup"><span data-stu-id="6e8d5-904">Option</span></span> | <span data-ttu-id="6e8d5-905">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-905">Default Value</span></span> | <span data-ttu-id="6e8d5-906">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-906">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="6e8d5-907">5 sekund</span><span class="sxs-lookup"><span data-stu-id="6e8d5-907">5 seconds</span></span> | <span data-ttu-id="6e8d5-908">Pokud se po ukončení serveru aplikace v tomto časovém intervalu nepovede zavřít, připojení se ukončí.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-908">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="6e8d5-909">Delegát, který lze použít k nastavení `Sec-WebSocket-Protocol` záhlaví na vlastní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-909">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="6e8d5-910">Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že se vrátí požadovaná hodnota.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-910">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="6e8d5-911">Konfigurace možností klienta</span><span class="sxs-lookup"><span data-stu-id="6e8d5-911">Configure client options</span></span>

<span data-ttu-id="6e8d5-912">Možnosti klienta lze nakonfigurovat pro `HubConnectionBuilder` typ (k dispozici v klientech rozhraní .NET a v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-912">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="6e8d5-913">Je také k dispozici v klientovi Java, ale `HttpHubConnectionBuilder` podtřídou je to, co obsahuje možnosti konfigurace tvůrce a také na `HubConnection` sebe sama.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-913">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="6e8d5-914">Konfigurovat protokolování</span><span class="sxs-lookup"><span data-stu-id="6e8d5-914">Configure logging</span></span>

<span data-ttu-id="6e8d5-915">Protokolování je konfigurováno v klientovi .NET pomocí `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-915">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="6e8d5-916">Zprostředkovatelé protokolování a filtry je možné zaregistrovat stejným způsobem jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-916">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="6e8d5-917">Další informace najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="6e8d5-917">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="6e8d5-918">Aby bylo možné registrovat poskytovatele protokolování, je nutné nainstalovat potřebné balíčky.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-918">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="6e8d5-919">Úplný seznam najdete v části [předdefinovaná zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-919">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="6e8d5-920">Chcete-li například povolit protokolování konzoly, nainstalujte balíček `Microsoft.Extensions.Logging.Console` NuGet.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-920">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="6e8d5-921">Zavolejte metodu `AddConsole` rozšíření:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-921">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="6e8d5-922">V klientovi jazyka JavaScript existuje podobná `configureLogging` metoda.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-922">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="6e8d5-923">Zadejte `LogLevel` hodnotu, která určuje minimální úroveň zpráv protokolu, které mají být vyprodukovány.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-923">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="6e8d5-924">Protokoly se zapisují do okna konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-924">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="6e8d5-925">Chcete-li protokolování zcela zakázat `signalR.LogLevel.None` , zadejte `configureLogging` v metodě.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-925">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="6e8d5-926">Další informace o protokolování najdete v [dokumentaci k nástroji pro diagnostiku signálu](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-926">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="6e8d5-927">Klient Java Signal používá knihovnu [SLF4J](https://www.slf4j.org/) k protokolování.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-927">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="6e8d5-928">Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-928">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="6e8d5-929">Následující fragment kódu ukazuje, jak používat `java.util.logging` s klientem nástroje pro signalizaci v jazyce Java.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-929">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="6e8d5-930">Pokud ve svých závislostech nenakonfigurujete protokolování, SLF4J načte výchozí protokolovací nástroj No-operation s následující zprávou upozornění:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-930">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="6e8d5-931">Tuto možnost lze bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-931">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="6e8d5-932">Konfigurace povolených přenosů</span><span class="sxs-lookup"><span data-stu-id="6e8d5-932">Configure allowed transports</span></span>

<span data-ttu-id="6e8d5-933">Přenosy používané signálem může být nakonfigurována při `WithUrl` volání (`withUrl` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-933">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="6e8d5-934">Bitové nebo hodnoty `HttpTransportType` lze použít k omezení klienta na používání pouze zadaných přenosů.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-934">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="6e8d5-935">Ve výchozím nastavení jsou povolené všechny přenosy.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-935">All transports are enabled by default.</span></span>

<span data-ttu-id="6e8d5-936">Pokud třeba chcete zakázat přenos událostí odeslaných serverem, ale povolit WebSockets a dlouhá připojení s dotazem:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-936">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="6e8d5-937">V klientu jazyka JavaScript jsou přenosy konfigurovány nastavením `transport` pole v objektu Options, který je k dispozici `withUrl`pro:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-937">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="6e8d5-938">V této verzi je jediným dostupným přenosem klientský WebSocket v jazyce Java.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-938">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="6e8d5-939">Konfigurace ověřování nosiče</span><span class="sxs-lookup"><span data-stu-id="6e8d5-939">Configure bearer authentication</span></span>

<span data-ttu-id="6e8d5-940">Chcete-li poskytnout ověřovací data společně s požadavky na signalizaci `AccessTokenProvider` , použijte`accessTokenFactory` možnost (v jazyce JavaScript) k určení funkce, která vrací požadovaný přístupový token.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-940">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="6e8d5-941">V klientovi .NET se tento přístupový token předává jako token ověřování HTTP "Bearer" (s použitím `Authorization` hlavičky s typem `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-941">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="6e8d5-942">V klientu jazyka JavaScript se přístupový token používá jako nosný token, **s výjimkou** případů, kdy rozhraní API prohlížeče omezuje možnost použít hlavičky (konkrétně v požadavcích na události odeslané serverem a objekty WebSockets).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-942">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="6e8d5-943">V těchto případech je přístupový token k dispozici jako hodnota `access_token`řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-943">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="6e8d5-944">V klientu .NET lze `AccessTokenProvider` možnost zadat pomocí delegáta možnosti v: `WithUrl`</span><span class="sxs-lookup"><span data-stu-id="6e8d5-944">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="6e8d5-945">V klientovi JavaScriptu je přístupový token nakonfigurovaný nastavením `accessTokenFactory` pole v objektu Options v: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="6e8d5-945">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="6e8d5-946">V klientovi pro signalizaci Java můžete nakonfigurovat nosný token, který se použije pro ověřování, a to tak, že do [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)poskytne továrnu přístupového tokenu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-946">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="6e8d5-947">Použijte [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) k poskytnutí [>jednoho\<řetězce ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava) .</span><span class="sxs-lookup"><span data-stu-id="6e8d5-947">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="6e8d5-948">Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-948">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="6e8d5-949">Konfigurace možností timeout a Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="6e8d5-949">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="6e8d5-950">Další možnosti konfigurace časového limitu a chování funkce Keep-Alive jsou k `HubConnection` dispozici na samotném objektu:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-950">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="6e8d5-951">.NET</span><span class="sxs-lookup"><span data-stu-id="6e8d5-951">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6e8d5-952">Možnost</span><span class="sxs-lookup"><span data-stu-id="6e8d5-952">Option</span></span> | <span data-ttu-id="6e8d5-953">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-953">Default value</span></span> | <span data-ttu-id="6e8d5-954">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-954">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="6e8d5-955">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="6e8d5-955">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6e8d5-956">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-956">Timeout for server activity.</span></span> <span data-ttu-id="6e8d5-957">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí `Closed` událost (`onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-957">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6e8d5-958">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-958">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6e8d5-959">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-959">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="6e8d5-960">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e8d5-960">15 seconds</span></span> | <span data-ttu-id="6e8d5-961">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-961">Timeout for initial server handshake.</span></span> <span data-ttu-id="6e8d5-962">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a spustí `Closed` událost (`onclose` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-962">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6e8d5-963">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-963">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6e8d5-964">Další informace o procesu handshake najdete v tématu [specifikace protokolu centra signalizace](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-964">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="6e8d5-965">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e8d5-965">15 seconds</span></span> | <span data-ttu-id="6e8d5-966">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-966">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6e8d5-967">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-967">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6e8d5-968">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-968">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="6e8d5-969">V klientovi .NET jsou hodnoty časového limitu zadány `TimeSpan` jako hodnoty.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-969">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="6e8d5-970">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6e8d5-970">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6e8d5-971">Možnost</span><span class="sxs-lookup"><span data-stu-id="6e8d5-971">Option</span></span> | <span data-ttu-id="6e8d5-972">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-972">Default value</span></span> | <span data-ttu-id="6e8d5-973">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-973">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="6e8d5-974">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="6e8d5-974">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6e8d5-975">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-975">Timeout for server activity.</span></span> <span data-ttu-id="6e8d5-976">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onclose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-976">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="6e8d5-977">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-977">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6e8d5-978">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-978">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="6e8d5-979">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="6e8d5-979">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="6e8d5-980">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-980">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6e8d5-981">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-981">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6e8d5-982">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-982">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="6e8d5-983">Java</span><span class="sxs-lookup"><span data-stu-id="6e8d5-983">Java</span></span>](#tab/java)

| <span data-ttu-id="6e8d5-984">Možnost</span><span class="sxs-lookup"><span data-stu-id="6e8d5-984">Option</span></span> | <span data-ttu-id="6e8d5-985">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-985">Default value</span></span> | <span data-ttu-id="6e8d5-986">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-986">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="6e8d5-987">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="6e8d5-987">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="6e8d5-988">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="6e8d5-988">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6e8d5-989">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-989">Timeout for server activity.</span></span> <span data-ttu-id="6e8d5-990">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onClose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-990">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="6e8d5-991">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-991">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6e8d5-992">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-992">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="6e8d5-993">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e8d5-993">15 seconds</span></span> | <span data-ttu-id="6e8d5-994">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-994">Timeout for initial server handshake.</span></span> <span data-ttu-id="6e8d5-995">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje `onClose` událost.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-995">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="6e8d5-996">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-996">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6e8d5-997">Další informace o procesu handshake najdete v tématu [specifikace protokolu centra signalizace](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-997">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="6e8d5-998">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="6e8d5-998">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="6e8d5-999">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="6e8d5-999">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="6e8d5-1000">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1000">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="6e8d5-1001">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1001">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="6e8d5-1002">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1002">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="6e8d5-1003">Konfigurace dalších možností</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1003">Configure additional options</span></span>

<span data-ttu-id="6e8d5-1004">Další možnosti lze konfigurovat v `WithUrl` metodě (`withUrl` v jazyce JavaScript) na `HubConnectionBuilder` různých konfiguračních rozhraních API `HttpHubConnectionBuilder` v klientovi Java:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1004">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="6e8d5-1005">.NET</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1005">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6e8d5-1006">Možnost .NET</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1006">.NET Option</span></span> |  <span data-ttu-id="6e8d5-1007">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1007">Default value</span></span> | <span data-ttu-id="6e8d5-1008">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1008">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="6e8d5-1009">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1009">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="6e8d5-1010">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1010">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6e8d5-1011">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1011">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6e8d5-1012">Toto nastavení se nedá povolit při použití služby signalizace Azure.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1012">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="6e8d5-1013">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1013">Empty</span></span> | <span data-ttu-id="6e8d5-1014">Kolekce certifikátů TLS pro odeslání na požadavky na ověření.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1014">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="6e8d5-1015">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1015">Empty</span></span> | <span data-ttu-id="6e8d5-1016">Kolekce souborů cookie protokolu HTTP, které se mají odeslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1016">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="6e8d5-1017">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1017">Empty</span></span> | <span data-ttu-id="6e8d5-1018">Přihlašovací údaje, které se mají poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1018">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="6e8d5-1019">5 sekund</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1019">5 seconds</span></span> | <span data-ttu-id="6e8d5-1020">Jenom objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1020">WebSockets only.</span></span> <span data-ttu-id="6e8d5-1021">Maximální doba, po jejímž uplynutí bude klient čekat na ukončení žádosti o uzavření.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1021">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="6e8d5-1022">Pokud server nepotvrdí zavření v této době, klient se odpojí.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1022">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="6e8d5-1023">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1023">Empty</span></span> | <span data-ttu-id="6e8d5-1024">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1024">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="6e8d5-1025">Delegát, který lze použít ke konfiguraci nebo nahrazení `HttpMessageHandler` používaného k odeslání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1025">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="6e8d5-1026">Nepoužívá se pro připojení pomocí protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1026">Not used for WebSocket connections.</span></span> <span data-ttu-id="6e8d5-1027">Tento delegát musí vracet hodnotu, která není null, a obdrží výchozí hodnotu jako parametr.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1027">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="6e8d5-1028">Buď změňte nastavení u této výchozí hodnoty a vraťte je, nebo vraťte novou `HttpMessageHandler` instanci.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1028">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="6e8d5-1029">**Při nahrazování obslužné rutiny Nezapomeňte zkopírovat nastavení, která chcete zachovat, od poskytnuté obslužné rutiny, jinak konfigurované možnosti (například soubory cookie a záhlaví) nebudou použity pro novou obslužnou rutinu.**</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1029">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="6e8d5-1030">Proxy server HTTP, který se má použít při odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1030">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="6e8d5-1031">Nastavte tuto logickou hodnotu pro odeslání výchozích přihlašovacích údajů pro požadavky HTTP a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1031">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="6e8d5-1032">To umožňuje použití ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1032">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="6e8d5-1033">Delegát, který lze použít ke konfiguraci dalších možností protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1033">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="6e8d5-1034">Přijímá instanci [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , která se dá použít ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1034">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="6e8d5-1035">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1035">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6e8d5-1036">Možnost JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1036">JavaScript Option</span></span> | <span data-ttu-id="6e8d5-1037">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1037">Default Value</span></span> | <span data-ttu-id="6e8d5-1038">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1038">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="6e8d5-1039">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1039">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="6e8d5-1040">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1040">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6e8d5-1041">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1041">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6e8d5-1042">Toto nastavení se nedá povolit při použití služby signalizace Azure.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1042">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="6e8d5-1043">Java</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1043">Java</span></span>](#tab/java)

| <span data-ttu-id="6e8d5-1044">Možnost jazyka Java</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1044">Java Option</span></span> | <span data-ttu-id="6e8d5-1045">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1045">Default Value</span></span> | <span data-ttu-id="6e8d5-1046">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1046">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="6e8d5-1047">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1047">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="6e8d5-1048">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1048">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6e8d5-1049">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1049">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6e8d5-1050">Toto nastavení se nedá povolit při použití služby signalizace Azure.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1050">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="6e8d5-1051">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1051">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="6e8d5-1052">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1052">Empty</span></span> | <span data-ttu-id="6e8d5-1053">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1053">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="6e8d5-1054">V klientu .NET můžete tyto možnosti upravit pomocí delegáta možností, který je k `WithUrl`dispozici pro:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1054">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="6e8d5-1055">V klientovi JavaScriptu lze tyto možnosti poskytnout v objektu jazyka JavaScript, který je k dispozici pro `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1055">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="6e8d5-1056">V klientovi Java lze tyto možnosti nakonfigurovat s metodami `HttpHubConnectionBuilder` vrácenými z`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1056">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="6e8d5-1057">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1057">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="6e8d5-1058">Možnosti serializace JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1058">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="6e8d5-1059">ASP.NET Core Signal podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1059">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="6e8d5-1060">Každý protokol má možnosti konfigurace serializace.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1060">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="6e8d5-1061">Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , kterou lze přidat po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v `Startup.ConfigureServices` metodě.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1061">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="6e8d5-1062">`AddJsonProtocol` Metoda přebírá delegáta, který přijímá `options` objekt.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1062">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="6e8d5-1063">Vlastnost [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) tohoto objektu je objekt JSON.NET `JsonSerializerSettings` , který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1063">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="6e8d5-1064">Další informace najdete v [dokumentaci k JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1064">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="6e8d5-1065">Chcete-li například nakonfigurovat serializátor pro použití názvů vlastností "PascalCase" namísto výchozích názvů "camelCase", použijte následující kód v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1065">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="6e8d5-1066">V klientovi .NET existuje stejná `AddJsonProtocol` rozšiřující metoda v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1066">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="6e8d5-1067">`Microsoft.Extensions.DependencyInjection` Obor názvů musí být importován, aby bylo možné přeložit metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1067">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="6e8d5-1068">V tuto chvíli není možné konfigurovat serializaci JSON v klientovi jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1068">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="6e8d5-1069">Možnosti serializace MessagePack</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1069">MessagePack serialization options</span></span>

<span data-ttu-id="6e8d5-1070">Serializaci MessagePack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1070">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="6e8d5-1071">Další podrobnosti najdete [v tématu MessagePack v nástroji Signal](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1071">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="6e8d5-1072">V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi v jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1072">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="6e8d5-1073">Konfigurovat možnosti serveru</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1073">Configure server options</span></span>

<span data-ttu-id="6e8d5-1074">Následující tabulka obsahuje popis možností konfigurace Center pro signály:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1074">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="6e8d5-1075">Možnost</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1075">Option</span></span> | <span data-ttu-id="6e8d5-1076">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1076">Default Value</span></span> | <span data-ttu-id="6e8d5-1077">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1077">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="6e8d5-1078">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1078">15 seconds</span></span> | <span data-ttu-id="6e8d5-1079">Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1079">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="6e8d5-1080">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1080">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6e8d5-1081">Další informace o procesu handshake najdete v tématu [specifikace protokolu centra signalizace](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1081">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="6e8d5-1082">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1082">15 seconds</span></span> | <span data-ttu-id="6e8d5-1083">Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1083">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="6e8d5-1084">Při změně `KeepAliveInterval` `serverTimeoutInMilliseconds` nastavení změňte `ServerTimeout` / nastavení v klientovi.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1084">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="6e8d5-1085">`ServerTimeout` / Doporučená `serverTimeoutInMilliseconds` hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1085">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="6e8d5-1086">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1086">All installed protocols</span></span> | <span data-ttu-id="6e8d5-1087">Protokoly podporované tímto rozbočovačem</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1087">Protocols supported by this hub.</span></span> <span data-ttu-id="6e8d5-1088">Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1088">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="6e8d5-1089">Pokud `true`se do klientů vrátí podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1089">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="6e8d5-1090">Výchozí hodnota je `false`, protože tyto zprávy o výjimce můžou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1090">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="6e8d5-1091">Možnosti lze nakonfigurovat pro všechna centra tím, že poskytnete možnosti delegáta `AddSignalR` pro volání `Startup.ConfigureServices`v.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1091">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="6e8d5-1092">Možnosti pro jeden rozbočovač přepíší globální možnosti poskytované v `AddSignalR` a můžou být nakonfigurované pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1092">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<MyHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="6e8d5-1093">Rozšířené možnosti konfigurace protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1093">Advanced HTTP configuration options</span></span>

<span data-ttu-id="6e8d5-1094">Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1094">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="6e8d5-1095">Tyto možnosti jsou nakonfigurovány předáním delegáta [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) v `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1095">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="6e8d5-1096">Následující tabulka popisuje možnosti konfigurace upřesňujících možností protokolu HTTP pro ASP.NET Core signalizaci:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1096">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="6e8d5-1097">Možnost</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1097">Option</span></span> | <span data-ttu-id="6e8d5-1098">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1098">Default Value</span></span> | <span data-ttu-id="6e8d5-1099">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1099">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="6e8d5-1100">32 KB</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1100">32 KB</span></span> | <span data-ttu-id="6e8d5-1101">Maximální počet bajtů přijatých od klienta, které jsou vyrovnávací paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1101">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="6e8d5-1102">Zvýšením této hodnoty umožníte serveru přijímat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1102">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="6e8d5-1103">Data se `Authorize` automaticky shromažďují z atributů použitých pro třídu centra.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1103">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="6e8d5-1104">Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1104">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="6e8d5-1105">32 KB</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1105">32 KB</span></span> | <span data-ttu-id="6e8d5-1106">Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1106">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="6e8d5-1107">Zvýšením této hodnoty umožníte serveru odesílat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1107">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="6e8d5-1108">Všechny přenosy jsou povolené.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1108">All Transports are enabled.</span></span> | <span data-ttu-id="6e8d5-1109">Bitové příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenos, který může klient použít pro připojení.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1109">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="6e8d5-1110">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1110">See below.</span></span> | <span data-ttu-id="6e8d5-1111">Další možnosti specifické pro přenos dlouhého cyklického dotazování.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1111">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="6e8d5-1112">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1112">See below.</span></span> | <span data-ttu-id="6e8d5-1113">Další možnosti specifické pro přenos pomocí protokolu WebSockets</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1113">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="6e8d5-1114">Přenos dlouhého cyklického dotazování má další možnosti, které je možné `LongPolling` konfigurovat pomocí vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1114">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="6e8d5-1115">Možnost</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1115">Option</span></span> | <span data-ttu-id="6e8d5-1116">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1116">Default Value</span></span> | <span data-ttu-id="6e8d5-1117">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1117">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="6e8d5-1118">90 sekund</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1118">90 seconds</span></span> | <span data-ttu-id="6e8d5-1119">Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jedné žádosti o cyklické dotazování.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1119">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="6e8d5-1120">Snížení této hodnoty způsobí, že klient bude vydávat nové požadavky na dotaz častěji.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1120">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="6e8d5-1121">Přenos protokolu WebSocket má další možnosti, které je možné konfigurovat pomocí `WebSockets` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1121">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="6e8d5-1122">Možnost</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1122">Option</span></span> | <span data-ttu-id="6e8d5-1123">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1123">Default Value</span></span> | <span data-ttu-id="6e8d5-1124">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1124">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="6e8d5-1125">5 sekund</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1125">5 seconds</span></span> | <span data-ttu-id="6e8d5-1126">Pokud se po ukončení serveru aplikace v tomto časovém intervalu nepovede zavřít, připojení se ukončí.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1126">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="6e8d5-1127">Delegát, který lze použít k nastavení `Sec-WebSocket-Protocol` záhlaví na vlastní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1127">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="6e8d5-1128">Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že se vrátí požadovaná hodnota.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1128">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="6e8d5-1129">Konfigurace možností klienta</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1129">Configure client options</span></span>

<span data-ttu-id="6e8d5-1130">Možnosti klienta lze nakonfigurovat pro `HubConnectionBuilder` typ (k dispozici v klientech rozhraní .NET a v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1130">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="6e8d5-1131">Je také k dispozici v klientovi Java, ale `HttpHubConnectionBuilder` podtřídou je to, co obsahuje možnosti konfigurace tvůrce a také na `HubConnection` sebe sama.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1131">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="6e8d5-1132">Konfigurovat protokolování</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1132">Configure logging</span></span>

<span data-ttu-id="6e8d5-1133">Protokolování je konfigurováno v klientovi .NET pomocí `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1133">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="6e8d5-1134">Zprostředkovatelé protokolování a filtry je možné zaregistrovat stejným způsobem jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1134">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="6e8d5-1135">Další informace najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1135">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="6e8d5-1136">Aby bylo možné registrovat poskytovatele protokolování, je nutné nainstalovat potřebné balíčky.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1136">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="6e8d5-1137">Úplný seznam najdete v části [předdefinovaná zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1137">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="6e8d5-1138">Chcete-li například povolit protokolování konzoly, nainstalujte balíček `Microsoft.Extensions.Logging.Console` NuGet.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1138">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="6e8d5-1139">Zavolejte metodu `AddConsole` rozšíření:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1139">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="6e8d5-1140">V klientovi jazyka JavaScript existuje podobná `configureLogging` metoda.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1140">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="6e8d5-1141">Zadejte `LogLevel` hodnotu, která určuje minimální úroveň zpráv protokolu, které mají být vyprodukovány.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1141">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="6e8d5-1142">Protokoly se zapisují do okna konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1142">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="6e8d5-1143">Chcete-li protokolování zcela zakázat `signalR.LogLevel.None` , zadejte `configureLogging` v metodě.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1143">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="6e8d5-1144">Další informace o protokolování najdete v [dokumentaci k nástroji pro diagnostiku signálu](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1144">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="6e8d5-1145">Klient Java Signal používá knihovnu [SLF4J](https://www.slf4j.org/) k protokolování.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1145">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="6e8d5-1146">Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1146">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="6e8d5-1147">Následující fragment kódu ukazuje, jak používat `java.util.logging` s klientem nástroje pro signalizaci v jazyce Java.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1147">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="6e8d5-1148">Pokud ve svých závislostech nenakonfigurujete protokolování, SLF4J načte výchozí protokolovací nástroj No-operation s následující zprávou upozornění:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1148">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="6e8d5-1149">Tuto možnost lze bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1149">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="6e8d5-1150">Konfigurace povolených přenosů</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1150">Configure allowed transports</span></span>

<span data-ttu-id="6e8d5-1151">Přenosy používané signálem může být nakonfigurována při `WithUrl` volání (`withUrl` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1151">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="6e8d5-1152">Bitové nebo hodnoty `HttpTransportType` lze použít k omezení klienta na používání pouze zadaných přenosů.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1152">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="6e8d5-1153">Ve výchozím nastavení jsou povolené všechny přenosy.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1153">All transports are enabled by default.</span></span>

<span data-ttu-id="6e8d5-1154">Pokud třeba chcete zakázat přenos událostí odeslaných serverem, ale povolit WebSockets a dlouhá připojení s dotazem:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1154">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="6e8d5-1155">V klientu jazyka JavaScript jsou přenosy konfigurovány nastavením `transport` pole v objektu Options, který je k dispozici `withUrl`pro:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1155">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="6e8d5-1156">Konfigurace ověřování nosiče</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1156">Configure bearer authentication</span></span>

<span data-ttu-id="6e8d5-1157">Chcete-li poskytnout ověřovací data společně s požadavky na signalizaci `AccessTokenProvider` , použijte`accessTokenFactory` možnost (v jazyce JavaScript) k určení funkce, která vrací požadovaný přístupový token.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1157">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="6e8d5-1158">V klientovi .NET se tento přístupový token předává jako token ověřování HTTP "Bearer" (s použitím `Authorization` hlavičky s typem `Bearer`).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1158">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="6e8d5-1159">V klientu jazyka JavaScript se přístupový token používá jako nosný token, **s výjimkou** případů, kdy rozhraní API prohlížeče omezuje možnost použít hlavičky (konkrétně v požadavcích na události odeslané serverem a objekty WebSockets).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1159">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="6e8d5-1160">V těchto případech je přístupový token k dispozici jako hodnota `access_token`řetězce dotazu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1160">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="6e8d5-1161">V klientu .NET lze `AccessTokenProvider` možnost zadat pomocí delegáta možnosti v: `WithUrl`</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1161">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="6e8d5-1162">V klientovi JavaScriptu je přístupový token nakonfigurovaný nastavením `accessTokenFactory` pole v objektu Options v: `withUrl`</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1162">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="6e8d5-1163">V klientovi pro signalizaci Java můžete nakonfigurovat nosný token, který se použije pro ověřování, a to tak, že do [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java)poskytne továrnu přístupového tokenu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1163">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="6e8d5-1164">Použijte [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) k poskytnutí [>jednoho\<řetězce ](https://reactivex.io/documentation/single.html) [RxJava](https://github.com/ReactiveX/RxJava) .</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1164">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="6e8d5-1165">Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1165">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="6e8d5-1166">Konfigurace možností timeout a Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1166">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="6e8d5-1167">Další možnosti konfigurace časového limitu a chování funkce Keep-Alive jsou k `HubConnection` dispozici na samotném objektu:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1167">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="6e8d5-1168">.NET</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1168">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6e8d5-1169">Možnost</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1169">Option</span></span> | <span data-ttu-id="6e8d5-1170">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1170">Default value</span></span> | <span data-ttu-id="6e8d5-1171">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1171">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="6e8d5-1172">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1172">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6e8d5-1173">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1173">Timeout for server activity.</span></span> <span data-ttu-id="6e8d5-1174">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí `Closed` událost (`onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1174">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6e8d5-1175">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1175">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6e8d5-1176">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1176">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="6e8d5-1177">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1177">15 seconds</span></span> | <span data-ttu-id="6e8d5-1178">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1178">Timeout for initial server handshake.</span></span> <span data-ttu-id="6e8d5-1179">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a spustí `Closed` událost (`onclose` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1179">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="6e8d5-1180">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1180">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6e8d5-1181">Další informace o procesu handshake najdete v tématu [specifikace protokolu centra signalizace](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1181">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="6e8d5-1182">V klientovi .NET jsou hodnoty časového limitu zadány `TimeSpan` jako hodnoty.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1182">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="6e8d5-1183">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1183">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6e8d5-1184">Možnost</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1184">Option</span></span> | <span data-ttu-id="6e8d5-1185">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1185">Default value</span></span> | <span data-ttu-id="6e8d5-1186">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1186">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="6e8d5-1187">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1187">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6e8d5-1188">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1188">Timeout for server activity.</span></span> <span data-ttu-id="6e8d5-1189">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onclose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1189">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="6e8d5-1190">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1190">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6e8d5-1191">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1191">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="6e8d5-1192">Java</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1192">Java</span></span>](#tab/java)

| <span data-ttu-id="6e8d5-1193">Možnost</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1193">Option</span></span> | <span data-ttu-id="6e8d5-1194">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1194">Default value</span></span> | <span data-ttu-id="6e8d5-1195">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1195">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="6e8d5-1196">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1196">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="6e8d5-1197">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1197">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="6e8d5-1198">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1198">Timeout for server activity.</span></span> <span data-ttu-id="6e8d5-1199">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onClose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1199">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="6e8d5-1200">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1200">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="6e8d5-1201">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit k příkazům k zadání času.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1201">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="6e8d5-1202">15 sekund</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1202">15 seconds</span></span> | <span data-ttu-id="6e8d5-1203">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1203">Timeout for initial server handshake.</span></span> <span data-ttu-id="6e8d5-1204">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje `onClose` událost.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1204">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="6e8d5-1205">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1205">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="6e8d5-1206">Další informace o procesu handshake najdete v tématu [specifikace protokolu centra signalizace](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1206">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="6e8d5-1207">Konfigurace dalších možností</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1207">Configure additional options</span></span>

<span data-ttu-id="6e8d5-1208">Další možnosti lze konfigurovat v `WithUrl` metodě (`withUrl` v jazyce JavaScript) na `HubConnectionBuilder` různých konfiguračních rozhraních API `HttpHubConnectionBuilder` v klientovi Java:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1208">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="6e8d5-1209">.NET</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1209">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="6e8d5-1210">Možnost .NET</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1210">.NET Option</span></span> |  <span data-ttu-id="6e8d5-1211">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1211">Default value</span></span> | <span data-ttu-id="6e8d5-1212">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1212">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="6e8d5-1213">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1213">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="6e8d5-1214">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1214">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6e8d5-1215">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1215">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6e8d5-1216">Toto nastavení se nedá povolit při použití služby signalizace Azure.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1216">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="6e8d5-1217">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1217">Empty</span></span> | <span data-ttu-id="6e8d5-1218">Kolekce certifikátů TLS pro odeslání na požadavky na ověření.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1218">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="6e8d5-1219">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1219">Empty</span></span> | <span data-ttu-id="6e8d5-1220">Kolekce souborů cookie protokolu HTTP, které se mají odeslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1220">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="6e8d5-1221">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1221">Empty</span></span> | <span data-ttu-id="6e8d5-1222">Přihlašovací údaje, které se mají poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1222">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="6e8d5-1223">5 sekund</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1223">5 seconds</span></span> | <span data-ttu-id="6e8d5-1224">Jenom objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1224">WebSockets only.</span></span> <span data-ttu-id="6e8d5-1225">Maximální doba, po jejímž uplynutí bude klient čekat na ukončení žádosti o uzavření.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1225">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="6e8d5-1226">Pokud server nepotvrdí zavření v této době, klient se odpojí.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1226">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="6e8d5-1227">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1227">Empty</span></span> | <span data-ttu-id="6e8d5-1228">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1228">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="6e8d5-1229">Delegát, který lze použít ke konfiguraci nebo nahrazení `HttpMessageHandler` používaného k odeslání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1229">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="6e8d5-1230">Nepoužívá se pro připojení pomocí protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1230">Not used for WebSocket connections.</span></span> <span data-ttu-id="6e8d5-1231">Tento delegát musí vracet hodnotu, která není null, a obdrží výchozí hodnotu jako parametr.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1231">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="6e8d5-1232">Buď změňte nastavení u této výchozí hodnoty a vraťte je, nebo vraťte novou `HttpMessageHandler` instanci.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1232">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="6e8d5-1233">**Při nahrazování obslužné rutiny Nezapomeňte zkopírovat nastavení, která chcete zachovat, od poskytnuté obslužné rutiny, jinak konfigurované možnosti (například soubory cookie a záhlaví) nebudou použity pro novou obslužnou rutinu.**</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1233">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="6e8d5-1234">Proxy server HTTP, který se má použít při odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1234">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="6e8d5-1235">Nastavte tuto logickou hodnotu pro odeslání výchozích přihlašovacích údajů pro požadavky HTTP a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1235">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="6e8d5-1236">To umožňuje použití ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1236">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="6e8d5-1237">Delegát, který lze použít ke konfiguraci dalších možností protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1237">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="6e8d5-1238">Přijímá instanci [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , která se dá použít ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1238">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="6e8d5-1239">JavaScript</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1239">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="6e8d5-1240">Možnost JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1240">JavaScript Option</span></span> | <span data-ttu-id="6e8d5-1241">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1241">Default Value</span></span> | <span data-ttu-id="6e8d5-1242">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1242">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="6e8d5-1243">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1243">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="6e8d5-1244">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1244">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6e8d5-1245">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1245">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6e8d5-1246">Toto nastavení se nedá povolit při použití služby signalizace Azure.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1246">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="6e8d5-1247">Java</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1247">Java</span></span>](#tab/java)

| <span data-ttu-id="6e8d5-1248">Možnost jazyka Java</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1248">Java Option</span></span> | <span data-ttu-id="6e8d5-1249">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1249">Default Value</span></span> | <span data-ttu-id="6e8d5-1250">Popis</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1250">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="6e8d5-1251">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1251">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="6e8d5-1252">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1252">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="6e8d5-1253">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1253">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="6e8d5-1254">Toto nastavení se nedá povolit při použití služby signalizace Azure.</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1254">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="6e8d5-1255">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1255">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="6e8d5-1256">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1256">Empty</span></span> | <span data-ttu-id="6e8d5-1257">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1257">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="6e8d5-1258">V klientu .NET můžete tyto možnosti upravit pomocí delegáta možností, který je k `WithUrl`dispozici pro:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1258">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/myhub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="6e8d5-1259">V klientovi JavaScriptu lze tyto možnosti poskytnout v objektu jazyka JavaScript, který je k dispozici pro `withUrl`:</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1259">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/myhub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="6e8d5-1260">V klientovi Java lze tyto možnosti nakonfigurovat s metodami `HttpHubConnectionBuilder` vrácenými z`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1260">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/myhub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="6e8d5-1261">Další materiály a zdroje informací</span><span class="sxs-lookup"><span data-stu-id="6e8d5-1261">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
