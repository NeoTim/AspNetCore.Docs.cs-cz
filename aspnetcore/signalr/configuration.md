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
ms.openlocfilehash: 09866f1fd56a4d0747ef3814c85ab5070cfb8d59
ms.sourcegitcommit: a423e8fcde4b6181a3073ed646a603ba20bfa5f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2020
ms.locfileid: "84756116"
---
# <a name="aspnet-core-signalr-configuration"></a><span data-ttu-id="4683e-103">SignalRKonfigurace ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="4683e-103">ASP.NET Core SignalR configuration</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="4683e-104">Možnosti serializace JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="4683e-104">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="4683e-105">ASP.NET Core SignalR podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="4683e-105">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="4683e-106">Každý protokol má možnosti konfigurace serializace.</span><span class="sxs-lookup"><span data-stu-id="4683e-106">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="4683e-107">Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="4683e-107">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="4683e-108">`AddJsonProtocol`lze přidat po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="4683e-108">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="4683e-109">`AddJsonProtocol`Metoda přebírá delegáta, který přijímá `options` objekt.</span><span class="sxs-lookup"><span data-stu-id="4683e-109">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="4683e-110">Vlastnost [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) tohoto objektu je `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objekt, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="4683e-110">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="4683e-111">Další informace najdete v [System.Text.Jsv dokumentaci](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="4683e-111">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="4683e-112">Chcete-li například nakonfigurovat serializátor, aby neměnil velká a malá písmena názvů vlastností namísto výchozích názvů "camelCase", použijte následující kód v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="4683e-112">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="4683e-113">V klientovi .NET `AddJsonProtocol` existuje stejná rozšiřující metoda v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="4683e-113">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="4683e-114">`Microsoft.Extensions.DependencyInjection`Obor názvů musí být importován, aby bylo možné přeložit metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="4683e-114">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="4683e-115">V tuto chvíli není možné konfigurovat serializaci JSON v klientovi jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4683e-115">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="4683e-116">Přepnout na Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="4683e-116">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="4683e-117">Pokud potřebujete funkce nástroje `Newtonsoft.Json` , které nejsou podporované v systému `System.Text.Json` , přečtěte si téma [Přepnutí na Newtonsoft.Jsna](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="4683e-117">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="4683e-118">Možnosti serializace MessagePack</span><span class="sxs-lookup"><span data-stu-id="4683e-118">MessagePack serialization options</span></span>

<span data-ttu-id="4683e-119">Serializaci MessagePack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="4683e-119">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="4683e-120">Další podrobnosti najdete [v SignalR tématu MessagePack](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="4683e-120">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="4683e-121">V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi v jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4683e-121">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="4683e-122">Konfigurovat možnosti serveru</span><span class="sxs-lookup"><span data-stu-id="4683e-122">Configure server options</span></span>

<span data-ttu-id="4683e-123">Následující tabulka popisuje možnosti konfigurace SignalR Center:</span><span class="sxs-lookup"><span data-stu-id="4683e-123">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="4683e-124">Možnost</span><span class="sxs-lookup"><span data-stu-id="4683e-124">Option</span></span> | <span data-ttu-id="4683e-125">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-125">Default Value</span></span> | <span data-ttu-id="4683e-126">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-126">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="4683e-127">30 sekund</span><span class="sxs-lookup"><span data-stu-id="4683e-127">30 seconds</span></span> | <span data-ttu-id="4683e-128">Server bude předpokládat, že je klient odpojený, pokud v tomto intervalu neobdržel zprávu (včetně Keep-Alive).</span><span class="sxs-lookup"><span data-stu-id="4683e-128">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="4683e-129">Může trvat delší dobu, než je tento časový limit, aby bylo možné klienta ve skutečnosti označit jako odpojený, protože to je implementováno.</span><span class="sxs-lookup"><span data-stu-id="4683e-129">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="4683e-130">Doporučená hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="4683e-130">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="4683e-131">15 sekund</span><span class="sxs-lookup"><span data-stu-id="4683e-131">15 seconds</span></span> | <span data-ttu-id="4683e-132">Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře.</span><span class="sxs-lookup"><span data-stu-id="4683e-132">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="4683e-133">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="4683e-133">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="4683e-134">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="4683e-134">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="4683e-135">15 sekund</span><span class="sxs-lookup"><span data-stu-id="4683e-135">15 seconds</span></span> | <span data-ttu-id="4683e-136">Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="4683e-136">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="4683e-137">Při změně `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení změňte nastavení v klientovi.</span><span class="sxs-lookup"><span data-stu-id="4683e-137">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="4683e-138">Doporučená `ServerTimeout` / `serverTimeoutInMilliseconds` hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="4683e-138">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="4683e-139">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="4683e-139">All installed protocols</span></span> | <span data-ttu-id="4683e-140">Protokoly podporované tímto rozbočovačem</span><span class="sxs-lookup"><span data-stu-id="4683e-140">Protocols supported by this hub.</span></span> <span data-ttu-id="4683e-141">Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly.</span><span class="sxs-lookup"><span data-stu-id="4683e-141">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="4683e-142">Pokud se `true` do klientů vrátí podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="4683e-142">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="4683e-143">Výchozí hodnota je `false` , protože tyto zprávy o výjimce můžou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="4683e-143">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="4683e-144">Maximální počet položek, které lze uložit do vyrovnávací paměti pro datové proudy pro odeslání klienta.</span><span class="sxs-lookup"><span data-stu-id="4683e-144">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="4683e-145">Pokud je dosaženo tohoto limitu, zpracování volání je blokováno, dokud server nezpracovává položky datového proudu.</span><span class="sxs-lookup"><span data-stu-id="4683e-145">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="4683e-146">32 KB</span><span class="sxs-lookup"><span data-stu-id="4683e-146">32 KB</span></span> | <span data-ttu-id="4683e-147">Maximální velikost jedné příchozí zprávy centra</span><span class="sxs-lookup"><span data-stu-id="4683e-147">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="4683e-148">Možnosti lze nakonfigurovat pro všechna centra tím, že poskytnete možnosti delegáta pro `AddSignalR` volání v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="4683e-148">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="4683e-149">Možnosti pro jeden rozbočovač přepíší globální možnosti poskytované v `AddSignalR` a můžou být nakonfigurované pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="4683e-149">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="4683e-150">Rozšířené možnosti konfigurace protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="4683e-150">Advanced HTTP configuration options</span></span>

<span data-ttu-id="4683e-151">Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="4683e-151">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="4683e-152">Tyto možnosti jsou nakonfigurovány předáním delegáta [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="4683e-152">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="4683e-153">Následující tabulka popisuje možnosti konfigurace SignalR rozšířených možností protokolu HTTP v ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="4683e-153">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="4683e-154">Možnost</span><span class="sxs-lookup"><span data-stu-id="4683e-154">Option</span></span> | <span data-ttu-id="4683e-155">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-155">Default Value</span></span> | <span data-ttu-id="4683e-156">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-156">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="4683e-157">32 KB</span><span class="sxs-lookup"><span data-stu-id="4683e-157">32 KB</span></span> | <span data-ttu-id="4683e-158">Maximální počet bajtů přijatých od klienta, které jsou ukládány do vyrovnávací paměti serveru před použitím protitlaku.</span><span class="sxs-lookup"><span data-stu-id="4683e-158">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="4683e-159">Zvýšením této hodnoty umožníte, aby server přijímal větší zprávy rychleji bez použití zatížení, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="4683e-159">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="4683e-160">Data se automaticky shromažďují z `Authorize` atributů použitých pro třídu centra.</span><span class="sxs-lookup"><span data-stu-id="4683e-160">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="4683e-161">Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru</span><span class="sxs-lookup"><span data-stu-id="4683e-161">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="4683e-162">32 KB</span><span class="sxs-lookup"><span data-stu-id="4683e-162">32 KB</span></span> | <span data-ttu-id="4683e-163">Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru před pozorováním protitlaku.</span><span class="sxs-lookup"><span data-stu-id="4683e-163">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="4683e-164">Zvýšením této hodnoty umožníte serveru rychleji ukládat větší zprávy bez nutnosti očekávat zatížení, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="4683e-164">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="4683e-165">Všechny přenosy jsou povolené.</span><span class="sxs-lookup"><span data-stu-id="4683e-165">All Transports are enabled.</span></span> | <span data-ttu-id="4683e-166">Bitové příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenos, který může klient použít pro připojení.</span><span class="sxs-lookup"><span data-stu-id="4683e-166">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="4683e-167">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="4683e-167">See below.</span></span> | <span data-ttu-id="4683e-168">Další možnosti specifické pro přenos dlouhého cyklického dotazování.</span><span class="sxs-lookup"><span data-stu-id="4683e-168">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="4683e-169">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="4683e-169">See below.</span></span> | <span data-ttu-id="4683e-170">Další možnosti specifické pro přenos pomocí protokolu WebSockets</span><span class="sxs-lookup"><span data-stu-id="4683e-170">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="4683e-171">0</span><span class="sxs-lookup"><span data-stu-id="4683e-171">0</span></span> | <span data-ttu-id="4683e-172">Zadejte minimální verzi protokolu Negotiate.</span><span class="sxs-lookup"><span data-stu-id="4683e-172">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="4683e-173">Slouží k omezení klientů na novější verze.</span><span class="sxs-lookup"><span data-stu-id="4683e-173">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="4683e-174">Přenos dlouhého cyklického dotazování má další možnosti, které je možné konfigurovat pomocí `LongPolling` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="4683e-174">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="4683e-175">Možnost</span><span class="sxs-lookup"><span data-stu-id="4683e-175">Option</span></span> | <span data-ttu-id="4683e-176">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-176">Default Value</span></span> | <span data-ttu-id="4683e-177">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-177">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="4683e-178">90 sekund</span><span class="sxs-lookup"><span data-stu-id="4683e-178">90 seconds</span></span> | <span data-ttu-id="4683e-179">Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jedné žádosti o cyklické dotazování.</span><span class="sxs-lookup"><span data-stu-id="4683e-179">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="4683e-180">Snížení této hodnoty způsobí, že klient bude vydávat nové požadavky na dotaz častěji.</span><span class="sxs-lookup"><span data-stu-id="4683e-180">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="4683e-181">Přenos protokolu WebSocket má další možnosti, které je možné konfigurovat pomocí `WebSockets` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="4683e-181">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="4683e-182">Možnost</span><span class="sxs-lookup"><span data-stu-id="4683e-182">Option</span></span> | <span data-ttu-id="4683e-183">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-183">Default Value</span></span> | <span data-ttu-id="4683e-184">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-184">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="4683e-185">5 sekund</span><span class="sxs-lookup"><span data-stu-id="4683e-185">5 seconds</span></span> | <span data-ttu-id="4683e-186">Pokud se po ukončení serveru aplikace v tomto časovém intervalu nepovede zavřít, připojení se ukončí.</span><span class="sxs-lookup"><span data-stu-id="4683e-186">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="4683e-187">Delegát, který lze použít k nastavení `Sec-WebSocket-Protocol` záhlaví na vlastní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="4683e-187">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="4683e-188">Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že se vrátí požadovaná hodnota.</span><span class="sxs-lookup"><span data-stu-id="4683e-188">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="4683e-189">Konfigurace možností klienta</span><span class="sxs-lookup"><span data-stu-id="4683e-189">Configure client options</span></span>

<span data-ttu-id="4683e-190">Možnosti klienta lze nakonfigurovat pro `HubConnectionBuilder` typ (k dispozici v klientech rozhraní .NET a v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="4683e-190">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="4683e-191">Je také k dispozici v klientovi Java, ale `HttpHubConnectionBuilder` podtřídou je to, co obsahuje možnosti konfigurace tvůrce a také na `HubConnection` sebe sama.</span><span class="sxs-lookup"><span data-stu-id="4683e-191">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="4683e-192">Konfigurovat protokolování</span><span class="sxs-lookup"><span data-stu-id="4683e-192">Configure logging</span></span>

<span data-ttu-id="4683e-193">Protokolování je konfigurováno v klientovi .NET pomocí `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="4683e-193">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="4683e-194">Zprostředkovatelé protokolování a filtry je možné zaregistrovat stejným způsobem jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="4683e-194">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="4683e-195">Další informace najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="4683e-195">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="4683e-196">Aby bylo možné registrovat poskytovatele protokolování, je nutné nainstalovat potřebné balíčky.</span><span class="sxs-lookup"><span data-stu-id="4683e-196">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="4683e-197">Úplný seznam najdete v části [předdefinovaná zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="4683e-197">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="4683e-198">Chcete-li například povolit protokolování konzoly, nainstalujte `Microsoft.Extensions.Logging.Console` balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="4683e-198">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="4683e-199">Zavolejte `AddConsole` metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="4683e-199">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="4683e-200">V klientovi jazyka JavaScript existuje podobná `configureLogging` metoda.</span><span class="sxs-lookup"><span data-stu-id="4683e-200">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="4683e-201">Zadejte `LogLevel` hodnotu, která určuje minimální úroveň zpráv protokolu, které mají být vyprodukovány.</span><span class="sxs-lookup"><span data-stu-id="4683e-201">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="4683e-202">Protokoly se zapisují do okna konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="4683e-202">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="4683e-203">Místo `LogLevel` hodnoty můžete zadat také `string` hodnotu představující název úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="4683e-203">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="4683e-204">To je užitečné při konfiguraci SignalR protokolování v prostředích, kde nemáte přístup k `LogLevel` konstantám.</span><span class="sxs-lookup"><span data-stu-id="4683e-204">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="4683e-205">V následující tabulce jsou uvedeny dostupné úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="4683e-205">The following table lists the available log levels.</span></span> <span data-ttu-id="4683e-206">Hodnota, kterou zadáte, `configureLogging` nastaví **minimální** úroveň protokolu, která se bude protokolovat.</span><span class="sxs-lookup"><span data-stu-id="4683e-206">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="4683e-207">Zprávy zaznamenané na této úrovni **nebo úrovně uvedené za ní v tabulce**budou protokolovány.</span><span class="sxs-lookup"><span data-stu-id="4683e-207">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="4683e-208">Řetězec</span><span class="sxs-lookup"><span data-stu-id="4683e-208">String</span></span>                      | <span data-ttu-id="4683e-209">LogLevel</span><span class="sxs-lookup"><span data-stu-id="4683e-209">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="4683e-210">`info` **nebo** `information`</span><span class="sxs-lookup"><span data-stu-id="4683e-210">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="4683e-211">`warn` **nebo** `warning`</span><span class="sxs-lookup"><span data-stu-id="4683e-211">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="4683e-212">Chcete-li protokolování zcela zakázat, zadejte `signalR.LogLevel.None` v `configureLogging` metodě.</span><span class="sxs-lookup"><span data-stu-id="4683e-212">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="4683e-213">Další informace o protokolování naleznete v dokumentaci k [ SignalR diagnostice](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="4683e-213">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="4683e-214">SignalRKlient Java používá k protokolování knihovnu [SLF4J](https://www.slf4j.org/) .</span><span class="sxs-lookup"><span data-stu-id="4683e-214">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="4683e-215">Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí.</span><span class="sxs-lookup"><span data-stu-id="4683e-215">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="4683e-216">Následující fragment kódu ukazuje, jak používat `java.util.logging` s SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="4683e-216">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="4683e-217">Pokud ve svých závislostech nenakonfigurujete protokolování, SLF4J načte výchozí protokolovací nástroj No-operation s následující zprávou upozornění:</span><span class="sxs-lookup"><span data-stu-id="4683e-217">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="4683e-218">Tuto možnost lze bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="4683e-218">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="4683e-219">Konfigurace povolených přenosů</span><span class="sxs-lookup"><span data-stu-id="4683e-219">Configure allowed transports</span></span>

<span data-ttu-id="4683e-220">Přenosy používané nástrojem SignalR lze konfigurovat ve `WithUrl` volání ( `withUrl` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="4683e-220">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="4683e-221">Bitové nebo hodnoty `HttpTransportType` lze použít k omezení klienta na používání pouze zadaných přenosů.</span><span class="sxs-lookup"><span data-stu-id="4683e-221">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="4683e-222">Ve výchozím nastavení jsou povolené všechny přenosy.</span><span class="sxs-lookup"><span data-stu-id="4683e-222">All transports are enabled by default.</span></span>

<span data-ttu-id="4683e-223">Pokud třeba chcete zakázat přenos událostí odeslaných serverem, ale povolit WebSockets a dlouhá připojení s dotazem:</span><span class="sxs-lookup"><span data-stu-id="4683e-223">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="4683e-224">V klientu jazyka JavaScript jsou přenosy konfigurovány nastavením `transport` pole v objektu Options, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="4683e-224">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="4683e-225">V této verzi je jediným dostupným přenosem klientský WebSocket v jazyce Java.</span><span class="sxs-lookup"><span data-stu-id="4683e-225">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="4683e-226">V klientovi Java se přenos vybere s `withTransport` metodou na `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="4683e-226">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="4683e-227">Klient Java standardně používá přenos pomocí protokolu WebSockets.</span><span class="sxs-lookup"><span data-stu-id="4683e-227">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="4683e-228">SignalRKlient Java ještě nepodporuje přenosovou zálohu.</span><span class="sxs-lookup"><span data-stu-id="4683e-228">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="4683e-229">Konfigurace ověřování nosiče</span><span class="sxs-lookup"><span data-stu-id="4683e-229">Configure bearer authentication</span></span>

<span data-ttu-id="4683e-230">K poskytnutí ověřovacích dat spolu s SignalR požadavky použijte `AccessTokenProvider` možnost ( `accessTokenFactory` v jazyce JavaScript) k určení funkce, která vrací požadovaný přístupový token.</span><span class="sxs-lookup"><span data-stu-id="4683e-230">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="4683e-231">V klientovi .NET se tento přístupový token předává jako token ověřování HTTP "Bearer" (s použitím `Authorization` hlavičky s typem `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="4683e-231">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="4683e-232">V klientu jazyka JavaScript se přístupový token používá jako nosný token, **s výjimkou** případů, kdy rozhraní API prohlížeče omezuje možnost použít hlavičky (konkrétně v požadavcích na události odeslané serverem a objekty WebSockets).</span><span class="sxs-lookup"><span data-stu-id="4683e-232">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="4683e-233">V těchto případech je přístupový token k dispozici jako hodnota řetězce dotazu `access_token` .</span><span class="sxs-lookup"><span data-stu-id="4683e-233">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="4683e-234">V klientu .NET `AccessTokenProvider` lze možnost zadat pomocí delegáta možnosti v `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="4683e-234">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="4683e-235">V klientovi JavaScriptu je přístupový token nakonfigurovaný nastavením `accessTokenFactory` pole v objektu Options v `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="4683e-235">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="4683e-236">V SignalR klientovi Java můžete nakonfigurovat nosný token, který se bude používat pro ověřování, a to poskytnutím továrny přístupového tokenu [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="4683e-236">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="4683e-237">Pomocí [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) můžete zadat [RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="4683e-237">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="4683e-238">Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.</span><span class="sxs-lookup"><span data-stu-id="4683e-238">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="4683e-239">Konfigurace možností timeout a Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="4683e-239">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="4683e-240">Další možnosti konfigurace časového limitu a chování funkce Keep-Alive jsou k dispozici na `HubConnection` samotném objektu:</span><span class="sxs-lookup"><span data-stu-id="4683e-240">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="4683e-241">.NET</span><span class="sxs-lookup"><span data-stu-id="4683e-241">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="4683e-242">Možnost</span><span class="sxs-lookup"><span data-stu-id="4683e-242">Option</span></span> | <span data-ttu-id="4683e-243">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-243">Default value</span></span> | <span data-ttu-id="4683e-244">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-244">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="4683e-245">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="4683e-245">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="4683e-246">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="4683e-246">Timeout for server activity.</span></span> <span data-ttu-id="4683e-247">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí `Closed` událost ( `onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="4683e-247">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="4683e-248">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="4683e-248">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="4683e-249">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="4683e-249">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="4683e-250">15 sekund</span><span class="sxs-lookup"><span data-stu-id="4683e-250">15 seconds</span></span> | <span data-ttu-id="4683e-251">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="4683e-251">Timeout for initial server handshake.</span></span> <span data-ttu-id="4683e-252">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a spustí `Closed` událost ( `onclose` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="4683e-252">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="4683e-253">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="4683e-253">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="4683e-254">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="4683e-254">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="4683e-255">15 sekund</span><span class="sxs-lookup"><span data-stu-id="4683e-255">15 seconds</span></span> | <span data-ttu-id="4683e-256">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="4683e-256">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="4683e-257">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="4683e-257">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="4683e-258">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="4683e-258">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="4683e-259">V klientovi .NET jsou hodnoty časového limitu zadány jako `TimeSpan` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="4683e-259">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="4683e-260">JavaScript</span><span class="sxs-lookup"><span data-stu-id="4683e-260">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="4683e-261">Možnost</span><span class="sxs-lookup"><span data-stu-id="4683e-261">Option</span></span> | <span data-ttu-id="4683e-262">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-262">Default value</span></span> | <span data-ttu-id="4683e-263">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-263">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="4683e-264">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="4683e-264">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="4683e-265">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="4683e-265">Timeout for server activity.</span></span> <span data-ttu-id="4683e-266">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onclose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="4683e-266">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="4683e-267">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="4683e-267">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="4683e-268">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="4683e-268">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="4683e-269">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="4683e-269">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="4683e-270">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="4683e-270">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="4683e-271">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="4683e-271">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="4683e-272">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="4683e-272">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="4683e-273">Java</span><span class="sxs-lookup"><span data-stu-id="4683e-273">Java</span></span>](#tab/java)

| <span data-ttu-id="4683e-274">Možnost</span><span class="sxs-lookup"><span data-stu-id="4683e-274">Option</span></span> | <span data-ttu-id="4683e-275">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-275">Default value</span></span> | <span data-ttu-id="4683e-276">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-276">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="4683e-277">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="4683e-277">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="4683e-278">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="4683e-278">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="4683e-279">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="4683e-279">Timeout for server activity.</span></span> <span data-ttu-id="4683e-280">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onClose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="4683e-280">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="4683e-281">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="4683e-281">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="4683e-282">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="4683e-282">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="4683e-283">15 sekund</span><span class="sxs-lookup"><span data-stu-id="4683e-283">15 seconds</span></span> | <span data-ttu-id="4683e-284">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="4683e-284">Timeout for initial server handshake.</span></span> <span data-ttu-id="4683e-285">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje `onClose` událost.</span><span class="sxs-lookup"><span data-stu-id="4683e-285">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="4683e-286">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="4683e-286">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="4683e-287">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="4683e-287">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="4683e-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="4683e-288">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="4683e-289">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="4683e-289">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="4683e-290">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="4683e-290">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="4683e-291">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="4683e-291">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="4683e-292">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="4683e-292">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="4683e-293">Konfigurace dalších možností</span><span class="sxs-lookup"><span data-stu-id="4683e-293">Configure additional options</span></span>

<span data-ttu-id="4683e-294">Další možnosti lze konfigurovat v `WithUrl` `withUrl` metodě (v jazyce JavaScript) na `HubConnectionBuilder` různých konfiguračních rozhraních API v `HttpHubConnectionBuilder` klientovi Java:</span><span class="sxs-lookup"><span data-stu-id="4683e-294">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="4683e-295">.NET</span><span class="sxs-lookup"><span data-stu-id="4683e-295">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="4683e-296">Možnost .NET</span><span class="sxs-lookup"><span data-stu-id="4683e-296">.NET Option</span></span> |  <span data-ttu-id="4683e-297">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-297">Default value</span></span> | <span data-ttu-id="4683e-298">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-298">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="4683e-299">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="4683e-299">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="4683e-300">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="4683e-300">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="4683e-301">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="4683e-301">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="4683e-302">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="4683e-302">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="4683e-303">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="4683e-303">Empty</span></span> | <span data-ttu-id="4683e-304">Kolekce certifikátů TLS pro odeslání na požadavky na ověření.</span><span class="sxs-lookup"><span data-stu-id="4683e-304">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="4683e-305">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="4683e-305">Empty</span></span> | <span data-ttu-id="4683e-306">Kolekce souborů cookie protokolu HTTP, které se mají odeslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="4683e-306">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="4683e-307">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="4683e-307">Empty</span></span> | <span data-ttu-id="4683e-308">Přihlašovací údaje, které se mají poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="4683e-308">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="4683e-309">5 sekund</span><span class="sxs-lookup"><span data-stu-id="4683e-309">5 seconds</span></span> | <span data-ttu-id="4683e-310">Jenom objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="4683e-310">WebSockets only.</span></span> <span data-ttu-id="4683e-311">Maximální doba, po jejímž uplynutí bude klient čekat na ukončení žádosti o uzavření.</span><span class="sxs-lookup"><span data-stu-id="4683e-311">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="4683e-312">Pokud server nepotvrdí zavření v této době, klient se odpojí.</span><span class="sxs-lookup"><span data-stu-id="4683e-312">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="4683e-313">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="4683e-313">Empty</span></span> | <span data-ttu-id="4683e-314">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="4683e-314">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="4683e-315">Delegát, který lze použít ke konfiguraci nebo nahrazení `HttpMessageHandler` používaného k odeslání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="4683e-315">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="4683e-316">Nepoužívá se pro připojení pomocí protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="4683e-316">Not used for WebSocket connections.</span></span> <span data-ttu-id="4683e-317">Tento delegát musí vracet hodnotu, která není null, a obdrží výchozí hodnotu jako parametr.</span><span class="sxs-lookup"><span data-stu-id="4683e-317">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="4683e-318">Buď změňte nastavení u této výchozí hodnoty a vraťte je, nebo vraťte novou `HttpMessageHandler` instanci.</span><span class="sxs-lookup"><span data-stu-id="4683e-318">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="4683e-319">**Při nahrazování obslužné rutiny Nezapomeňte zkopírovat nastavení, která chcete zachovat, od poskytnuté obslužné rutiny, jinak konfigurované možnosti (například soubory cookie a záhlaví) nebudou použity pro novou obslužnou rutinu.**</span><span class="sxs-lookup"><span data-stu-id="4683e-319">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="4683e-320">Proxy server HTTP, který se má použít při odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="4683e-320">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="4683e-321">Nastavte tuto logickou hodnotu pro odeslání výchozích přihlašovacích údajů pro požadavky HTTP a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="4683e-321">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="4683e-322">To umožňuje použití ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="4683e-322">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="4683e-323">Delegát, který lze použít ke konfiguraci dalších možností protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="4683e-323">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="4683e-324">Přijímá instanci [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , která se dá použít ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="4683e-324">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="4683e-325">JavaScript</span><span class="sxs-lookup"><span data-stu-id="4683e-325">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="4683e-326">Možnost JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="4683e-326">JavaScript Option</span></span> | <span data-ttu-id="4683e-327">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-327">Default Value</span></span> | <span data-ttu-id="4683e-328">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-328">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="4683e-329">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="4683e-329">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="4683e-330">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="4683e-330">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="4683e-331">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="4683e-331">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="4683e-332">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="4683e-332">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `withCredentials` | `true` | <span data-ttu-id="4683e-333">Určuje, jestli se budou odesílat přihlašovací údaje s požadavkem CORS.</span><span class="sxs-lookup"><span data-stu-id="4683e-333">Specifies whether credentials will be sent with the CORS request.</span></span> <span data-ttu-id="4683e-334">Azure App Service používá pro rychlé relace soubory cookie a vyžaduje, aby tato možnost fungovala správně.</span><span class="sxs-lookup"><span data-stu-id="4683e-334">Azure App Service uses cookies for sticky sessions and needs this option enabled to work correctly.</span></span> <span data-ttu-id="4683e-335">Další informace o CORS s SignalR najdete v tématu <xref:signalr/security#cross-origin-resource-sharing> .</span><span class="sxs-lookup"><span data-stu-id="4683e-335">For more info on CORS with SignalR, see <xref:signalr/security#cross-origin-resource-sharing>.</span></span> |

# <a name="java"></a>[<span data-ttu-id="4683e-336">Java</span><span class="sxs-lookup"><span data-stu-id="4683e-336">Java</span></span>](#tab/java)

| <span data-ttu-id="4683e-337">Možnost jazyka Java</span><span class="sxs-lookup"><span data-stu-id="4683e-337">Java Option</span></span> | <span data-ttu-id="4683e-338">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-338">Default Value</span></span> | <span data-ttu-id="4683e-339">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-339">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="4683e-340">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="4683e-340">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="4683e-341">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="4683e-341">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="4683e-342">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="4683e-342">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="4683e-343">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="4683e-343">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="4683e-344">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="4683e-344">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="4683e-345">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="4683e-345">Empty</span></span> | <span data-ttu-id="4683e-346">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="4683e-346">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="4683e-347">V klientu .NET můžete tyto možnosti upravit pomocí delegáta možností, který je k dispozici pro `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="4683e-347">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="4683e-348">V klientovi JavaScriptu lze tyto možnosti poskytnout v objektu jazyka JavaScript, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="4683e-348">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="4683e-349">V klientovi Java lze tyto možnosti nakonfigurovat s metodami `HttpHubConnectionBuilder` vrácenými z`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="4683e-349">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="4683e-350">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="4683e-350">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.1"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="4683e-351">Možnosti serializace JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="4683e-351">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="4683e-352">ASP.NET Core SignalR podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="4683e-352">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="4683e-353">Každý protokol má možnosti konfigurace serializace.</span><span class="sxs-lookup"><span data-stu-id="4683e-353">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="4683e-354">Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="4683e-354">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="4683e-355">`AddJsonProtocol`lze přidat po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="4683e-355">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="4683e-356">`AddJsonProtocol`Metoda přebírá delegáta, který přijímá `options` objekt.</span><span class="sxs-lookup"><span data-stu-id="4683e-356">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="4683e-357">Vlastnost [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) tohoto objektu je `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objekt, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="4683e-357">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="4683e-358">Další informace najdete v [System.Text.Jsv dokumentaci](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="4683e-358">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="4683e-359">Chcete-li například nakonfigurovat serializátor, aby neměnil velká a malá písmena názvů vlastností namísto výchozích názvů "camelCase", použijte následující kód v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="4683e-359">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null
    });
```

<span data-ttu-id="4683e-360">V klientovi .NET `AddJsonProtocol` existuje stejná rozšiřující metoda v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="4683e-360">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="4683e-361">`Microsoft.Extensions.DependencyInjection`Obor názvů musí být importován, aby bylo možné přeložit metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="4683e-361">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="4683e-362">V tuto chvíli není možné konfigurovat serializaci JSON v klientovi jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4683e-362">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="4683e-363">Přepnout na Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="4683e-363">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="4683e-364">Pokud potřebujete funkce nástroje `Newtonsoft.Json` , které nejsou podporované v systému `System.Text.Json` , přečtěte si téma [Přepnutí na Newtonsoft.Jsna](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="4683e-364">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="4683e-365">Možnosti serializace MessagePack</span><span class="sxs-lookup"><span data-stu-id="4683e-365">MessagePack serialization options</span></span>

<span data-ttu-id="4683e-366">Serializaci MessagePack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="4683e-366">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="4683e-367">Další podrobnosti najdete [v SignalR tématu MessagePack](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="4683e-367">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="4683e-368">V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi v jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4683e-368">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="4683e-369">Konfigurovat možnosti serveru</span><span class="sxs-lookup"><span data-stu-id="4683e-369">Configure server options</span></span>

<span data-ttu-id="4683e-370">Následující tabulka popisuje možnosti konfigurace SignalR Center:</span><span class="sxs-lookup"><span data-stu-id="4683e-370">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="4683e-371">Možnost</span><span class="sxs-lookup"><span data-stu-id="4683e-371">Option</span></span> | <span data-ttu-id="4683e-372">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-372">Default Value</span></span> | <span data-ttu-id="4683e-373">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-373">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="4683e-374">30 sekund</span><span class="sxs-lookup"><span data-stu-id="4683e-374">30 seconds</span></span> | <span data-ttu-id="4683e-375">Server bude předpokládat, že je klient odpojený, pokud v tomto intervalu neobdržel zprávu (včetně Keep-Alive).</span><span class="sxs-lookup"><span data-stu-id="4683e-375">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="4683e-376">Může trvat delší dobu, než je tento časový limit, aby bylo možné klienta ve skutečnosti označit jako odpojený, protože to je implementováno.</span><span class="sxs-lookup"><span data-stu-id="4683e-376">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="4683e-377">Doporučená hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="4683e-377">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="4683e-378">15 sekund</span><span class="sxs-lookup"><span data-stu-id="4683e-378">15 seconds</span></span> | <span data-ttu-id="4683e-379">Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře.</span><span class="sxs-lookup"><span data-stu-id="4683e-379">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="4683e-380">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="4683e-380">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="4683e-381">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="4683e-381">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="4683e-382">15 sekund</span><span class="sxs-lookup"><span data-stu-id="4683e-382">15 seconds</span></span> | <span data-ttu-id="4683e-383">Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="4683e-383">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="4683e-384">Při změně `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení změňte nastavení v klientovi.</span><span class="sxs-lookup"><span data-stu-id="4683e-384">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="4683e-385">Doporučená `ServerTimeout` / `serverTimeoutInMilliseconds` hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="4683e-385">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="4683e-386">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="4683e-386">All installed protocols</span></span> | <span data-ttu-id="4683e-387">Protokoly podporované tímto rozbočovačem</span><span class="sxs-lookup"><span data-stu-id="4683e-387">Protocols supported by this hub.</span></span> <span data-ttu-id="4683e-388">Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly.</span><span class="sxs-lookup"><span data-stu-id="4683e-388">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="4683e-389">Pokud se `true` do klientů vrátí podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="4683e-389">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="4683e-390">Výchozí hodnota je `false` , protože tyto zprávy o výjimce můžou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="4683e-390">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="4683e-391">Maximální počet položek, které lze uložit do vyrovnávací paměti pro datové proudy pro odeslání klienta.</span><span class="sxs-lookup"><span data-stu-id="4683e-391">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="4683e-392">Pokud je dosaženo tohoto limitu, zpracování volání je blokováno, dokud server nezpracovává položky datového proudu.</span><span class="sxs-lookup"><span data-stu-id="4683e-392">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="4683e-393">32 KB</span><span class="sxs-lookup"><span data-stu-id="4683e-393">32 KB</span></span> | <span data-ttu-id="4683e-394">Maximální velikost jedné příchozí zprávy centra</span><span class="sxs-lookup"><span data-stu-id="4683e-394">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="4683e-395">Možnosti lze nakonfigurovat pro všechna centra tím, že poskytnete možnosti delegáta pro `AddSignalR` volání v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="4683e-395">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="4683e-396">Možnosti pro jeden rozbočovač přepíší globální možnosti poskytované v `AddSignalR` a můžou být nakonfigurované pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="4683e-396">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="4683e-397">Rozšířené možnosti konfigurace protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="4683e-397">Advanced HTTP configuration options</span></span>

<span data-ttu-id="4683e-398">Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="4683e-398">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="4683e-399">Tyto možnosti jsou nakonfigurovány předáním delegáta [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="4683e-399">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="4683e-400">Následující tabulka popisuje možnosti konfigurace SignalR rozšířených možností protokolu HTTP v ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="4683e-400">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="4683e-401">Možnost</span><span class="sxs-lookup"><span data-stu-id="4683e-401">Option</span></span> | <span data-ttu-id="4683e-402">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-402">Default Value</span></span> | <span data-ttu-id="4683e-403">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-403">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="4683e-404">32 KB</span><span class="sxs-lookup"><span data-stu-id="4683e-404">32 KB</span></span> | <span data-ttu-id="4683e-405">Maximální počet bajtů přijatých od klienta, které jsou ukládány do vyrovnávací paměti serveru před použitím protitlaku.</span><span class="sxs-lookup"><span data-stu-id="4683e-405">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="4683e-406">Zvýšením této hodnoty umožníte, aby server přijímal větší zprávy rychleji bez použití zatížení, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="4683e-406">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="4683e-407">Data se automaticky shromažďují z `Authorize` atributů použitých pro třídu centra.</span><span class="sxs-lookup"><span data-stu-id="4683e-407">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="4683e-408">Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru</span><span class="sxs-lookup"><span data-stu-id="4683e-408">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="4683e-409">32 KB</span><span class="sxs-lookup"><span data-stu-id="4683e-409">32 KB</span></span> | <span data-ttu-id="4683e-410">Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru před pozorováním protitlaku.</span><span class="sxs-lookup"><span data-stu-id="4683e-410">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="4683e-411">Zvýšením této hodnoty umožníte serveru rychleji ukládat větší zprávy bez nutnosti očekávat zatížení, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="4683e-411">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="4683e-412">Všechny přenosy jsou povolené.</span><span class="sxs-lookup"><span data-stu-id="4683e-412">All Transports are enabled.</span></span> | <span data-ttu-id="4683e-413">Bitové příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenos, který může klient použít pro připojení.</span><span class="sxs-lookup"><span data-stu-id="4683e-413">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="4683e-414">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="4683e-414">See below.</span></span> | <span data-ttu-id="4683e-415">Další možnosti specifické pro přenos dlouhého cyklického dotazování.</span><span class="sxs-lookup"><span data-stu-id="4683e-415">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="4683e-416">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="4683e-416">See below.</span></span> | <span data-ttu-id="4683e-417">Další možnosti specifické pro přenos pomocí protokolu WebSockets</span><span class="sxs-lookup"><span data-stu-id="4683e-417">Additional options specific to the WebSockets transport.</span></span> |
| `MinimumProtocolVersion` | <span data-ttu-id="4683e-418">0</span><span class="sxs-lookup"><span data-stu-id="4683e-418">0</span></span> | <span data-ttu-id="4683e-419">Zadejte minimální verzi protokolu Negotiate.</span><span class="sxs-lookup"><span data-stu-id="4683e-419">Specify the minimum version of the negotiate protocol.</span></span> <span data-ttu-id="4683e-420">Slouží k omezení klientů na novější verze.</span><span class="sxs-lookup"><span data-stu-id="4683e-420">This is used to limit clients to newer versions.</span></span> |

<span data-ttu-id="4683e-421">Přenos dlouhého cyklického dotazování má další možnosti, které je možné konfigurovat pomocí `LongPolling` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="4683e-421">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="4683e-422">Možnost</span><span class="sxs-lookup"><span data-stu-id="4683e-422">Option</span></span> | <span data-ttu-id="4683e-423">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-423">Default Value</span></span> | <span data-ttu-id="4683e-424">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-424">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="4683e-425">90 sekund</span><span class="sxs-lookup"><span data-stu-id="4683e-425">90 seconds</span></span> | <span data-ttu-id="4683e-426">Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jedné žádosti o cyklické dotazování.</span><span class="sxs-lookup"><span data-stu-id="4683e-426">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="4683e-427">Snížení této hodnoty způsobí, že klient bude vydávat nové požadavky na dotaz častěji.</span><span class="sxs-lookup"><span data-stu-id="4683e-427">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="4683e-428">Přenos protokolu WebSocket má další možnosti, které je možné konfigurovat pomocí `WebSockets` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="4683e-428">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="4683e-429">Možnost</span><span class="sxs-lookup"><span data-stu-id="4683e-429">Option</span></span> | <span data-ttu-id="4683e-430">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-430">Default Value</span></span> | <span data-ttu-id="4683e-431">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-431">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="4683e-432">5 sekund</span><span class="sxs-lookup"><span data-stu-id="4683e-432">5 seconds</span></span> | <span data-ttu-id="4683e-433">Pokud se po ukončení serveru aplikace v tomto časovém intervalu nepovede zavřít, připojení se ukončí.</span><span class="sxs-lookup"><span data-stu-id="4683e-433">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="4683e-434">Delegát, který lze použít k nastavení `Sec-WebSocket-Protocol` záhlaví na vlastní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="4683e-434">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="4683e-435">Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že se vrátí požadovaná hodnota.</span><span class="sxs-lookup"><span data-stu-id="4683e-435">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="4683e-436">Konfigurace možností klienta</span><span class="sxs-lookup"><span data-stu-id="4683e-436">Configure client options</span></span>

<span data-ttu-id="4683e-437">Možnosti klienta lze nakonfigurovat pro `HubConnectionBuilder` typ (k dispozici v klientech rozhraní .NET a v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="4683e-437">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="4683e-438">Je také k dispozici v klientovi Java, ale `HttpHubConnectionBuilder` podtřídou je to, co obsahuje možnosti konfigurace tvůrce a také na `HubConnection` sebe sama.</span><span class="sxs-lookup"><span data-stu-id="4683e-438">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="4683e-439">Konfigurovat protokolování</span><span class="sxs-lookup"><span data-stu-id="4683e-439">Configure logging</span></span>

<span data-ttu-id="4683e-440">Protokolování je konfigurováno v klientovi .NET pomocí `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="4683e-440">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="4683e-441">Zprostředkovatelé protokolování a filtry je možné zaregistrovat stejným způsobem jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="4683e-441">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="4683e-442">Další informace najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="4683e-442">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="4683e-443">Aby bylo možné registrovat poskytovatele protokolování, je nutné nainstalovat potřebné balíčky.</span><span class="sxs-lookup"><span data-stu-id="4683e-443">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="4683e-444">Úplný seznam najdete v části [předdefinovaná zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="4683e-444">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="4683e-445">Chcete-li například povolit protokolování konzoly, nainstalujte `Microsoft.Extensions.Logging.Console` balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="4683e-445">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="4683e-446">Zavolejte `AddConsole` metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="4683e-446">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="4683e-447">V klientovi jazyka JavaScript existuje podobná `configureLogging` metoda.</span><span class="sxs-lookup"><span data-stu-id="4683e-447">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="4683e-448">Zadejte `LogLevel` hodnotu, která určuje minimální úroveň zpráv protokolu, které mají být vyprodukovány.</span><span class="sxs-lookup"><span data-stu-id="4683e-448">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="4683e-449">Protokoly se zapisují do okna konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="4683e-449">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="4683e-450">Místo `LogLevel` hodnoty můžete zadat také `string` hodnotu představující název úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="4683e-450">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="4683e-451">To je užitečné při konfiguraci SignalR protokolování v prostředích, kde nemáte přístup k `LogLevel` konstantám.</span><span class="sxs-lookup"><span data-stu-id="4683e-451">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="4683e-452">V následující tabulce jsou uvedeny dostupné úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="4683e-452">The following table lists the available log levels.</span></span> <span data-ttu-id="4683e-453">Hodnota, kterou zadáte, `configureLogging` nastaví **minimální** úroveň protokolu, která se bude protokolovat.</span><span class="sxs-lookup"><span data-stu-id="4683e-453">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="4683e-454">Zprávy zaznamenané na této úrovni **nebo úrovně uvedené za ní v tabulce**budou protokolovány.</span><span class="sxs-lookup"><span data-stu-id="4683e-454">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="4683e-455">Řetězec</span><span class="sxs-lookup"><span data-stu-id="4683e-455">String</span></span>                      | <span data-ttu-id="4683e-456">LogLevel</span><span class="sxs-lookup"><span data-stu-id="4683e-456">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="4683e-457">`info` **nebo** `information`</span><span class="sxs-lookup"><span data-stu-id="4683e-457">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="4683e-458">`warn` **nebo** `warning`</span><span class="sxs-lookup"><span data-stu-id="4683e-458">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="4683e-459">Chcete-li protokolování zcela zakázat, zadejte `signalR.LogLevel.None` v `configureLogging` metodě.</span><span class="sxs-lookup"><span data-stu-id="4683e-459">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="4683e-460">Další informace o protokolování naleznete v dokumentaci k [ SignalR diagnostice](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="4683e-460">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="4683e-461">SignalRKlient Java používá k protokolování knihovnu [SLF4J](https://www.slf4j.org/) .</span><span class="sxs-lookup"><span data-stu-id="4683e-461">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="4683e-462">Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí.</span><span class="sxs-lookup"><span data-stu-id="4683e-462">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="4683e-463">Následující fragment kódu ukazuje, jak používat `java.util.logging` s SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="4683e-463">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="4683e-464">Pokud ve svých závislostech nenakonfigurujete protokolování, SLF4J načte výchozí protokolovací nástroj No-operation s následující zprávou upozornění:</span><span class="sxs-lookup"><span data-stu-id="4683e-464">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="4683e-465">Tuto možnost lze bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="4683e-465">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="4683e-466">Konfigurace povolených přenosů</span><span class="sxs-lookup"><span data-stu-id="4683e-466">Configure allowed transports</span></span>

<span data-ttu-id="4683e-467">Přenosy používané nástrojem SignalR lze konfigurovat ve `WithUrl` volání ( `withUrl` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="4683e-467">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="4683e-468">Bitové nebo hodnoty `HttpTransportType` lze použít k omezení klienta na používání pouze zadaných přenosů.</span><span class="sxs-lookup"><span data-stu-id="4683e-468">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="4683e-469">Ve výchozím nastavení jsou povolené všechny přenosy.</span><span class="sxs-lookup"><span data-stu-id="4683e-469">All transports are enabled by default.</span></span>

<span data-ttu-id="4683e-470">Pokud třeba chcete zakázat přenos událostí odeslaných serverem, ale povolit WebSockets a dlouhá připojení s dotazem:</span><span class="sxs-lookup"><span data-stu-id="4683e-470">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="4683e-471">V klientu jazyka JavaScript jsou přenosy konfigurovány nastavením `transport` pole v objektu Options, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="4683e-471">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="4683e-472">V této verzi je jediným dostupným přenosem klientský WebSocket v jazyce Java.</span><span class="sxs-lookup"><span data-stu-id="4683e-472">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="4683e-473">V klientovi Java se přenos vybere s `withTransport` metodou na `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="4683e-473">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="4683e-474">Klient Java standardně používá přenos pomocí protokolu WebSockets.</span><span class="sxs-lookup"><span data-stu-id="4683e-474">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="4683e-475">SignalRKlient Java ještě nepodporuje přenosovou zálohu.</span><span class="sxs-lookup"><span data-stu-id="4683e-475">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="4683e-476">Konfigurace ověřování nosiče</span><span class="sxs-lookup"><span data-stu-id="4683e-476">Configure bearer authentication</span></span>

<span data-ttu-id="4683e-477">K poskytnutí ověřovacích dat spolu s SignalR požadavky použijte `AccessTokenProvider` možnost ( `accessTokenFactory` v jazyce JavaScript) k určení funkce, která vrací požadovaný přístupový token.</span><span class="sxs-lookup"><span data-stu-id="4683e-477">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="4683e-478">V klientovi .NET se tento přístupový token předává jako token ověřování HTTP "Bearer" (s použitím `Authorization` hlavičky s typem `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="4683e-478">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="4683e-479">V klientu jazyka JavaScript se přístupový token používá jako nosný token, **s výjimkou** případů, kdy rozhraní API prohlížeče omezuje možnost použít hlavičky (konkrétně v požadavcích na události odeslané serverem a objekty WebSockets).</span><span class="sxs-lookup"><span data-stu-id="4683e-479">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="4683e-480">V těchto případech je přístupový token k dispozici jako hodnota řetězce dotazu `access_token` .</span><span class="sxs-lookup"><span data-stu-id="4683e-480">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="4683e-481">V klientu .NET `AccessTokenProvider` lze možnost zadat pomocí delegáta možnosti v `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="4683e-481">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="4683e-482">V klientovi JavaScriptu je přístupový token nakonfigurovaný nastavením `accessTokenFactory` pole v objektu Options v `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="4683e-482">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="4683e-483">V SignalR klientovi Java můžete nakonfigurovat nosný token, který se bude používat pro ověřování, a to poskytnutím továrny přístupového tokenu [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="4683e-483">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="4683e-484">Pomocí [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) můžete zadat [RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="4683e-484">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="4683e-485">Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.</span><span class="sxs-lookup"><span data-stu-id="4683e-485">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="4683e-486">Konfigurace možností timeout a Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="4683e-486">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="4683e-487">Další možnosti konfigurace časového limitu a chování funkce Keep-Alive jsou k dispozici na `HubConnection` samotném objektu:</span><span class="sxs-lookup"><span data-stu-id="4683e-487">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="4683e-488">.NET</span><span class="sxs-lookup"><span data-stu-id="4683e-488">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="4683e-489">Možnost</span><span class="sxs-lookup"><span data-stu-id="4683e-489">Option</span></span> | <span data-ttu-id="4683e-490">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-490">Default value</span></span> | <span data-ttu-id="4683e-491">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-491">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="4683e-492">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="4683e-492">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="4683e-493">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="4683e-493">Timeout for server activity.</span></span> <span data-ttu-id="4683e-494">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí `Closed` událost ( `onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="4683e-494">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="4683e-495">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="4683e-495">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="4683e-496">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="4683e-496">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="4683e-497">15 sekund</span><span class="sxs-lookup"><span data-stu-id="4683e-497">15 seconds</span></span> | <span data-ttu-id="4683e-498">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="4683e-498">Timeout for initial server handshake.</span></span> <span data-ttu-id="4683e-499">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a spustí `Closed` událost ( `onclose` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="4683e-499">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="4683e-500">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="4683e-500">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="4683e-501">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="4683e-501">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="4683e-502">15 sekund</span><span class="sxs-lookup"><span data-stu-id="4683e-502">15 seconds</span></span> | <span data-ttu-id="4683e-503">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="4683e-503">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="4683e-504">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="4683e-504">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="4683e-505">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="4683e-505">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="4683e-506">V klientovi .NET jsou hodnoty časového limitu zadány jako `TimeSpan` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="4683e-506">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="4683e-507">JavaScript</span><span class="sxs-lookup"><span data-stu-id="4683e-507">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="4683e-508">Možnost</span><span class="sxs-lookup"><span data-stu-id="4683e-508">Option</span></span> | <span data-ttu-id="4683e-509">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-509">Default value</span></span> | <span data-ttu-id="4683e-510">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-510">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="4683e-511">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="4683e-511">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="4683e-512">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="4683e-512">Timeout for server activity.</span></span> <span data-ttu-id="4683e-513">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onclose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="4683e-513">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="4683e-514">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="4683e-514">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="4683e-515">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="4683e-515">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="4683e-516">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="4683e-516">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="4683e-517">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="4683e-517">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="4683e-518">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="4683e-518">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="4683e-519">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="4683e-519">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="4683e-520">Java</span><span class="sxs-lookup"><span data-stu-id="4683e-520">Java</span></span>](#tab/java)

| <span data-ttu-id="4683e-521">Možnost</span><span class="sxs-lookup"><span data-stu-id="4683e-521">Option</span></span> | <span data-ttu-id="4683e-522">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-522">Default value</span></span> | <span data-ttu-id="4683e-523">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-523">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="4683e-524">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="4683e-524">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="4683e-525">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="4683e-525">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="4683e-526">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="4683e-526">Timeout for server activity.</span></span> <span data-ttu-id="4683e-527">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onClose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="4683e-527">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="4683e-528">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="4683e-528">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="4683e-529">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="4683e-529">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="4683e-530">15 sekund</span><span class="sxs-lookup"><span data-stu-id="4683e-530">15 seconds</span></span> | <span data-ttu-id="4683e-531">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="4683e-531">Timeout for initial server handshake.</span></span> <span data-ttu-id="4683e-532">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje `onClose` událost.</span><span class="sxs-lookup"><span data-stu-id="4683e-532">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="4683e-533">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="4683e-533">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="4683e-534">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="4683e-534">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="4683e-535">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="4683e-535">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="4683e-536">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="4683e-536">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="4683e-537">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="4683e-537">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="4683e-538">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="4683e-538">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="4683e-539">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="4683e-539">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="4683e-540">Konfigurace dalších možností</span><span class="sxs-lookup"><span data-stu-id="4683e-540">Configure additional options</span></span>

<span data-ttu-id="4683e-541">Další možnosti lze konfigurovat v `WithUrl` `withUrl` metodě (v jazyce JavaScript) na `HubConnectionBuilder` různých konfiguračních rozhraních API v `HttpHubConnectionBuilder` klientovi Java:</span><span class="sxs-lookup"><span data-stu-id="4683e-541">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="4683e-542">.NET</span><span class="sxs-lookup"><span data-stu-id="4683e-542">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="4683e-543">Možnost .NET</span><span class="sxs-lookup"><span data-stu-id="4683e-543">.NET Option</span></span> |  <span data-ttu-id="4683e-544">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-544">Default value</span></span> | <span data-ttu-id="4683e-545">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-545">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="4683e-546">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="4683e-546">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="4683e-547">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="4683e-547">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="4683e-548">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="4683e-548">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="4683e-549">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="4683e-549">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="4683e-550">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="4683e-550">Empty</span></span> | <span data-ttu-id="4683e-551">Kolekce certifikátů TLS pro odeslání na požadavky na ověření.</span><span class="sxs-lookup"><span data-stu-id="4683e-551">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="4683e-552">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="4683e-552">Empty</span></span> | <span data-ttu-id="4683e-553">Kolekce souborů cookie protokolu HTTP, které se mají odeslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="4683e-553">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="4683e-554">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="4683e-554">Empty</span></span> | <span data-ttu-id="4683e-555">Přihlašovací údaje, které se mají poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="4683e-555">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="4683e-556">5 sekund</span><span class="sxs-lookup"><span data-stu-id="4683e-556">5 seconds</span></span> | <span data-ttu-id="4683e-557">Jenom objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="4683e-557">WebSockets only.</span></span> <span data-ttu-id="4683e-558">Maximální doba, po jejímž uplynutí bude klient čekat na ukončení žádosti o uzavření.</span><span class="sxs-lookup"><span data-stu-id="4683e-558">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="4683e-559">Pokud server nepotvrdí zavření v této době, klient se odpojí.</span><span class="sxs-lookup"><span data-stu-id="4683e-559">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="4683e-560">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="4683e-560">Empty</span></span> | <span data-ttu-id="4683e-561">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="4683e-561">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="4683e-562">Delegát, který lze použít ke konfiguraci nebo nahrazení `HttpMessageHandler` používaného k odeslání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="4683e-562">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="4683e-563">Nepoužívá se pro připojení pomocí protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="4683e-563">Not used for WebSocket connections.</span></span> <span data-ttu-id="4683e-564">Tento delegát musí vracet hodnotu, která není null, a obdrží výchozí hodnotu jako parametr.</span><span class="sxs-lookup"><span data-stu-id="4683e-564">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="4683e-565">Buď změňte nastavení u této výchozí hodnoty a vraťte je, nebo vraťte novou `HttpMessageHandler` instanci.</span><span class="sxs-lookup"><span data-stu-id="4683e-565">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="4683e-566">**Při nahrazování obslužné rutiny Nezapomeňte zkopírovat nastavení, která chcete zachovat, od poskytnuté obslužné rutiny, jinak konfigurované možnosti (například soubory cookie a záhlaví) nebudou použity pro novou obslužnou rutinu.**</span><span class="sxs-lookup"><span data-stu-id="4683e-566">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="4683e-567">Proxy server HTTP, který se má použít při odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="4683e-567">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="4683e-568">Nastavte tuto logickou hodnotu pro odeslání výchozích přihlašovacích údajů pro požadavky HTTP a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="4683e-568">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="4683e-569">To umožňuje použití ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="4683e-569">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="4683e-570">Delegát, který lze použít ke konfiguraci dalších možností protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="4683e-570">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="4683e-571">Přijímá instanci [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , která se dá použít ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="4683e-571">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="4683e-572">JavaScript</span><span class="sxs-lookup"><span data-stu-id="4683e-572">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="4683e-573">Možnost JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="4683e-573">JavaScript Option</span></span> | <span data-ttu-id="4683e-574">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-574">Default Value</span></span> | <span data-ttu-id="4683e-575">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-575">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="4683e-576">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="4683e-576">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="4683e-577">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="4683e-577">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="4683e-578">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="4683e-578">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="4683e-579">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="4683e-579">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="4683e-580">Java</span><span class="sxs-lookup"><span data-stu-id="4683e-580">Java</span></span>](#tab/java)

| <span data-ttu-id="4683e-581">Možnost jazyka Java</span><span class="sxs-lookup"><span data-stu-id="4683e-581">Java Option</span></span> | <span data-ttu-id="4683e-582">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-582">Default Value</span></span> | <span data-ttu-id="4683e-583">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-583">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="4683e-584">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="4683e-584">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="4683e-585">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="4683e-585">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="4683e-586">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="4683e-586">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="4683e-587">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="4683e-587">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="4683e-588">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="4683e-588">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="4683e-589">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="4683e-589">Empty</span></span> | <span data-ttu-id="4683e-590">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="4683e-590">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="4683e-591">V klientu .NET můžete tyto možnosti upravit pomocí delegáta možností, který je k dispozici pro `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="4683e-591">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="4683e-592">V klientovi JavaScriptu lze tyto možnosti poskytnout v objektu jazyka JavaScript, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="4683e-592">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="4683e-593">V klientovi Java lze tyto možnosti nakonfigurovat s metodami `HttpHubConnectionBuilder` vrácenými z`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="4683e-593">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="4683e-594">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="4683e-594">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-3.0"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="4683e-595">Možnosti serializace JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="4683e-595">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="4683e-596">ASP.NET Core SignalR podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="4683e-596">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="4683e-597">Každý protokol má možnosti konfigurace serializace.</span><span class="sxs-lookup"><span data-stu-id="4683e-597">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="4683e-598">Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) .</span><span class="sxs-lookup"><span data-stu-id="4683e-598">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method.</span></span> <span data-ttu-id="4683e-599">`AddJsonProtocol`lze přidat po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="4683e-599">`AddJsonProtocol` can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="4683e-600">`AddJsonProtocol`Metoda přebírá delegáta, který přijímá `options` objekt.</span><span class="sxs-lookup"><span data-stu-id="4683e-600">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="4683e-601">Vlastnost [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) tohoto objektu je `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> objekt, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="4683e-601">The [PayloadSerializerOptions](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializeroptions) property on that object is a `System.Text.Json` <xref:System.Text.Json.JsonSerializerOptions> object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="4683e-602">Další informace najdete v [System.Text.Jsv dokumentaci](/dotnet/api/system.text.json).</span><span class="sxs-lookup"><span data-stu-id="4683e-602">For more information, see the [System.Text.Json documentation](/dotnet/api/system.text.json).</span></span>

<span data-ttu-id="4683e-603">Chcete-li například nakonfigurovat serializátor, aby neměnil velká a malá písmena názvů vlastností namísto výchozích názvů "camelCase", použijte následující kód v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="4683e-603">As an example, to configure the serializer to not change the casing of property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>

```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerOptions.PropertyNamingPolicy = null;
    });
```

<span data-ttu-id="4683e-604">V klientovi .NET `AddJsonProtocol` existuje stejná rozšiřující metoda v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="4683e-604">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="4683e-605">`Microsoft.Extensions.DependencyInjection`Obor názvů musí být importován, aby bylo možné přeložit metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="4683e-605">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="4683e-606">V tuto chvíli není možné konfigurovat serializaci JSON v klientovi jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4683e-606">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="switch-to-newtonsoftjson"></a><span data-ttu-id="4683e-607">Přepnout na Newtonsoft.Js</span><span class="sxs-lookup"><span data-stu-id="4683e-607">Switch to Newtonsoft.Json</span></span>

<span data-ttu-id="4683e-608">Pokud potřebujete funkce nástroje `Newtonsoft.Json` , které nejsou podporované v systému `System.Text.Json` , přečtěte si téma [Přepnutí na Newtonsoft.Jsna](xref:migration/22-to-30#switch-to-newtonsoftjson).</span><span class="sxs-lookup"><span data-stu-id="4683e-608">If you need features of `Newtonsoft.Json` that aren't supported in `System.Text.Json`, See [Switch to Newtonsoft.Json](xref:migration/22-to-30#switch-to-newtonsoftjson).</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="4683e-609">Možnosti serializace MessagePack</span><span class="sxs-lookup"><span data-stu-id="4683e-609">MessagePack serialization options</span></span>

<span data-ttu-id="4683e-610">Serializaci MessagePack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="4683e-610">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="4683e-611">Další podrobnosti najdete [v SignalR tématu MessagePack](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="4683e-611">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="4683e-612">V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi v jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4683e-612">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="4683e-613">Konfigurovat možnosti serveru</span><span class="sxs-lookup"><span data-stu-id="4683e-613">Configure server options</span></span>

<span data-ttu-id="4683e-614">Následující tabulka popisuje možnosti konfigurace SignalR Center:</span><span class="sxs-lookup"><span data-stu-id="4683e-614">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="4683e-615">Možnost</span><span class="sxs-lookup"><span data-stu-id="4683e-615">Option</span></span> | <span data-ttu-id="4683e-616">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-616">Default Value</span></span> | <span data-ttu-id="4683e-617">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-617">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="4683e-618">30 sekund</span><span class="sxs-lookup"><span data-stu-id="4683e-618">30 seconds</span></span> | <span data-ttu-id="4683e-619">Server bude předpokládat, že je klient odpojený, pokud v tomto intervalu neobdržel zprávu (včetně Keep-Alive).</span><span class="sxs-lookup"><span data-stu-id="4683e-619">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="4683e-620">Může trvat delší dobu, než je tento časový limit, aby bylo možné klienta ve skutečnosti označit jako odpojený, protože to je implementováno.</span><span class="sxs-lookup"><span data-stu-id="4683e-620">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="4683e-621">Doporučená hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="4683e-621">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="4683e-622">15 sekund</span><span class="sxs-lookup"><span data-stu-id="4683e-622">15 seconds</span></span> | <span data-ttu-id="4683e-623">Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře.</span><span class="sxs-lookup"><span data-stu-id="4683e-623">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="4683e-624">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="4683e-624">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="4683e-625">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="4683e-625">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="4683e-626">15 sekund</span><span class="sxs-lookup"><span data-stu-id="4683e-626">15 seconds</span></span> | <span data-ttu-id="4683e-627">Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="4683e-627">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="4683e-628">Při změně `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení změňte nastavení v klientovi.</span><span class="sxs-lookup"><span data-stu-id="4683e-628">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="4683e-629">Doporučená `ServerTimeout` / `serverTimeoutInMilliseconds` hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="4683e-629">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="4683e-630">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="4683e-630">All installed protocols</span></span> | <span data-ttu-id="4683e-631">Protokoly podporované tímto rozbočovačem</span><span class="sxs-lookup"><span data-stu-id="4683e-631">Protocols supported by this hub.</span></span> <span data-ttu-id="4683e-632">Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly.</span><span class="sxs-lookup"><span data-stu-id="4683e-632">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="4683e-633">Pokud se `true` do klientů vrátí podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="4683e-633">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="4683e-634">Výchozí hodnota je `false` , protože tyto zprávy o výjimce můžou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="4683e-634">The default is `false`, as these exception messages can contain sensitive information.</span></span> |
| `StreamBufferCapacity` | `10` | <span data-ttu-id="4683e-635">Maximální počet položek, které lze uložit do vyrovnávací paměti pro datové proudy pro odeslání klienta.</span><span class="sxs-lookup"><span data-stu-id="4683e-635">The maximum number of items that can be buffered for client upload streams.</span></span> <span data-ttu-id="4683e-636">Pokud je dosaženo tohoto limitu, zpracování volání je blokováno, dokud server nezpracovává položky datového proudu.</span><span class="sxs-lookup"><span data-stu-id="4683e-636">If this limit is reached, the processing of invocations is blocked until the server processes stream items.</span></span>|
| `MaximumReceiveMessageSize` | <span data-ttu-id="4683e-637">32 KB</span><span class="sxs-lookup"><span data-stu-id="4683e-637">32 KB</span></span> | <span data-ttu-id="4683e-638">Maximální velikost jedné příchozí zprávy centra</span><span class="sxs-lookup"><span data-stu-id="4683e-638">Maximum size of a single incoming hub message.</span></span> |

<span data-ttu-id="4683e-639">Možnosti lze nakonfigurovat pro všechna centra tím, že poskytnete možnosti delegáta pro `AddSignalR` volání v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="4683e-639">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="4683e-640">Možnosti pro jeden rozbočovač přepíší globální možnosti poskytované v `AddSignalR` a můžou být nakonfigurované pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="4683e-640">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="4683e-641">Rozšířené možnosti konfigurace protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="4683e-641">Advanced HTTP configuration options</span></span>

<span data-ttu-id="4683e-642">Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="4683e-642">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="4683e-643">Tyto možnosti jsou nakonfigurovány předáním delegáta [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="4683e-643">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.builder.hubendpointroutebuilderextensions.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="4683e-644">Následující tabulka popisuje možnosti konfigurace SignalR rozšířených možností protokolu HTTP v ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="4683e-644">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="4683e-645">Možnost</span><span class="sxs-lookup"><span data-stu-id="4683e-645">Option</span></span> | <span data-ttu-id="4683e-646">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-646">Default Value</span></span> | <span data-ttu-id="4683e-647">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-647">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="4683e-648">32 KB</span><span class="sxs-lookup"><span data-stu-id="4683e-648">32 KB</span></span> | <span data-ttu-id="4683e-649">Maximální počet bajtů přijatých od klienta, které jsou ukládány do vyrovnávací paměti serveru před použitím protitlaku.</span><span class="sxs-lookup"><span data-stu-id="4683e-649">The maximum number of bytes received from the client that the server buffers before applying backpressure.</span></span> <span data-ttu-id="4683e-650">Zvýšením této hodnoty umožníte, aby server přijímal větší zprávy rychleji bez použití zatížení, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="4683e-650">Increasing this value allows the server to receive larger messages more quickly without applying backpressure, but can increase memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="4683e-651">Data se automaticky shromažďují z `Authorize` atributů použitých pro třídu centra.</span><span class="sxs-lookup"><span data-stu-id="4683e-651">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="4683e-652">Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru</span><span class="sxs-lookup"><span data-stu-id="4683e-652">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="4683e-653">32 KB</span><span class="sxs-lookup"><span data-stu-id="4683e-653">32 KB</span></span> | <span data-ttu-id="4683e-654">Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru před pozorováním protitlaku.</span><span class="sxs-lookup"><span data-stu-id="4683e-654">The maximum number of bytes sent by the app that the server buffers before observing backpressure.</span></span> <span data-ttu-id="4683e-655">Zvýšením této hodnoty umožníte serveru rychleji ukládat větší zprávy bez nutnosti očekávat zatížení, ale může zvýšit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="4683e-655">Increasing this value allows the server to buffer larger messages more quickly without awaiting backpressure, but can increase memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="4683e-656">Všechny přenosy jsou povolené.</span><span class="sxs-lookup"><span data-stu-id="4683e-656">All Transports are enabled.</span></span> | <span data-ttu-id="4683e-657">Bitové příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenos, který může klient použít pro připojení.</span><span class="sxs-lookup"><span data-stu-id="4683e-657">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="4683e-658">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="4683e-658">See below.</span></span> | <span data-ttu-id="4683e-659">Další možnosti specifické pro přenos dlouhého cyklického dotazování.</span><span class="sxs-lookup"><span data-stu-id="4683e-659">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="4683e-660">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="4683e-660">See below.</span></span> | <span data-ttu-id="4683e-661">Další možnosti specifické pro přenos pomocí protokolu WebSockets</span><span class="sxs-lookup"><span data-stu-id="4683e-661">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="4683e-662">Přenos dlouhého cyklického dotazování má další možnosti, které je možné konfigurovat pomocí `LongPolling` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="4683e-662">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="4683e-663">Možnost</span><span class="sxs-lookup"><span data-stu-id="4683e-663">Option</span></span> | <span data-ttu-id="4683e-664">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-664">Default Value</span></span> | <span data-ttu-id="4683e-665">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-665">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="4683e-666">90 sekund</span><span class="sxs-lookup"><span data-stu-id="4683e-666">90 seconds</span></span> | <span data-ttu-id="4683e-667">Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jedné žádosti o cyklické dotazování.</span><span class="sxs-lookup"><span data-stu-id="4683e-667">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="4683e-668">Snížení této hodnoty způsobí, že klient bude vydávat nové požadavky na dotaz častěji.</span><span class="sxs-lookup"><span data-stu-id="4683e-668">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="4683e-669">Přenos protokolu WebSocket má další možnosti, které je možné konfigurovat pomocí `WebSockets` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="4683e-669">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="4683e-670">Možnost</span><span class="sxs-lookup"><span data-stu-id="4683e-670">Option</span></span> | <span data-ttu-id="4683e-671">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-671">Default Value</span></span> | <span data-ttu-id="4683e-672">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-672">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="4683e-673">5 sekund</span><span class="sxs-lookup"><span data-stu-id="4683e-673">5 seconds</span></span> | <span data-ttu-id="4683e-674">Pokud se po ukončení serveru aplikace v tomto časovém intervalu nepovede zavřít, připojení se ukončí.</span><span class="sxs-lookup"><span data-stu-id="4683e-674">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="4683e-675">Delegát, který lze použít k nastavení `Sec-WebSocket-Protocol` záhlaví na vlastní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="4683e-675">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="4683e-676">Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že se vrátí požadovaná hodnota.</span><span class="sxs-lookup"><span data-stu-id="4683e-676">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="4683e-677">Konfigurace možností klienta</span><span class="sxs-lookup"><span data-stu-id="4683e-677">Configure client options</span></span>

<span data-ttu-id="4683e-678">Možnosti klienta lze nakonfigurovat pro `HubConnectionBuilder` typ (k dispozici v klientech rozhraní .NET a v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="4683e-678">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="4683e-679">Je také k dispozici v klientovi Java, ale `HttpHubConnectionBuilder` podtřídou je to, co obsahuje možnosti konfigurace tvůrce a také na `HubConnection` sebe sama.</span><span class="sxs-lookup"><span data-stu-id="4683e-679">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="4683e-680">Konfigurovat protokolování</span><span class="sxs-lookup"><span data-stu-id="4683e-680">Configure logging</span></span>

<span data-ttu-id="4683e-681">Protokolování je konfigurováno v klientovi .NET pomocí `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="4683e-681">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="4683e-682">Zprostředkovatelé protokolování a filtry je možné zaregistrovat stejným způsobem jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="4683e-682">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="4683e-683">Další informace najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="4683e-683">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="4683e-684">Aby bylo možné registrovat poskytovatele protokolování, je nutné nainstalovat potřebné balíčky.</span><span class="sxs-lookup"><span data-stu-id="4683e-684">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="4683e-685">Úplný seznam najdete v části [předdefinovaná zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="4683e-685">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="4683e-686">Chcete-li například povolit protokolování konzoly, nainstalujte `Microsoft.Extensions.Logging.Console` balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="4683e-686">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="4683e-687">Zavolejte `AddConsole` metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="4683e-687">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="4683e-688">V klientovi jazyka JavaScript existuje podobná `configureLogging` metoda.</span><span class="sxs-lookup"><span data-stu-id="4683e-688">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="4683e-689">Zadejte `LogLevel` hodnotu, která určuje minimální úroveň zpráv protokolu, které mají být vyprodukovány.</span><span class="sxs-lookup"><span data-stu-id="4683e-689">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="4683e-690">Protokoly se zapisují do okna konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="4683e-690">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

<span data-ttu-id="4683e-691">Místo `LogLevel` hodnoty můžete zadat také `string` hodnotu představující název úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="4683e-691">Instead of a `LogLevel` value, you can also provide a `string` value representing a log level name.</span></span> <span data-ttu-id="4683e-692">To je užitečné při konfiguraci SignalR protokolování v prostředích, kde nemáte přístup k `LogLevel` konstantám.</span><span class="sxs-lookup"><span data-stu-id="4683e-692">This is useful when configuring SignalR logging in environments where you don't have access to the `LogLevel` constants.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging("warn")
    .build();
```

<span data-ttu-id="4683e-693">V následující tabulce jsou uvedeny dostupné úrovně protokolu.</span><span class="sxs-lookup"><span data-stu-id="4683e-693">The following table lists the available log levels.</span></span> <span data-ttu-id="4683e-694">Hodnota, kterou zadáte, `configureLogging` nastaví **minimální** úroveň protokolu, která se bude protokolovat.</span><span class="sxs-lookup"><span data-stu-id="4683e-694">The value you provide to `configureLogging` sets the **minimum** log level that will be logged.</span></span> <span data-ttu-id="4683e-695">Zprávy zaznamenané na této úrovni **nebo úrovně uvedené za ní v tabulce**budou protokolovány.</span><span class="sxs-lookup"><span data-stu-id="4683e-695">Messages logged at this level, **or the levels listed after it in the table**, will be logged.</span></span>

| <span data-ttu-id="4683e-696">Řetězec</span><span class="sxs-lookup"><span data-stu-id="4683e-696">String</span></span>                      | <span data-ttu-id="4683e-697">LogLevel</span><span class="sxs-lookup"><span data-stu-id="4683e-697">LogLevel</span></span>               |
| --------------------------- | ---------------------- |
| `trace`                     | `LogLevel.Trace`       |
| `debug`                     | `LogLevel.Debug`       |
| <span data-ttu-id="4683e-698">`info` **nebo** `information`</span><span class="sxs-lookup"><span data-stu-id="4683e-698">`info` **or** `information`</span></span> | `LogLevel.Information` |
| <span data-ttu-id="4683e-699">`warn` **nebo** `warning`</span><span class="sxs-lookup"><span data-stu-id="4683e-699">`warn` **or** `warning`</span></span>     | `LogLevel.Warning`     |
| `error`                     | `LogLevel.Error`       |
| `critical`                  | `LogLevel.Critical`    |
| `none`                      | `LogLevel.None`        |

> [!NOTE]
> <span data-ttu-id="4683e-700">Chcete-li protokolování zcela zakázat, zadejte `signalR.LogLevel.None` v `configureLogging` metodě.</span><span class="sxs-lookup"><span data-stu-id="4683e-700">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="4683e-701">Další informace o protokolování naleznete v dokumentaci k [ SignalR diagnostice](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="4683e-701">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="4683e-702">SignalRKlient Java používá k protokolování knihovnu [SLF4J](https://www.slf4j.org/) .</span><span class="sxs-lookup"><span data-stu-id="4683e-702">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="4683e-703">Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí.</span><span class="sxs-lookup"><span data-stu-id="4683e-703">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="4683e-704">Následující fragment kódu ukazuje, jak používat `java.util.logging` s SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="4683e-704">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="4683e-705">Pokud ve svých závislostech nenakonfigurujete protokolování, SLF4J načte výchozí protokolovací nástroj No-operation s následující zprávou upozornění:</span><span class="sxs-lookup"><span data-stu-id="4683e-705">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="4683e-706">Tuto možnost lze bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="4683e-706">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="4683e-707">Konfigurace povolených přenosů</span><span class="sxs-lookup"><span data-stu-id="4683e-707">Configure allowed transports</span></span>

<span data-ttu-id="4683e-708">Přenosy používané nástrojem SignalR lze konfigurovat ve `WithUrl` volání ( `withUrl` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="4683e-708">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="4683e-709">Bitové nebo hodnoty `HttpTransportType` lze použít k omezení klienta na používání pouze zadaných přenosů.</span><span class="sxs-lookup"><span data-stu-id="4683e-709">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="4683e-710">Ve výchozím nastavení jsou povolené všechny přenosy.</span><span class="sxs-lookup"><span data-stu-id="4683e-710">All transports are enabled by default.</span></span>

<span data-ttu-id="4683e-711">Pokud třeba chcete zakázat přenos událostí odeslaných serverem, ale povolit WebSockets a dlouhá připojení s dotazem:</span><span class="sxs-lookup"><span data-stu-id="4683e-711">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="4683e-712">V klientu jazyka JavaScript jsou přenosy konfigurovány nastavením `transport` pole v objektu Options, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="4683e-712">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="4683e-713">V této verzi je jediným dostupným přenosem klientský WebSocket v jazyce Java.</span><span class="sxs-lookup"><span data-stu-id="4683e-713">In this version of the Java client websockets is the only available transport.</span></span>

<span data-ttu-id="4683e-714">V klientovi Java se přenos vybere s `withTransport` metodou na `HttpHubConnectionBuilder` .</span><span class="sxs-lookup"><span data-stu-id="4683e-714">In the Java client, the transport is selected with the `withTransport` method on the `HttpHubConnectionBuilder`.</span></span> <span data-ttu-id="4683e-715">Klient Java standardně používá přenos pomocí protokolu WebSockets.</span><span class="sxs-lookup"><span data-stu-id="4683e-715">The Java client defaults to using the WebSockets transport.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withTransport(TransportEnum.WEBSOCKETS)
    .build();
```

> [!NOTE]
> <span data-ttu-id="4683e-716">SignalRKlient Java ještě nepodporuje přenosovou zálohu.</span><span class="sxs-lookup"><span data-stu-id="4683e-716">The SignalR Java client doesn't support transport fallback yet.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="4683e-717">Konfigurace ověřování nosiče</span><span class="sxs-lookup"><span data-stu-id="4683e-717">Configure bearer authentication</span></span>

<span data-ttu-id="4683e-718">K poskytnutí ověřovacích dat spolu s SignalR požadavky použijte `AccessTokenProvider` možnost ( `accessTokenFactory` v jazyce JavaScript) k určení funkce, která vrací požadovaný přístupový token.</span><span class="sxs-lookup"><span data-stu-id="4683e-718">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="4683e-719">V klientovi .NET se tento přístupový token předává jako token ověřování HTTP "Bearer" (s použitím `Authorization` hlavičky s typem `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="4683e-719">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="4683e-720">V klientu jazyka JavaScript se přístupový token používá jako nosný token, **s výjimkou** případů, kdy rozhraní API prohlížeče omezuje možnost použít hlavičky (konkrétně v požadavcích na události odeslané serverem a objekty WebSockets).</span><span class="sxs-lookup"><span data-stu-id="4683e-720">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="4683e-721">V těchto případech je přístupový token k dispozici jako hodnota řetězce dotazu `access_token` .</span><span class="sxs-lookup"><span data-stu-id="4683e-721">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="4683e-722">V klientu .NET `AccessTokenProvider` lze možnost zadat pomocí delegáta možnosti v `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="4683e-722">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="4683e-723">V klientovi JavaScriptu je přístupový token nakonfigurovaný nastavením `accessTokenFactory` pole v objektu Options v `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="4683e-723">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="4683e-724">V SignalR klientovi Java můžete nakonfigurovat nosný token, který se bude používat pro ověřování, a to poskytnutím továrny přístupového tokenu [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="4683e-724">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="4683e-725">Pomocí [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) můžete zadat [RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="4683e-725">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="4683e-726">Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.</span><span class="sxs-lookup"><span data-stu-id="4683e-726">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="4683e-727">Konfigurace možností timeout a Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="4683e-727">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="4683e-728">Další možnosti konfigurace časového limitu a chování funkce Keep-Alive jsou k dispozici na `HubConnection` samotném objektu:</span><span class="sxs-lookup"><span data-stu-id="4683e-728">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="4683e-729">.NET</span><span class="sxs-lookup"><span data-stu-id="4683e-729">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="4683e-730">Možnost</span><span class="sxs-lookup"><span data-stu-id="4683e-730">Option</span></span> | <span data-ttu-id="4683e-731">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-731">Default value</span></span> | <span data-ttu-id="4683e-732">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-732">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="4683e-733">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="4683e-733">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="4683e-734">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="4683e-734">Timeout for server activity.</span></span> <span data-ttu-id="4683e-735">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí `Closed` událost ( `onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="4683e-735">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="4683e-736">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="4683e-736">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="4683e-737">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="4683e-737">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="4683e-738">15 sekund</span><span class="sxs-lookup"><span data-stu-id="4683e-738">15 seconds</span></span> | <span data-ttu-id="4683e-739">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="4683e-739">Timeout for initial server handshake.</span></span> <span data-ttu-id="4683e-740">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a spustí `Closed` událost ( `onclose` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="4683e-740">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="4683e-741">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="4683e-741">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="4683e-742">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="4683e-742">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="4683e-743">15 sekund</span><span class="sxs-lookup"><span data-stu-id="4683e-743">15 seconds</span></span> | <span data-ttu-id="4683e-744">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="4683e-744">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="4683e-745">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="4683e-745">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="4683e-746">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="4683e-746">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="4683e-747">V klientovi .NET jsou hodnoty časového limitu zadány jako `TimeSpan` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="4683e-747">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="4683e-748">JavaScript</span><span class="sxs-lookup"><span data-stu-id="4683e-748">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="4683e-749">Možnost</span><span class="sxs-lookup"><span data-stu-id="4683e-749">Option</span></span> | <span data-ttu-id="4683e-750">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-750">Default value</span></span> | <span data-ttu-id="4683e-751">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-751">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="4683e-752">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="4683e-752">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="4683e-753">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="4683e-753">Timeout for server activity.</span></span> <span data-ttu-id="4683e-754">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onclose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="4683e-754">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="4683e-755">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="4683e-755">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="4683e-756">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="4683e-756">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="4683e-757">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="4683e-757">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="4683e-758">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="4683e-758">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="4683e-759">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="4683e-759">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="4683e-760">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="4683e-760">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="4683e-761">Java</span><span class="sxs-lookup"><span data-stu-id="4683e-761">Java</span></span>](#tab/java)

| <span data-ttu-id="4683e-762">Možnost</span><span class="sxs-lookup"><span data-stu-id="4683e-762">Option</span></span> | <span data-ttu-id="4683e-763">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-763">Default value</span></span> | <span data-ttu-id="4683e-764">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-764">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="4683e-765">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="4683e-765">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="4683e-766">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="4683e-766">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="4683e-767">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="4683e-767">Timeout for server activity.</span></span> <span data-ttu-id="4683e-768">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onClose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="4683e-768">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="4683e-769">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="4683e-769">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="4683e-770">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="4683e-770">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="4683e-771">15 sekund</span><span class="sxs-lookup"><span data-stu-id="4683e-771">15 seconds</span></span> | <span data-ttu-id="4683e-772">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="4683e-772">Timeout for initial server handshake.</span></span> <span data-ttu-id="4683e-773">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje `onClose` událost.</span><span class="sxs-lookup"><span data-stu-id="4683e-773">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="4683e-774">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="4683e-774">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="4683e-775">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="4683e-775">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="4683e-776">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="4683e-776">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="4683e-777">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="4683e-777">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="4683e-778">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="4683e-778">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="4683e-779">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="4683e-779">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="4683e-780">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="4683e-780">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="4683e-781">Konfigurace dalších možností</span><span class="sxs-lookup"><span data-stu-id="4683e-781">Configure additional options</span></span>

<span data-ttu-id="4683e-782">Další možnosti lze konfigurovat v `WithUrl` `withUrl` metodě (v jazyce JavaScript) na `HubConnectionBuilder` různých konfiguračních rozhraních API v `HttpHubConnectionBuilder` klientovi Java:</span><span class="sxs-lookup"><span data-stu-id="4683e-782">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="4683e-783">.NET</span><span class="sxs-lookup"><span data-stu-id="4683e-783">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="4683e-784">Možnost .NET</span><span class="sxs-lookup"><span data-stu-id="4683e-784">.NET Option</span></span> |  <span data-ttu-id="4683e-785">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-785">Default value</span></span> | <span data-ttu-id="4683e-786">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-786">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="4683e-787">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="4683e-787">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="4683e-788">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="4683e-788">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="4683e-789">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="4683e-789">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="4683e-790">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="4683e-790">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="4683e-791">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="4683e-791">Empty</span></span> | <span data-ttu-id="4683e-792">Kolekce certifikátů TLS pro odeslání na požadavky na ověření.</span><span class="sxs-lookup"><span data-stu-id="4683e-792">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="4683e-793">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="4683e-793">Empty</span></span> | <span data-ttu-id="4683e-794">Kolekce souborů cookie protokolu HTTP, které se mají odeslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="4683e-794">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="4683e-795">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="4683e-795">Empty</span></span> | <span data-ttu-id="4683e-796">Přihlašovací údaje, které se mají poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="4683e-796">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="4683e-797">5 sekund</span><span class="sxs-lookup"><span data-stu-id="4683e-797">5 seconds</span></span> | <span data-ttu-id="4683e-798">Jenom objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="4683e-798">WebSockets only.</span></span> <span data-ttu-id="4683e-799">Maximální doba, po jejímž uplynutí bude klient čekat na ukončení žádosti o uzavření.</span><span class="sxs-lookup"><span data-stu-id="4683e-799">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="4683e-800">Pokud server nepotvrdí zavření v této době, klient se odpojí.</span><span class="sxs-lookup"><span data-stu-id="4683e-800">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="4683e-801">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="4683e-801">Empty</span></span> | <span data-ttu-id="4683e-802">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="4683e-802">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="4683e-803">Delegát, který lze použít ke konfiguraci nebo nahrazení `HttpMessageHandler` používaného k odeslání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="4683e-803">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="4683e-804">Nepoužívá se pro připojení pomocí protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="4683e-804">Not used for WebSocket connections.</span></span> <span data-ttu-id="4683e-805">Tento delegát musí vracet hodnotu, která není null, a obdrží výchozí hodnotu jako parametr.</span><span class="sxs-lookup"><span data-stu-id="4683e-805">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="4683e-806">Buď změňte nastavení u této výchozí hodnoty a vraťte je, nebo vraťte novou `HttpMessageHandler` instanci.</span><span class="sxs-lookup"><span data-stu-id="4683e-806">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="4683e-807">**Při nahrazování obslužné rutiny Nezapomeňte zkopírovat nastavení, která chcete zachovat, od poskytnuté obslužné rutiny, jinak konfigurované možnosti (například soubory cookie a záhlaví) nebudou použity pro novou obslužnou rutinu.**</span><span class="sxs-lookup"><span data-stu-id="4683e-807">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="4683e-808">Proxy server HTTP, který se má použít při odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="4683e-808">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="4683e-809">Nastavte tuto logickou hodnotu pro odeslání výchozích přihlašovacích údajů pro požadavky HTTP a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="4683e-809">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="4683e-810">To umožňuje použití ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="4683e-810">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="4683e-811">Delegát, který lze použít ke konfiguraci dalších možností protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="4683e-811">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="4683e-812">Přijímá instanci [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , která se dá použít ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="4683e-812">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="4683e-813">JavaScript</span><span class="sxs-lookup"><span data-stu-id="4683e-813">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="4683e-814">Možnost JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="4683e-814">JavaScript Option</span></span> | <span data-ttu-id="4683e-815">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-815">Default Value</span></span> | <span data-ttu-id="4683e-816">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-816">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="4683e-817">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="4683e-817">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="4683e-818">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="4683e-818">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="4683e-819">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="4683e-819">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="4683e-820">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="4683e-820">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="4683e-821">Java</span><span class="sxs-lookup"><span data-stu-id="4683e-821">Java</span></span>](#tab/java)

| <span data-ttu-id="4683e-822">Možnost jazyka Java</span><span class="sxs-lookup"><span data-stu-id="4683e-822">Java Option</span></span> | <span data-ttu-id="4683e-823">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-823">Default Value</span></span> | <span data-ttu-id="4683e-824">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-824">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="4683e-825">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="4683e-825">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="4683e-826">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="4683e-826">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="4683e-827">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="4683e-827">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="4683e-828">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="4683e-828">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="4683e-829">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="4683e-829">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="4683e-830">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="4683e-830">Empty</span></span> | <span data-ttu-id="4683e-831">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="4683e-831">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="4683e-832">V klientu .NET můžete tyto možnosti upravit pomocí delegáta možností, který je k dispozici pro `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="4683e-832">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="4683e-833">V klientovi JavaScriptu lze tyto možnosti poskytnout v objektu jazyka JavaScript, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="4683e-833">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="4683e-834">V klientovi Java lze tyto možnosti nakonfigurovat s metodami `HttpHubConnectionBuilder` vrácenými z`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="4683e-834">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="4683e-835">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="4683e-835">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="= aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="4683e-836">Možnosti serializace JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="4683e-836">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="4683e-837">ASP.NET Core SignalR podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="4683e-837">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="4683e-838">Každý protokol má možnosti konfigurace serializace.</span><span class="sxs-lookup"><span data-stu-id="4683e-838">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="4683e-839">Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , kterou lze přidat po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v `Startup.ConfigureServices` metodě.</span><span class="sxs-lookup"><span data-stu-id="4683e-839">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="4683e-840">`AddJsonProtocol`Metoda přebírá delegáta, který přijímá `options` objekt.</span><span class="sxs-lookup"><span data-stu-id="4683e-840">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="4683e-841">Vlastnost [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) tohoto objektu je `JsonSerializerSettings` objekt JSON.NET, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="4683e-841">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="4683e-842">Další informace najdete v [dokumentaci k JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="4683e-842">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="4683e-843">Chcete-li například nakonfigurovat serializátor pro použití názvů vlastností "PascalCase" namísto výchozích názvů "camelCase", použijte následující kód v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="4683e-843">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="4683e-844">V klientovi .NET `AddJsonProtocol` existuje stejná rozšiřující metoda v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="4683e-844">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="4683e-845">`Microsoft.Extensions.DependencyInjection`Obor názvů musí být importován, aby bylo možné přeložit metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="4683e-845">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="4683e-846">V tuto chvíli není možné konfigurovat serializaci JSON v klientovi jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4683e-846">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="4683e-847">Možnosti serializace MessagePack</span><span class="sxs-lookup"><span data-stu-id="4683e-847">MessagePack serialization options</span></span>

<span data-ttu-id="4683e-848">Serializaci MessagePack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="4683e-848">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="4683e-849">Další podrobnosti najdete [v SignalR tématu MessagePack](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="4683e-849">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="4683e-850">V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi v jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4683e-850">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="4683e-851">Konfigurovat možnosti serveru</span><span class="sxs-lookup"><span data-stu-id="4683e-851">Configure server options</span></span>

<span data-ttu-id="4683e-852">Následující tabulka popisuje možnosti konfigurace SignalR Center:</span><span class="sxs-lookup"><span data-stu-id="4683e-852">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="4683e-853">Možnost</span><span class="sxs-lookup"><span data-stu-id="4683e-853">Option</span></span> | <span data-ttu-id="4683e-854">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-854">Default Value</span></span> | <span data-ttu-id="4683e-855">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-855">Description</span></span> |
| ------ | ------------- | ----------- |
| `ClientTimeoutInterval` | <span data-ttu-id="4683e-856">30 sekund</span><span class="sxs-lookup"><span data-stu-id="4683e-856">30 seconds</span></span> | <span data-ttu-id="4683e-857">Server bude předpokládat, že je klient odpojený, pokud v tomto intervalu neobdržel zprávu (včetně Keep-Alive).</span><span class="sxs-lookup"><span data-stu-id="4683e-857">The server will consider the client disconnected if it hasn't received a message (including keep-alive) in this interval.</span></span> <span data-ttu-id="4683e-858">Může trvat delší dobu, než je tento časový limit, aby bylo možné klienta ve skutečnosti označit jako odpojený, protože to je implementováno.</span><span class="sxs-lookup"><span data-stu-id="4683e-858">It could take longer than this timeout interval for the client to actually be marked disconnected, due to how this is implemented.</span></span> <span data-ttu-id="4683e-859">Doporučená hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="4683e-859">The recommended value is double the `KeepAliveInterval` value.</span></span>|
| `HandshakeTimeout` | <span data-ttu-id="4683e-860">15 sekund</span><span class="sxs-lookup"><span data-stu-id="4683e-860">15 seconds</span></span> | <span data-ttu-id="4683e-861">Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře.</span><span class="sxs-lookup"><span data-stu-id="4683e-861">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="4683e-862">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="4683e-862">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="4683e-863">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="4683e-863">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="4683e-864">15 sekund</span><span class="sxs-lookup"><span data-stu-id="4683e-864">15 seconds</span></span> | <span data-ttu-id="4683e-865">Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="4683e-865">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="4683e-866">Při změně `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení změňte nastavení v klientovi.</span><span class="sxs-lookup"><span data-stu-id="4683e-866">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="4683e-867">Doporučená `ServerTimeout` / `serverTimeoutInMilliseconds` hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="4683e-867">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="4683e-868">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="4683e-868">All installed protocols</span></span> | <span data-ttu-id="4683e-869">Protokoly podporované tímto rozbočovačem</span><span class="sxs-lookup"><span data-stu-id="4683e-869">Protocols supported by this hub.</span></span> <span data-ttu-id="4683e-870">Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly.</span><span class="sxs-lookup"><span data-stu-id="4683e-870">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="4683e-871">Pokud se `true` do klientů vrátí podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="4683e-871">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="4683e-872">Výchozí hodnota je `false` , protože tyto zprávy o výjimce můžou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="4683e-872">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="4683e-873">Možnosti lze nakonfigurovat pro všechna centra tím, že poskytnete možnosti delegáta pro `AddSignalR` volání v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="4683e-873">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="4683e-874">Možnosti pro jeden rozbočovač přepíší globální možnosti poskytované v `AddSignalR` a můžou být nakonfigurované pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="4683e-874">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="4683e-875">Rozšířené možnosti konfigurace protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="4683e-875">Advanced HTTP configuration options</span></span>

<span data-ttu-id="4683e-876">Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="4683e-876">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="4683e-877">Tyto možnosti jsou nakonfigurovány předáním delegáta [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="4683e-877">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="4683e-878">Následující tabulka popisuje možnosti konfigurace SignalR rozšířených možností protokolu HTTP v ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="4683e-878">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="4683e-879">Možnost</span><span class="sxs-lookup"><span data-stu-id="4683e-879">Option</span></span> | <span data-ttu-id="4683e-880">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-880">Default Value</span></span> | <span data-ttu-id="4683e-881">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-881">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="4683e-882">32 KB</span><span class="sxs-lookup"><span data-stu-id="4683e-882">32 KB</span></span> | <span data-ttu-id="4683e-883">Maximální počet bajtů přijatých od klienta, které jsou vyrovnávací paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="4683e-883">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="4683e-884">Zvýšením této hodnoty umožníte serveru přijímat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="4683e-884">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="4683e-885">Data se automaticky shromažďují z `Authorize` atributů použitých pro třídu centra.</span><span class="sxs-lookup"><span data-stu-id="4683e-885">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="4683e-886">Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru</span><span class="sxs-lookup"><span data-stu-id="4683e-886">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="4683e-887">32 KB</span><span class="sxs-lookup"><span data-stu-id="4683e-887">32 KB</span></span> | <span data-ttu-id="4683e-888">Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="4683e-888">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="4683e-889">Zvýšením této hodnoty umožníte serveru odesílat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="4683e-889">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="4683e-890">Všechny přenosy jsou povolené.</span><span class="sxs-lookup"><span data-stu-id="4683e-890">All Transports are enabled.</span></span> | <span data-ttu-id="4683e-891">Bitové příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenos, který může klient použít pro připojení.</span><span class="sxs-lookup"><span data-stu-id="4683e-891">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="4683e-892">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="4683e-892">See below.</span></span> | <span data-ttu-id="4683e-893">Další možnosti specifické pro přenos dlouhého cyklického dotazování.</span><span class="sxs-lookup"><span data-stu-id="4683e-893">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="4683e-894">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="4683e-894">See below.</span></span> | <span data-ttu-id="4683e-895">Další možnosti specifické pro přenos pomocí protokolu WebSockets</span><span class="sxs-lookup"><span data-stu-id="4683e-895">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="4683e-896">Přenos dlouhého cyklického dotazování má další možnosti, které je možné konfigurovat pomocí `LongPolling` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="4683e-896">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="4683e-897">Možnost</span><span class="sxs-lookup"><span data-stu-id="4683e-897">Option</span></span> | <span data-ttu-id="4683e-898">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-898">Default Value</span></span> | <span data-ttu-id="4683e-899">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-899">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="4683e-900">90 sekund</span><span class="sxs-lookup"><span data-stu-id="4683e-900">90 seconds</span></span> | <span data-ttu-id="4683e-901">Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jedné žádosti o cyklické dotazování.</span><span class="sxs-lookup"><span data-stu-id="4683e-901">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="4683e-902">Snížení této hodnoty způsobí, že klient bude vydávat nové požadavky na dotaz častěji.</span><span class="sxs-lookup"><span data-stu-id="4683e-902">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="4683e-903">Přenos protokolu WebSocket má další možnosti, které je možné konfigurovat pomocí `WebSockets` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="4683e-903">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="4683e-904">Možnost</span><span class="sxs-lookup"><span data-stu-id="4683e-904">Option</span></span> | <span data-ttu-id="4683e-905">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-905">Default Value</span></span> | <span data-ttu-id="4683e-906">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-906">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="4683e-907">5 sekund</span><span class="sxs-lookup"><span data-stu-id="4683e-907">5 seconds</span></span> | <span data-ttu-id="4683e-908">Pokud se po ukončení serveru aplikace v tomto časovém intervalu nepovede zavřít, připojení se ukončí.</span><span class="sxs-lookup"><span data-stu-id="4683e-908">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="4683e-909">Delegát, který lze použít k nastavení `Sec-WebSocket-Protocol` záhlaví na vlastní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="4683e-909">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="4683e-910">Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že se vrátí požadovaná hodnota.</span><span class="sxs-lookup"><span data-stu-id="4683e-910">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="4683e-911">Konfigurace možností klienta</span><span class="sxs-lookup"><span data-stu-id="4683e-911">Configure client options</span></span>

<span data-ttu-id="4683e-912">Možnosti klienta lze nakonfigurovat pro `HubConnectionBuilder` typ (k dispozici v klientech rozhraní .NET a v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="4683e-912">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="4683e-913">Je také k dispozici v klientovi Java, ale `HttpHubConnectionBuilder` podtřídou je to, co obsahuje možnosti konfigurace tvůrce a také na `HubConnection` sebe sama.</span><span class="sxs-lookup"><span data-stu-id="4683e-913">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="4683e-914">Konfigurovat protokolování</span><span class="sxs-lookup"><span data-stu-id="4683e-914">Configure logging</span></span>

<span data-ttu-id="4683e-915">Protokolování je konfigurováno v klientovi .NET pomocí `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="4683e-915">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="4683e-916">Zprostředkovatelé protokolování a filtry je možné zaregistrovat stejným způsobem jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="4683e-916">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="4683e-917">Další informace najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="4683e-917">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="4683e-918">Aby bylo možné registrovat poskytovatele protokolování, je nutné nainstalovat potřebné balíčky.</span><span class="sxs-lookup"><span data-stu-id="4683e-918">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="4683e-919">Úplný seznam najdete v části [předdefinovaná zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="4683e-919">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="4683e-920">Chcete-li například povolit protokolování konzoly, nainstalujte `Microsoft.Extensions.Logging.Console` balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="4683e-920">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="4683e-921">Zavolejte `AddConsole` metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="4683e-921">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="4683e-922">V klientovi jazyka JavaScript existuje podobná `configureLogging` metoda.</span><span class="sxs-lookup"><span data-stu-id="4683e-922">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="4683e-923">Zadejte `LogLevel` hodnotu, která určuje minimální úroveň zpráv protokolu, které mají být vyprodukovány.</span><span class="sxs-lookup"><span data-stu-id="4683e-923">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="4683e-924">Protokoly se zapisují do okna konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="4683e-924">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="4683e-925">Chcete-li protokolování zcela zakázat, zadejte `signalR.LogLevel.None` v `configureLogging` metodě.</span><span class="sxs-lookup"><span data-stu-id="4683e-925">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="4683e-926">Další informace o protokolování naleznete v dokumentaci k [ SignalR diagnostice](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="4683e-926">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="4683e-927">SignalRKlient Java používá k protokolování knihovnu [SLF4J](https://www.slf4j.org/) .</span><span class="sxs-lookup"><span data-stu-id="4683e-927">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="4683e-928">Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí.</span><span class="sxs-lookup"><span data-stu-id="4683e-928">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="4683e-929">Následující fragment kódu ukazuje, jak používat `java.util.logging` s SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="4683e-929">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="4683e-930">Pokud ve svých závislostech nenakonfigurujete protokolování, SLF4J načte výchozí protokolovací nástroj No-operation s následující zprávou upozornění:</span><span class="sxs-lookup"><span data-stu-id="4683e-930">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="4683e-931">Tuto možnost lze bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="4683e-931">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="4683e-932">Konfigurace povolených přenosů</span><span class="sxs-lookup"><span data-stu-id="4683e-932">Configure allowed transports</span></span>

<span data-ttu-id="4683e-933">Přenosy používané nástrojem SignalR lze konfigurovat ve `WithUrl` volání ( `withUrl` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="4683e-933">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="4683e-934">Bitové nebo hodnoty `HttpTransportType` lze použít k omezení klienta na používání pouze zadaných přenosů.</span><span class="sxs-lookup"><span data-stu-id="4683e-934">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="4683e-935">Ve výchozím nastavení jsou povolené všechny přenosy.</span><span class="sxs-lookup"><span data-stu-id="4683e-935">All transports are enabled by default.</span></span>

<span data-ttu-id="4683e-936">Pokud třeba chcete zakázat přenos událostí odeslaných serverem, ale povolit WebSockets a dlouhá připojení s dotazem:</span><span class="sxs-lookup"><span data-stu-id="4683e-936">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="4683e-937">V klientu jazyka JavaScript jsou přenosy konfigurovány nastavením `transport` pole v objektu Options, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="4683e-937">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

<span data-ttu-id="4683e-938">V této verzi je jediným dostupným přenosem klientský WebSocket v jazyce Java.</span><span class="sxs-lookup"><span data-stu-id="4683e-938">In this version of the Java client websockets is the only available transport.</span></span>

### <a name="configure-bearer-authentication"></a><span data-ttu-id="4683e-939">Konfigurace ověřování nosiče</span><span class="sxs-lookup"><span data-stu-id="4683e-939">Configure bearer authentication</span></span>

<span data-ttu-id="4683e-940">K poskytnutí ověřovacích dat spolu s SignalR požadavky použijte `AccessTokenProvider` možnost ( `accessTokenFactory` v jazyce JavaScript) k určení funkce, která vrací požadovaný přístupový token.</span><span class="sxs-lookup"><span data-stu-id="4683e-940">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="4683e-941">V klientovi .NET se tento přístupový token předává jako token ověřování HTTP "Bearer" (s použitím `Authorization` hlavičky s typem `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="4683e-941">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="4683e-942">V klientu jazyka JavaScript se přístupový token používá jako nosný token, **s výjimkou** případů, kdy rozhraní API prohlížeče omezuje možnost použít hlavičky (konkrétně v požadavcích na události odeslané serverem a objekty WebSockets).</span><span class="sxs-lookup"><span data-stu-id="4683e-942">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="4683e-943">V těchto případech je přístupový token k dispozici jako hodnota řetězce dotazu `access_token` .</span><span class="sxs-lookup"><span data-stu-id="4683e-943">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="4683e-944">V klientu .NET `AccessTokenProvider` lze možnost zadat pomocí delegáta možnosti v `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="4683e-944">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="4683e-945">V klientovi JavaScriptu je přístupový token nakonfigurovaný nastavením `accessTokenFactory` pole v objektu Options v `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="4683e-945">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="4683e-946">V SignalR klientovi Java můžete nakonfigurovat nosný token, který se bude používat pro ověřování, a to poskytnutím továrny přístupového tokenu [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="4683e-946">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="4683e-947">Pomocí [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) můžete zadat [RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="4683e-947">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="4683e-948">Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.</span><span class="sxs-lookup"><span data-stu-id="4683e-948">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="4683e-949">Konfigurace možností timeout a Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="4683e-949">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="4683e-950">Další možnosti konfigurace časového limitu a chování funkce Keep-Alive jsou k dispozici na `HubConnection` samotném objektu:</span><span class="sxs-lookup"><span data-stu-id="4683e-950">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="4683e-951">.NET</span><span class="sxs-lookup"><span data-stu-id="4683e-951">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="4683e-952">Možnost</span><span class="sxs-lookup"><span data-stu-id="4683e-952">Option</span></span> | <span data-ttu-id="4683e-953">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-953">Default value</span></span> | <span data-ttu-id="4683e-954">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-954">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="4683e-955">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="4683e-955">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="4683e-956">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="4683e-956">Timeout for server activity.</span></span> <span data-ttu-id="4683e-957">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí `Closed` událost ( `onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="4683e-957">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="4683e-958">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="4683e-958">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="4683e-959">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="4683e-959">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="4683e-960">15 sekund</span><span class="sxs-lookup"><span data-stu-id="4683e-960">15 seconds</span></span> | <span data-ttu-id="4683e-961">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="4683e-961">Timeout for initial server handshake.</span></span> <span data-ttu-id="4683e-962">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a spustí `Closed` událost ( `onclose` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="4683e-962">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="4683e-963">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="4683e-963">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="4683e-964">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="4683e-964">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="4683e-965">15 sekund</span><span class="sxs-lookup"><span data-stu-id="4683e-965">15 seconds</span></span> | <span data-ttu-id="4683e-966">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="4683e-966">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="4683e-967">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="4683e-967">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="4683e-968">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="4683e-968">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

<span data-ttu-id="4683e-969">V klientovi .NET jsou hodnoty časového limitu zadány jako `TimeSpan` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="4683e-969">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="4683e-970">JavaScript</span><span class="sxs-lookup"><span data-stu-id="4683e-970">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="4683e-971">Možnost</span><span class="sxs-lookup"><span data-stu-id="4683e-971">Option</span></span> | <span data-ttu-id="4683e-972">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-972">Default value</span></span> | <span data-ttu-id="4683e-973">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-973">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="4683e-974">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="4683e-974">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="4683e-975">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="4683e-975">Timeout for server activity.</span></span> <span data-ttu-id="4683e-976">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onclose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="4683e-976">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="4683e-977">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="4683e-977">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="4683e-978">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="4683e-978">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `keepAliveIntervalInMilliseconds` | <span data-ttu-id="4683e-979">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="4683e-979">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="4683e-980">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="4683e-980">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="4683e-981">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="4683e-981">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="4683e-982">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="4683e-982">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

# <a name="java"></a>[<span data-ttu-id="4683e-983">Java</span><span class="sxs-lookup"><span data-stu-id="4683e-983">Java</span></span>](#tab/java)

| <span data-ttu-id="4683e-984">Možnost</span><span class="sxs-lookup"><span data-stu-id="4683e-984">Option</span></span> | <span data-ttu-id="4683e-985">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-985">Default value</span></span> | <span data-ttu-id="4683e-986">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-986">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="4683e-987">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="4683e-987">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="4683e-988">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="4683e-988">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="4683e-989">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="4683e-989">Timeout for server activity.</span></span> <span data-ttu-id="4683e-990">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onClose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="4683e-990">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="4683e-991">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="4683e-991">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="4683e-992">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="4683e-992">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="4683e-993">15 sekund</span><span class="sxs-lookup"><span data-stu-id="4683e-993">15 seconds</span></span> | <span data-ttu-id="4683e-994">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="4683e-994">Timeout for initial server handshake.</span></span> <span data-ttu-id="4683e-995">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje `onClose` událost.</span><span class="sxs-lookup"><span data-stu-id="4683e-995">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="4683e-996">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="4683e-996">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="4683e-997">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="4683e-997">For more detail on the Handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| <span data-ttu-id="4683e-998">`getKeepAliveInterval` / `setKeepAliveInterval`</span><span class="sxs-lookup"><span data-stu-id="4683e-998">`getKeepAliveInterval` / `setKeepAliveInterval`</span></span> | <span data-ttu-id="4683e-999">15 sekund (15 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="4683e-999">15 seconds (15,000 milliseconds)</span></span> | <span data-ttu-id="4683e-1000">Určuje interval, ve kterém klient odesílá zprávy nástroje test.</span><span class="sxs-lookup"><span data-stu-id="4683e-1000">Determines the interval at which the client sends ping messages.</span></span> <span data-ttu-id="4683e-1001">Odesláním jakékoli zprávy z klienta se obnoví časovač na začátek intervalu.</span><span class="sxs-lookup"><span data-stu-id="4683e-1001">Sending any message from the client resets the timer to the start of the interval.</span></span> <span data-ttu-id="4683e-1002">Pokud klient neodeslal zprávu v `ClientTimeoutInterval` sadě na serveru, Server považuje klienta za odpojený.</span><span class="sxs-lookup"><span data-stu-id="4683e-1002">If the client hasn't sent a message in the `ClientTimeoutInterval` set on the server, the server considers the client disconnected.</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="4683e-1003">Konfigurace dalších možností</span><span class="sxs-lookup"><span data-stu-id="4683e-1003">Configure additional options</span></span>

<span data-ttu-id="4683e-1004">Další možnosti lze konfigurovat v `WithUrl` `withUrl` metodě (v jazyce JavaScript) na `HubConnectionBuilder` různých konfiguračních rozhraních API v `HttpHubConnectionBuilder` klientovi Java:</span><span class="sxs-lookup"><span data-stu-id="4683e-1004">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="4683e-1005">.NET</span><span class="sxs-lookup"><span data-stu-id="4683e-1005">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="4683e-1006">Možnost .NET</span><span class="sxs-lookup"><span data-stu-id="4683e-1006">.NET Option</span></span> |  <span data-ttu-id="4683e-1007">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-1007">Default value</span></span> | <span data-ttu-id="4683e-1008">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-1008">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="4683e-1009">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="4683e-1009">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="4683e-1010">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="4683e-1010">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="4683e-1011">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="4683e-1011">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="4683e-1012">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="4683e-1012">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="4683e-1013">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="4683e-1013">Empty</span></span> | <span data-ttu-id="4683e-1014">Kolekce certifikátů TLS pro odeslání na požadavky na ověření.</span><span class="sxs-lookup"><span data-stu-id="4683e-1014">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="4683e-1015">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="4683e-1015">Empty</span></span> | <span data-ttu-id="4683e-1016">Kolekce souborů cookie protokolu HTTP, které se mají odeslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="4683e-1016">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="4683e-1017">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="4683e-1017">Empty</span></span> | <span data-ttu-id="4683e-1018">Přihlašovací údaje, které se mají poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="4683e-1018">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="4683e-1019">5 sekund</span><span class="sxs-lookup"><span data-stu-id="4683e-1019">5 seconds</span></span> | <span data-ttu-id="4683e-1020">Jenom objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="4683e-1020">WebSockets only.</span></span> <span data-ttu-id="4683e-1021">Maximální doba, po jejímž uplynutí bude klient čekat na ukončení žádosti o uzavření.</span><span class="sxs-lookup"><span data-stu-id="4683e-1021">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="4683e-1022">Pokud server nepotvrdí zavření v této době, klient se odpojí.</span><span class="sxs-lookup"><span data-stu-id="4683e-1022">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="4683e-1023">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="4683e-1023">Empty</span></span> | <span data-ttu-id="4683e-1024">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="4683e-1024">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="4683e-1025">Delegát, který lze použít ke konfiguraci nebo nahrazení `HttpMessageHandler` používaného k odeslání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="4683e-1025">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="4683e-1026">Nepoužívá se pro připojení pomocí protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="4683e-1026">Not used for WebSocket connections.</span></span> <span data-ttu-id="4683e-1027">Tento delegát musí vracet hodnotu, která není null, a obdrží výchozí hodnotu jako parametr.</span><span class="sxs-lookup"><span data-stu-id="4683e-1027">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="4683e-1028">Buď změňte nastavení u této výchozí hodnoty a vraťte je, nebo vraťte novou `HttpMessageHandler` instanci.</span><span class="sxs-lookup"><span data-stu-id="4683e-1028">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="4683e-1029">**Při nahrazování obslužné rutiny Nezapomeňte zkopírovat nastavení, která chcete zachovat, od poskytnuté obslužné rutiny, jinak konfigurované možnosti (například soubory cookie a záhlaví) nebudou použity pro novou obslužnou rutinu.**</span><span class="sxs-lookup"><span data-stu-id="4683e-1029">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="4683e-1030">Proxy server HTTP, který se má použít při odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="4683e-1030">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="4683e-1031">Nastavte tuto logickou hodnotu pro odeslání výchozích přihlašovacích údajů pro požadavky HTTP a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="4683e-1031">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="4683e-1032">To umožňuje použití ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="4683e-1032">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="4683e-1033">Delegát, který lze použít ke konfiguraci dalších možností protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="4683e-1033">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="4683e-1034">Přijímá instanci [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , která se dá použít ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="4683e-1034">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="4683e-1035">JavaScript</span><span class="sxs-lookup"><span data-stu-id="4683e-1035">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="4683e-1036">Možnost JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="4683e-1036">JavaScript Option</span></span> | <span data-ttu-id="4683e-1037">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-1037">Default Value</span></span> | <span data-ttu-id="4683e-1038">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-1038">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="4683e-1039">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="4683e-1039">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="4683e-1040">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="4683e-1040">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="4683e-1041">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="4683e-1041">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="4683e-1042">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="4683e-1042">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="4683e-1043">Java</span><span class="sxs-lookup"><span data-stu-id="4683e-1043">Java</span></span>](#tab/java)

| <span data-ttu-id="4683e-1044">Možnost jazyka Java</span><span class="sxs-lookup"><span data-stu-id="4683e-1044">Java Option</span></span> | <span data-ttu-id="4683e-1045">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-1045">Default Value</span></span> | <span data-ttu-id="4683e-1046">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-1046">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="4683e-1047">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="4683e-1047">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="4683e-1048">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="4683e-1048">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="4683e-1049">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="4683e-1049">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="4683e-1050">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="4683e-1050">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="4683e-1051">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="4683e-1051">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="4683e-1052">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="4683e-1052">Empty</span></span> | <span data-ttu-id="4683e-1053">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="4683e-1053">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="4683e-1054">V klientu .NET můžete tyto možnosti upravit pomocí delegáta možností, který je k dispozici pro `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="4683e-1054">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="4683e-1055">V klientovi JavaScriptu lze tyto možnosti poskytnout v objektu jazyka JavaScript, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="4683e-1055">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="4683e-1056">V klientovi Java lze tyto možnosti nakonfigurovat s metodami `HttpHubConnectionBuilder` vrácenými z`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="4683e-1056">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="4683e-1057">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="4683e-1057">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
::: moniker range="< aspnetcore-2.2"

## <a name="jsonmessagepack-serialization-options"></a><span data-ttu-id="4683e-1058">Možnosti serializace JSON/MessagePack</span><span class="sxs-lookup"><span data-stu-id="4683e-1058">JSON/MessagePack serialization options</span></span>

<span data-ttu-id="4683e-1059">ASP.NET Core SignalR podporuje dva protokoly pro kódování zpráv: [JSON](https://www.json.org/) a [MessagePack](https://msgpack.org/index.html).</span><span class="sxs-lookup"><span data-stu-id="4683e-1059">ASP.NET Core SignalR supports two protocols for encoding messages: [JSON](https://www.json.org/) and [MessagePack](https://msgpack.org/index.html).</span></span> <span data-ttu-id="4683e-1060">Každý protokol má možnosti konfigurace serializace.</span><span class="sxs-lookup"><span data-stu-id="4683e-1060">Each protocol has serialization configuration options.</span></span>

<span data-ttu-id="4683e-1061">Serializaci JSON lze nakonfigurovat na serveru pomocí metody rozšíření [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) , kterou lze přidat po [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v `Startup.ConfigureServices` metodě.</span><span class="sxs-lookup"><span data-stu-id="4683e-1061">JSON serialization can be configured on the server using the [AddJsonProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.jsonprotocoldependencyinjectionextensions.addjsonprotocol) extension method, which can be added after [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in your `Startup.ConfigureServices` method.</span></span> <span data-ttu-id="4683e-1062">`AddJsonProtocol`Metoda přebírá delegáta, který přijímá `options` objekt.</span><span class="sxs-lookup"><span data-stu-id="4683e-1062">The `AddJsonProtocol` method takes a delegate that receives an `options` object.</span></span> <span data-ttu-id="4683e-1063">Vlastnost [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) tohoto objektu je `JsonSerializerSettings` objekt JSON.NET, který lze použít ke konfiguraci serializace argumentů a vrácených hodnot.</span><span class="sxs-lookup"><span data-stu-id="4683e-1063">The [PayloadSerializerSettings](/dotnet/api/microsoft.aspnetcore.signalr.jsonhubprotocoloptions.payloadserializersettings) property on that object is a JSON.NET `JsonSerializerSettings` object that can be used to configure serialization of arguments and return values.</span></span> <span data-ttu-id="4683e-1064">Další informace najdete v [dokumentaci k JSON.NET](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span><span class="sxs-lookup"><span data-stu-id="4683e-1064">For more information, see the [JSON.NET documentation](https://www.newtonsoft.com/json/help/html/Introduction.htm).</span></span>
 
<span data-ttu-id="4683e-1065">Chcete-li například nakonfigurovat serializátor pro použití názvů vlastností "PascalCase" namísto výchozích názvů "camelCase", použijte následující kód v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="4683e-1065">As an example, to configure the serializer to use "PascalCase" property names, instead of the default "camelCase" names, use the following code in `Startup.ConfigureServices`:</span></span>
 
```csharp
services.AddSignalR()
    .AddJsonProtocol(options => {
        options.PayloadSerializerSettings.ContractResolver =
            new DefaultContractResolver();
    });
```

<span data-ttu-id="4683e-1066">V klientovi .NET `AddJsonProtocol` existuje stejná rozšiřující metoda v [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span><span class="sxs-lookup"><span data-stu-id="4683e-1066">In the .NET client, the same `AddJsonProtocol` extension method exists on [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder).</span></span> <span data-ttu-id="4683e-1067">`Microsoft.Extensions.DependencyInjection`Obor názvů musí být importován, aby bylo možné přeložit metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="4683e-1067">The `Microsoft.Extensions.DependencyInjection` namespace must be imported to resolve the extension method:</span></span>

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
> <span data-ttu-id="4683e-1068">V tuto chvíli není možné konfigurovat serializaci JSON v klientovi jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4683e-1068">It's not possible to configure JSON serialization in the JavaScript client at this time.</span></span>

### <a name="messagepack-serialization-options"></a><span data-ttu-id="4683e-1069">Možnosti serializace MessagePack</span><span class="sxs-lookup"><span data-stu-id="4683e-1069">MessagePack serialization options</span></span>

<span data-ttu-id="4683e-1070">Serializaci MessagePack lze nakonfigurovat poskytnutím delegáta volání [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) .</span><span class="sxs-lookup"><span data-stu-id="4683e-1070">MessagePack serialization can be configured by providing a delegate to the [AddMessagePackProtocol](/dotnet/api/microsoft.extensions.dependencyinjection.msgpackprotocoldependencyinjectionextensions.addmessagepackprotocol) call.</span></span> <span data-ttu-id="4683e-1071">Další podrobnosti najdete [v SignalR tématu MessagePack](xref:signalr/messagepackhubprotocol) .</span><span class="sxs-lookup"><span data-stu-id="4683e-1071">See [MessagePack in SignalR](xref:signalr/messagepackhubprotocol) for more details.</span></span>

> [!NOTE]
> <span data-ttu-id="4683e-1072">V tuto chvíli není možné konfigurovat serializaci MessagePack v klientovi v jazyce JavaScript.</span><span class="sxs-lookup"><span data-stu-id="4683e-1072">It's not possible to configure MessagePack serialization in the JavaScript client at this time.</span></span>

## <a name="configure-server-options"></a><span data-ttu-id="4683e-1073">Konfigurovat možnosti serveru</span><span class="sxs-lookup"><span data-stu-id="4683e-1073">Configure server options</span></span>

<span data-ttu-id="4683e-1074">Následující tabulka popisuje možnosti konfigurace SignalR Center:</span><span class="sxs-lookup"><span data-stu-id="4683e-1074">The following table describes options for configuring SignalR hubs:</span></span>

| <span data-ttu-id="4683e-1075">Možnost</span><span class="sxs-lookup"><span data-stu-id="4683e-1075">Option</span></span> | <span data-ttu-id="4683e-1076">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-1076">Default Value</span></span> | <span data-ttu-id="4683e-1077">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-1077">Description</span></span> |
| ------ | ------------- | ----------- |
| `HandshakeTimeout` | <span data-ttu-id="4683e-1078">15 sekund</span><span class="sxs-lookup"><span data-stu-id="4683e-1078">15 seconds</span></span> | <span data-ttu-id="4683e-1079">Pokud klient v tomto časovém intervalu nepošle počáteční zprávu handshake, připojení se zavře.</span><span class="sxs-lookup"><span data-stu-id="4683e-1079">If the client doesn't send an initial handshake message within this time interval, the connection is closed.</span></span> <span data-ttu-id="4683e-1080">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="4683e-1080">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="4683e-1081">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="4683e-1081">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |
| `KeepAliveInterval` | <span data-ttu-id="4683e-1082">15 sekund</span><span class="sxs-lookup"><span data-stu-id="4683e-1082">15 seconds</span></span> | <span data-ttu-id="4683e-1083">Pokud server do tohoto intervalu neodeslal zprávu, odešle se automaticky zpráva s potvrzením, aby bylo připojení otevřené.</span><span class="sxs-lookup"><span data-stu-id="4683e-1083">If the server hasn't sent a message within this interval, a ping message is sent automatically to keep the connection open.</span></span> <span data-ttu-id="4683e-1084">Při změně `KeepAliveInterval` `ServerTimeout` / `serverTimeoutInMilliseconds` nastavení změňte nastavení v klientovi.</span><span class="sxs-lookup"><span data-stu-id="4683e-1084">When changing `KeepAliveInterval`, change the `ServerTimeout`/`serverTimeoutInMilliseconds` setting on the client.</span></span> <span data-ttu-id="4683e-1085">Doporučená `ServerTimeout` / `serverTimeoutInMilliseconds` hodnota je dvojnásobná `KeepAliveInterval` hodnota.</span><span class="sxs-lookup"><span data-stu-id="4683e-1085">The recommended `ServerTimeout`/`serverTimeoutInMilliseconds` value is double the `KeepAliveInterval` value.</span></span>  |
| `SupportedProtocols` | <span data-ttu-id="4683e-1086">Všechny nainstalované protokoly</span><span class="sxs-lookup"><span data-stu-id="4683e-1086">All installed protocols</span></span> | <span data-ttu-id="4683e-1087">Protokoly podporované tímto rozbočovačem</span><span class="sxs-lookup"><span data-stu-id="4683e-1087">Protocols supported by this hub.</span></span> <span data-ttu-id="4683e-1088">Ve výchozím nastavení jsou povoleny všechny protokoly zaregistrované na serveru, ale protokoly je možné z tohoto seznamu odebrat, aby byly pro jednotlivá centra zakázané konkrétní protokoly.</span><span class="sxs-lookup"><span data-stu-id="4683e-1088">By default, all protocols registered on the server are allowed, but protocols can be removed from this list to disable specific protocols for individual hubs.</span></span> |
| `EnableDetailedErrors` | `false` | <span data-ttu-id="4683e-1089">Pokud se `true` do klientů vrátí podrobné zprávy o výjimce, pokud je vyvolána výjimka v metodě rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="4683e-1089">If `true`, detailed exception messages are returned to clients when an exception is thrown in a Hub method.</span></span> <span data-ttu-id="4683e-1090">Výchozí hodnota je `false` , protože tyto zprávy o výjimce můžou obsahovat citlivé informace.</span><span class="sxs-lookup"><span data-stu-id="4683e-1090">The default is `false`, as these exception messages can contain sensitive information.</span></span> |

<span data-ttu-id="4683e-1091">Možnosti lze nakonfigurovat pro všechna centra tím, že poskytnete možnosti delegáta pro `AddSignalR` volání v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="4683e-1091">Options can be configured for all hubs by providing an options delegate to the `AddSignalR` call in `Startup.ConfigureServices`.</span></span>

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

<span data-ttu-id="4683e-1092">Možnosti pro jeden rozbočovač přepíší globální možnosti poskytované v `AddSignalR` a můžou být nakonfigurované pomocí <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*> :</span><span class="sxs-lookup"><span data-stu-id="4683e-1092">Options for a single hub override the global options provided in `AddSignalR` and can be configured using <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddHubOptions*>:</span></span>

```csharp
services.AddSignalR().AddHubOptions<ChatHub>(options =>
{
    options.EnableDetailedErrors = true;
});
```

### <a name="advanced-http-configuration-options"></a><span data-ttu-id="4683e-1093">Rozšířené možnosti konfigurace protokolu HTTP</span><span class="sxs-lookup"><span data-stu-id="4683e-1093">Advanced HTTP configuration options</span></span>

<span data-ttu-id="4683e-1094">Slouží `HttpConnectionDispatcherOptions` ke konfiguraci upřesňujících nastavení souvisejících s přenosy a správou vyrovnávací paměti.</span><span class="sxs-lookup"><span data-stu-id="4683e-1094">Use `HttpConnectionDispatcherOptions` to configure advanced settings related to transports and memory buffer management.</span></span> <span data-ttu-id="4683e-1095">Tyto možnosti jsou nakonfigurovány předáním delegáta [do \<T> MapHub](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) v `Startup.Configure` .</span><span class="sxs-lookup"><span data-stu-id="4683e-1095">These options are configured by passing a delegate to [MapHub\<T>](/dotnet/api/microsoft.aspnetcore.signalr.hubroutebuilder.maphub) in `Startup.Configure`.</span></span>

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

<span data-ttu-id="4683e-1096">Následující tabulka popisuje možnosti konfigurace SignalR rozšířených možností protokolu HTTP v ASP.NET Core:</span><span class="sxs-lookup"><span data-stu-id="4683e-1096">The following table describes options for configuring ASP.NET Core SignalR's advanced HTTP options:</span></span>

| <span data-ttu-id="4683e-1097">Možnost</span><span class="sxs-lookup"><span data-stu-id="4683e-1097">Option</span></span> | <span data-ttu-id="4683e-1098">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-1098">Default Value</span></span> | <span data-ttu-id="4683e-1099">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-1099">Description</span></span> |
| ------ | ------------- | ----------- |
| `ApplicationMaxBufferSize` | <span data-ttu-id="4683e-1100">32 KB</span><span class="sxs-lookup"><span data-stu-id="4683e-1100">32 KB</span></span> | <span data-ttu-id="4683e-1101">Maximální počet bajtů přijatých od klienta, které jsou vyrovnávací paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="4683e-1101">The maximum number of bytes received from the client that the server buffers.</span></span> <span data-ttu-id="4683e-1102">Zvýšením této hodnoty umožníte serveru přijímat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="4683e-1102">Increasing this value allows the server to receive larger messages, but can negatively impact memory consumption.</span></span> |
| `AuthorizationData` | <span data-ttu-id="4683e-1103">Data se automaticky shromažďují z `Authorize` atributů použitých pro třídu centra.</span><span class="sxs-lookup"><span data-stu-id="4683e-1103">Data automatically gathered from the `Authorize` attributes applied to the Hub class.</span></span> | <span data-ttu-id="4683e-1104">Seznam objektů [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) , pomocí kterých se určí, jestli je klient autorizovaný pro připojení k centru</span><span class="sxs-lookup"><span data-stu-id="4683e-1104">A list of [IAuthorizeData](/dotnet/api/microsoft.aspnetcore.authorization.iauthorizedata) objects used to determine if a client is authorized to connect to the hub.</span></span> |
| `TransportMaxBufferSize` | <span data-ttu-id="4683e-1105">32 KB</span><span class="sxs-lookup"><span data-stu-id="4683e-1105">32 KB</span></span> | <span data-ttu-id="4683e-1106">Maximální počet bajtů odeslaných aplikací, které jsou vyrovnávací paměti serveru.</span><span class="sxs-lookup"><span data-stu-id="4683e-1106">The maximum number of bytes sent by the app that the server buffers.</span></span> <span data-ttu-id="4683e-1107">Zvýšením této hodnoty umožníte serveru odesílat větší zprávy, ale můžou negativně ovlivnit spotřebu paměti.</span><span class="sxs-lookup"><span data-stu-id="4683e-1107">Increasing this value allows the server to send larger messages, but can negatively impact memory consumption.</span></span> |
| `Transports` | <span data-ttu-id="4683e-1108">Všechny přenosy jsou povolené.</span><span class="sxs-lookup"><span data-stu-id="4683e-1108">All Transports are enabled.</span></span> | <span data-ttu-id="4683e-1109">Bitové příznaky výčtu `HttpTransportType` hodnot, které mohou omezit přenos, který může klient použít pro připojení.</span><span class="sxs-lookup"><span data-stu-id="4683e-1109">A bit flags enum of `HttpTransportType` values that can restrict the transports a client can use to connect.</span></span> |
| `LongPolling` | <span data-ttu-id="4683e-1110">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="4683e-1110">See below.</span></span> | <span data-ttu-id="4683e-1111">Další možnosti specifické pro přenos dlouhého cyklického dotazování.</span><span class="sxs-lookup"><span data-stu-id="4683e-1111">Additional options specific to the Long Polling transport.</span></span> |
| `WebSockets` | <span data-ttu-id="4683e-1112">Viz níže.</span><span class="sxs-lookup"><span data-stu-id="4683e-1112">See below.</span></span> | <span data-ttu-id="4683e-1113">Další možnosti specifické pro přenos pomocí protokolu WebSockets</span><span class="sxs-lookup"><span data-stu-id="4683e-1113">Additional options specific to the WebSockets transport.</span></span> |

<span data-ttu-id="4683e-1114">Přenos dlouhého cyklického dotazování má další možnosti, které je možné konfigurovat pomocí `LongPolling` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="4683e-1114">The Long Polling transport has additional options that can be configured using the `LongPolling` property:</span></span>

| <span data-ttu-id="4683e-1115">Možnost</span><span class="sxs-lookup"><span data-stu-id="4683e-1115">Option</span></span> | <span data-ttu-id="4683e-1116">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-1116">Default Value</span></span> | <span data-ttu-id="4683e-1117">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-1117">Description</span></span> |
| ------ | ------------- | ----------- |
| `PollTimeout` | <span data-ttu-id="4683e-1118">90 sekund</span><span class="sxs-lookup"><span data-stu-id="4683e-1118">90 seconds</span></span> | <span data-ttu-id="4683e-1119">Maximální doba, po kterou server čeká na odeslání zprávy klientovi před ukončením jedné žádosti o cyklické dotazování.</span><span class="sxs-lookup"><span data-stu-id="4683e-1119">The maximum amount of time the server waits for a message to send to the client before terminating a single poll request.</span></span> <span data-ttu-id="4683e-1120">Snížení této hodnoty způsobí, že klient bude vydávat nové požadavky na dotaz častěji.</span><span class="sxs-lookup"><span data-stu-id="4683e-1120">Decreasing this value causes the client to issue new poll requests more frequently.</span></span> |

<span data-ttu-id="4683e-1121">Přenos protokolu WebSocket má další možnosti, které je možné konfigurovat pomocí `WebSockets` vlastnosti:</span><span class="sxs-lookup"><span data-stu-id="4683e-1121">The WebSocket transport has additional options that can be configured using the `WebSockets` property:</span></span>

| <span data-ttu-id="4683e-1122">Možnost</span><span class="sxs-lookup"><span data-stu-id="4683e-1122">Option</span></span> | <span data-ttu-id="4683e-1123">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-1123">Default Value</span></span> | <span data-ttu-id="4683e-1124">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-1124">Description</span></span> |
| ------ | ------------- | ----------- |
| `CloseTimeout` | <span data-ttu-id="4683e-1125">5 sekund</span><span class="sxs-lookup"><span data-stu-id="4683e-1125">5 seconds</span></span> | <span data-ttu-id="4683e-1126">Pokud se po ukončení serveru aplikace v tomto časovém intervalu nepovede zavřít, připojení se ukončí.</span><span class="sxs-lookup"><span data-stu-id="4683e-1126">After the server closes, if the client fails to close within this time interval, the connection is terminated.</span></span> |
| `SubProtocolSelector` | `null` | <span data-ttu-id="4683e-1127">Delegát, který lze použít k nastavení `Sec-WebSocket-Protocol` záhlaví na vlastní hodnotu.</span><span class="sxs-lookup"><span data-stu-id="4683e-1127">A delegate that can be used to set the `Sec-WebSocket-Protocol` header to a custom value.</span></span> <span data-ttu-id="4683e-1128">Delegát obdrží hodnoty požadované klientem jako vstup a očekává se, že se vrátí požadovaná hodnota.</span><span class="sxs-lookup"><span data-stu-id="4683e-1128">The delegate receives the values requested by the client as input and is expected to return the desired value.</span></span> |

## <a name="configure-client-options"></a><span data-ttu-id="4683e-1129">Konfigurace možností klienta</span><span class="sxs-lookup"><span data-stu-id="4683e-1129">Configure client options</span></span>

<span data-ttu-id="4683e-1130">Možnosti klienta lze nakonfigurovat pro `HubConnectionBuilder` typ (k dispozici v klientech rozhraní .NET a v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="4683e-1130">Client options can be configured on the `HubConnectionBuilder` type (available in the .NET and JavaScript clients).</span></span> <span data-ttu-id="4683e-1131">Je také k dispozici v klientovi Java, ale `HttpHubConnectionBuilder` podtřídou je to, co obsahuje možnosti konfigurace tvůrce a také na `HubConnection` sebe sama.</span><span class="sxs-lookup"><span data-stu-id="4683e-1131">It's also available in the Java client, but the `HttpHubConnectionBuilder` subclass is what contains the builder configuration options, as well as on the `HubConnection` itself.</span></span>

### <a name="configure-logging"></a><span data-ttu-id="4683e-1132">Konfigurovat protokolování</span><span class="sxs-lookup"><span data-stu-id="4683e-1132">Configure logging</span></span>

<span data-ttu-id="4683e-1133">Protokolování je konfigurováno v klientovi .NET pomocí `ConfigureLogging` metody.</span><span class="sxs-lookup"><span data-stu-id="4683e-1133">Logging is configured in the .NET Client using the `ConfigureLogging` method.</span></span> <span data-ttu-id="4683e-1134">Zprostředkovatelé protokolování a filtry je možné zaregistrovat stejným způsobem jako na serveru.</span><span class="sxs-lookup"><span data-stu-id="4683e-1134">Logging providers and filters can be registered in the same way as they are on the server.</span></span> <span data-ttu-id="4683e-1135">Další informace najdete v dokumentaci k [protokolování ASP.NET Core](xref:fundamentals/logging/index) .</span><span class="sxs-lookup"><span data-stu-id="4683e-1135">See the [Logging in ASP.NET Core](xref:fundamentals/logging/index) documentation for more information.</span></span>

> [!NOTE]
> <span data-ttu-id="4683e-1136">Aby bylo možné registrovat poskytovatele protokolování, je nutné nainstalovat potřebné balíčky.</span><span class="sxs-lookup"><span data-stu-id="4683e-1136">In order to register Logging providers, you must install the necessary packages.</span></span> <span data-ttu-id="4683e-1137">Úplný seznam najdete v části [předdefinovaná zprostředkovatelé protokolování](xref:fundamentals/logging/index#built-in-logging-providers) v dokumentaci.</span><span class="sxs-lookup"><span data-stu-id="4683e-1137">See the [Built-in logging providers](xref:fundamentals/logging/index#built-in-logging-providers) section of the docs for a full list.</span></span>

<span data-ttu-id="4683e-1138">Chcete-li například povolit protokolování konzoly, nainstalujte `Microsoft.Extensions.Logging.Console` balíček NuGet.</span><span class="sxs-lookup"><span data-stu-id="4683e-1138">For example, to enable Console logging, install the `Microsoft.Extensions.Logging.Console` NuGet package.</span></span> <span data-ttu-id="4683e-1139">Zavolejte `AddConsole` metodu rozšíření:</span><span class="sxs-lookup"><span data-stu-id="4683e-1139">Call the `AddConsole` extension method:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub")
    .ConfigureLogging(logging => {
        logging.SetMinimumLevel(LogLevel.Information);
        logging.AddConsole();
    })
    .Build();
```

<span data-ttu-id="4683e-1140">V klientovi jazyka JavaScript existuje podobná `configureLogging` metoda.</span><span class="sxs-lookup"><span data-stu-id="4683e-1140">In the JavaScript client, a similar `configureLogging` method exists.</span></span> <span data-ttu-id="4683e-1141">Zadejte `LogLevel` hodnotu, která určuje minimální úroveň zpráv protokolu, které mají být vyprodukovány.</span><span class="sxs-lookup"><span data-stu-id="4683e-1141">Provide a `LogLevel` value indicating the minimum level of log messages to produce.</span></span> <span data-ttu-id="4683e-1142">Protokoly se zapisují do okna konzoly prohlížeče.</span><span class="sxs-lookup"><span data-stu-id="4683e-1142">Logs are written to the browser console window.</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .configureLogging(signalR.LogLevel.Information)
    .build();
```

> [!NOTE]
> <span data-ttu-id="4683e-1143">Chcete-li protokolování zcela zakázat, zadejte `signalR.LogLevel.None` v `configureLogging` metodě.</span><span class="sxs-lookup"><span data-stu-id="4683e-1143">To disable logging entirely, specify `signalR.LogLevel.None` in the `configureLogging` method.</span></span>

<span data-ttu-id="4683e-1144">Další informace o protokolování naleznete v dokumentaci k [ SignalR diagnostice](xref:signalr/diagnostics).</span><span class="sxs-lookup"><span data-stu-id="4683e-1144">For more information on logging, see the [SignalR Diagnostics documentation](xref:signalr/diagnostics).</span></span>

<span data-ttu-id="4683e-1145">SignalRKlient Java používá k protokolování knihovnu [SLF4J](https://www.slf4j.org/) .</span><span class="sxs-lookup"><span data-stu-id="4683e-1145">The SignalR Java client uses the [SLF4J](https://www.slf4j.org/) library for logging.</span></span> <span data-ttu-id="4683e-1146">Jedná se o rozhraní API pro protokolování na vysoké úrovni, které umožňuje uživatelům knihovny zvolit si vlastní specifickou implementaci protokolování, a to tak, že se do konkrétní závislosti protokolování přinášejí.</span><span class="sxs-lookup"><span data-stu-id="4683e-1146">It's a high-level logging API that allows users of the library to chose their own specific logging implementation by bringing in a specific logging dependency.</span></span> <span data-ttu-id="4683e-1147">Následující fragment kódu ukazuje, jak používat `java.util.logging` s SignalR klientem Java.</span><span class="sxs-lookup"><span data-stu-id="4683e-1147">The following code snippet shows how to use `java.util.logging` with the SignalR Java client.</span></span>

```gradle
implementation 'org.slf4j:slf4j-jdk14:1.7.25'
```

<span data-ttu-id="4683e-1148">Pokud ve svých závislostech nenakonfigurujete protokolování, SLF4J načte výchozí protokolovací nástroj No-operation s následující zprávou upozornění:</span><span class="sxs-lookup"><span data-stu-id="4683e-1148">If you don't configure logging in your dependencies, SLF4J loads a default no-operation logger with the following warning message:</span></span>

```
SLF4J: Failed to load class "org.slf4j.impl.StaticLoggerBinder".
SLF4J: Defaulting to no-operation (NOP) logger implementation
SLF4J: See http://www.slf4j.org/codes.html#StaticLoggerBinder for further details.
```

<span data-ttu-id="4683e-1149">Tuto možnost lze bezpečně ignorovat.</span><span class="sxs-lookup"><span data-stu-id="4683e-1149">This can safely be ignored.</span></span>

### <a name="configure-allowed-transports"></a><span data-ttu-id="4683e-1150">Konfigurace povolených přenosů</span><span class="sxs-lookup"><span data-stu-id="4683e-1150">Configure allowed transports</span></span>

<span data-ttu-id="4683e-1151">Přenosy používané nástrojem SignalR lze konfigurovat ve `WithUrl` volání ( `withUrl` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="4683e-1151">The transports used by SignalR can be configured in the `WithUrl` call (`withUrl` in JavaScript).</span></span> <span data-ttu-id="4683e-1152">Bitové nebo hodnoty `HttpTransportType` lze použít k omezení klienta na používání pouze zadaných přenosů.</span><span class="sxs-lookup"><span data-stu-id="4683e-1152">A bitwise-OR of the values of `HttpTransportType` can be used to restrict the client to only use the specified transports.</span></span> <span data-ttu-id="4683e-1153">Ve výchozím nastavení jsou povolené všechny přenosy.</span><span class="sxs-lookup"><span data-stu-id="4683e-1153">All transports are enabled by default.</span></span>

<span data-ttu-id="4683e-1154">Pokud třeba chcete zakázat přenos událostí odeslaných serverem, ale povolit WebSockets a dlouhá připojení s dotazem:</span><span class="sxs-lookup"><span data-stu-id="4683e-1154">For example, to disable the Server-Sent Events transport, but allow WebSockets and Long Polling connections:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", HttpTransportType.WebSockets | HttpTransportType.LongPolling)
    .Build();
```

<span data-ttu-id="4683e-1155">V klientu jazyka JavaScript jsou přenosy konfigurovány nastavením `transport` pole v objektu Options, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="4683e-1155">In the JavaScript client, transports are configured by setting the `transport` field on the options object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", { transport: signalR.HttpTransportType.WebSockets | signalR.HttpTransportType.LongPolling })
    .build();
```

### <a name="configure-bearer-authentication"></a><span data-ttu-id="4683e-1156">Konfigurace ověřování nosiče</span><span class="sxs-lookup"><span data-stu-id="4683e-1156">Configure bearer authentication</span></span>

<span data-ttu-id="4683e-1157">K poskytnutí ověřovacích dat spolu s SignalR požadavky použijte `AccessTokenProvider` možnost ( `accessTokenFactory` v jazyce JavaScript) k určení funkce, která vrací požadovaný přístupový token.</span><span class="sxs-lookup"><span data-stu-id="4683e-1157">To provide authentication data along with SignalR requests, use the `AccessTokenProvider` option (`accessTokenFactory` in JavaScript) to specify a function that returns the desired access token.</span></span> <span data-ttu-id="4683e-1158">V klientovi .NET se tento přístupový token předává jako token ověřování HTTP "Bearer" (s použitím `Authorization` hlavičky s typem `Bearer` ).</span><span class="sxs-lookup"><span data-stu-id="4683e-1158">In the .NET Client, this access token is passed in as an HTTP "Bearer Authentication" token (Using the `Authorization` header with a type of `Bearer`).</span></span> <span data-ttu-id="4683e-1159">V klientu jazyka JavaScript se přístupový token používá jako nosný token, **s výjimkou** případů, kdy rozhraní API prohlížeče omezuje možnost použít hlavičky (konkrétně v požadavcích na události odeslané serverem a objekty WebSockets).</span><span class="sxs-lookup"><span data-stu-id="4683e-1159">In the JavaScript client, the access token is used as a Bearer token, **except** in a few cases where browser APIs restrict the ability to apply headers (specifically, in Server-Sent Events and WebSockets requests).</span></span> <span data-ttu-id="4683e-1160">V těchto případech je přístupový token k dispozici jako hodnota řetězce dotazu `access_token` .</span><span class="sxs-lookup"><span data-stu-id="4683e-1160">In these cases, the access token is provided as a query string value `access_token`.</span></span>

<span data-ttu-id="4683e-1161">V klientu .NET `AccessTokenProvider` lze možnost zadat pomocí delegáta možnosti v `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="4683e-1161">In the .NET client, the `AccessTokenProvider` option can be specified using the options delegate in `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.AccessTokenProvider = async () => {
            // Get and return the access token.
        };
    })
    .Build();
```

<span data-ttu-id="4683e-1162">V klientovi JavaScriptu je přístupový token nakonfigurovaný nastavením `accessTokenFactory` pole v objektu Options v `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="4683e-1162">In the JavaScript client, the access token is configured by setting the `accessTokenFactory` field on the options object in `withUrl`:</span></span>

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

<span data-ttu-id="4683e-1163">V SignalR klientovi Java můžete nakonfigurovat nosný token, který se bude používat pro ověřování, a to poskytnutím továrny přístupového tokenu [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span><span class="sxs-lookup"><span data-stu-id="4683e-1163">In the SignalR Java client, you can configure a bearer token to use for authentication by providing an access token factory to the [HttpHubConnectionBuilder](/java/api/com.microsoft.signalr._http_hub_connection_builder?view=aspnet-signalr-java).</span></span> <span data-ttu-id="4683e-1164">Pomocí [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) můžete zadat [RxJava](https://github.com/ReactiveX/RxJava) [Single \<String> ](https://reactivex.io/documentation/single.html).</span><span class="sxs-lookup"><span data-stu-id="4683e-1164">Use [withAccessTokenFactory](/java/api/com.microsoft.signalr._http_hub_connection_builder.withaccesstokenprovider?view=aspnet-signalr-java#com_microsoft_signalr__http_hub_connection_builder_withAccessTokenProvider_Single_String__) to provide an [RxJava](https://github.com/ReactiveX/RxJava) [Single\<String>](https://reactivex.io/documentation/single.html).</span></span> <span data-ttu-id="4683e-1165">Při volání metody [Single. odklad](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-)můžete napsat logiku pro vytvoření přístupových tokenů pro klienta.</span><span class="sxs-lookup"><span data-stu-id="4683e-1165">With a call to [Single.defer](https://reactivex.io/RxJava/javadoc/io/reactivex/Single.html#defer-java.util.concurrent.Callable-), you can write logic to produce access tokens for your client.</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
    .withAccessTokenProvider(Single.defer(() -> {
        // Your logic here.
        return Single.just("An Access Token");
    })).build();
```

### <a name="configure-timeout-and-keep-alive-options"></a><span data-ttu-id="4683e-1166">Konfigurace možností timeout a Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="4683e-1166">Configure timeout and keep-alive options</span></span>

<span data-ttu-id="4683e-1167">Další možnosti konfigurace časového limitu a chování funkce Keep-Alive jsou k dispozici na `HubConnection` samotném objektu:</span><span class="sxs-lookup"><span data-stu-id="4683e-1167">Additional options for configuring timeout and keep-alive behavior are available on the `HubConnection` object itself:</span></span>

# <a name="net"></a>[<span data-ttu-id="4683e-1168">.NET</span><span class="sxs-lookup"><span data-stu-id="4683e-1168">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="4683e-1169">Možnost</span><span class="sxs-lookup"><span data-stu-id="4683e-1169">Option</span></span> | <span data-ttu-id="4683e-1170">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-1170">Default value</span></span> | <span data-ttu-id="4683e-1171">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-1171">Description</span></span> |
| ------ | ------------- | ----------- |
| `ServerTimeout` | <span data-ttu-id="4683e-1172">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="4683e-1172">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="4683e-1173">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="4683e-1173">Timeout for server activity.</span></span> <span data-ttu-id="4683e-1174">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a spustí `Closed` událost ( `onclose` v JavaScriptu).</span><span class="sxs-lookup"><span data-stu-id="4683e-1174">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="4683e-1175">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="4683e-1175">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="4683e-1176">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="4683e-1176">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |
| `HandshakeTimeout` | <span data-ttu-id="4683e-1177">15 sekund</span><span class="sxs-lookup"><span data-stu-id="4683e-1177">15 seconds</span></span> | <span data-ttu-id="4683e-1178">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="4683e-1178">Timeout for initial server handshake.</span></span> <span data-ttu-id="4683e-1179">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a spustí `Closed` událost ( `onclose` v jazyce JavaScript).</span><span class="sxs-lookup"><span data-stu-id="4683e-1179">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `Closed` event (`onclose` in JavaScript).</span></span> <span data-ttu-id="4683e-1180">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="4683e-1180">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="4683e-1181">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="4683e-1181">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

<span data-ttu-id="4683e-1182">V klientovi .NET jsou hodnoty časového limitu zadány jako `TimeSpan` hodnoty.</span><span class="sxs-lookup"><span data-stu-id="4683e-1182">In the .NET Client, timeout values are specified as `TimeSpan` values.</span></span>

# <a name="javascript"></a>[<span data-ttu-id="4683e-1183">JavaScript</span><span class="sxs-lookup"><span data-stu-id="4683e-1183">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="4683e-1184">Možnost</span><span class="sxs-lookup"><span data-stu-id="4683e-1184">Option</span></span> | <span data-ttu-id="4683e-1185">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-1185">Default value</span></span> | <span data-ttu-id="4683e-1186">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-1186">Description</span></span> |
| ------ | ------------- | ----------- |
| `serverTimeoutInMilliseconds` | <span data-ttu-id="4683e-1187">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="4683e-1187">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="4683e-1188">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="4683e-1188">Timeout for server activity.</span></span> <span data-ttu-id="4683e-1189">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onclose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="4683e-1189">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onclose` event.</span></span> <span data-ttu-id="4683e-1190">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="4683e-1190">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="4683e-1191">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit na příkazy pro zadání času.</span><span class="sxs-lookup"><span data-stu-id="4683e-1191">The recommended value is a number at least double the server's `KeepAliveInterval` value to allow time for pings to arrive.</span></span> |

# <a name="java"></a>[<span data-ttu-id="4683e-1192">Java</span><span class="sxs-lookup"><span data-stu-id="4683e-1192">Java</span></span>](#tab/java)

| <span data-ttu-id="4683e-1193">Možnost</span><span class="sxs-lookup"><span data-stu-id="4683e-1193">Option</span></span> | <span data-ttu-id="4683e-1194">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-1194">Default value</span></span> | <span data-ttu-id="4683e-1195">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-1195">Description</span></span> |
| ------ | ------------- | ----------- |
| <span data-ttu-id="4683e-1196">`getServerTimeout` / `setServerTimeout`</span><span class="sxs-lookup"><span data-stu-id="4683e-1196">`getServerTimeout` / `setServerTimeout`</span></span> | <span data-ttu-id="4683e-1197">30 sekund (30 000 milisekund)</span><span class="sxs-lookup"><span data-stu-id="4683e-1197">30 seconds (30,000 milliseconds)</span></span> | <span data-ttu-id="4683e-1198">Vypršel časový limit aktivity serveru.</span><span class="sxs-lookup"><span data-stu-id="4683e-1198">Timeout for server activity.</span></span> <span data-ttu-id="4683e-1199">Pokud server v tomto intervalu neodeslal zprávu, klient považuje server za odpojený a `onClose` událost spustí.</span><span class="sxs-lookup"><span data-stu-id="4683e-1199">If the server hasn't sent a message in this interval, the client considers the server disconnected and triggers the `onClose` event.</span></span> <span data-ttu-id="4683e-1200">Tato hodnota musí být dostatečně velká, aby bylo možné odeslat zprávu s upozorněním na e-mail ze serveru **a** klienta přijmout v intervalu časového limitu.</span><span class="sxs-lookup"><span data-stu-id="4683e-1200">This value must be large enough for a ping message to be sent from the server **and** received by the client within the timeout interval.</span></span> <span data-ttu-id="4683e-1201">Doporučená hodnota je číslo alespoň dvojnásobku `KeepAliveInterval` hodnoty serveru, aby bylo možné dorazit k příkazům k zadání času.</span><span class="sxs-lookup"><span data-stu-id="4683e-1201">The recommended value is a number at least double the server's `KeepAliveInterval` value, to allow time for pings to arrive.</span></span> |
| `withHandshakeResponseTimeout` | <span data-ttu-id="4683e-1202">15 sekund</span><span class="sxs-lookup"><span data-stu-id="4683e-1202">15 seconds</span></span> | <span data-ttu-id="4683e-1203">Vypršel časový limit počáteční metody handshake serveru.</span><span class="sxs-lookup"><span data-stu-id="4683e-1203">Timeout for initial server handshake.</span></span> <span data-ttu-id="4683e-1204">Pokud server v tomto intervalu neodešle odpověď handshake, klient zruší metodu handshake a aktivuje `onClose` událost.</span><span class="sxs-lookup"><span data-stu-id="4683e-1204">If the server doesn't send a handshake response in this interval, the client cancels the handshake and triggers the `onClose` event.</span></span> <span data-ttu-id="4683e-1205">Toto je pokročilé nastavení, které by mělo být změněno pouze v případě, že dochází k chybám časového limitu handshake kvůli závažné latenci sítě.</span><span class="sxs-lookup"><span data-stu-id="4683e-1205">This is an advanced setting that should only be modified if handshake timeout errors are occurring due to severe network latency.</span></span> <span data-ttu-id="4683e-1206">Další informace o procesu handshake najdete v tématu [ SignalR specifikace protokolu pro rozbočovače](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span><span class="sxs-lookup"><span data-stu-id="4683e-1206">For more detail on the handshake process, see the [SignalR Hub Protocol Specification](https://github.com/aspnet/SignalR/blob/master/specs/HubProtocol.md).</span></span> |

---

### <a name="configure-additional-options"></a><span data-ttu-id="4683e-1207">Konfigurace dalších možností</span><span class="sxs-lookup"><span data-stu-id="4683e-1207">Configure additional options</span></span>

<span data-ttu-id="4683e-1208">Další možnosti lze konfigurovat v `WithUrl` `withUrl` metodě (v jazyce JavaScript) na `HubConnectionBuilder` různých konfiguračních rozhraních API v `HttpHubConnectionBuilder` klientovi Java:</span><span class="sxs-lookup"><span data-stu-id="4683e-1208">Additional options can be configured in the `WithUrl` (`withUrl` in JavaScript) method on `HubConnectionBuilder` or on the various configuration APIs on the `HttpHubConnectionBuilder` in the Java client:</span></span>

# <a name="net"></a>[<span data-ttu-id="4683e-1209">.NET</span><span class="sxs-lookup"><span data-stu-id="4683e-1209">.NET</span></span>](#tab/dotnet)

| <span data-ttu-id="4683e-1210">Možnost .NET</span><span class="sxs-lookup"><span data-stu-id="4683e-1210">.NET Option</span></span> |  <span data-ttu-id="4683e-1211">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-1211">Default value</span></span> | <span data-ttu-id="4683e-1212">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-1212">Description</span></span> |
| ----------- | -------------- | ----------- |
| `AccessTokenProvider` | `null` | <span data-ttu-id="4683e-1213">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="4683e-1213">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `SkipNegotiation` | `false` | <span data-ttu-id="4683e-1214">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="4683e-1214">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="4683e-1215">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="4683e-1215">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="4683e-1216">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="4683e-1216">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| `ClientCertificates` | <span data-ttu-id="4683e-1217">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="4683e-1217">Empty</span></span> | <span data-ttu-id="4683e-1218">Kolekce certifikátů TLS pro odeslání na požadavky na ověření.</span><span class="sxs-lookup"><span data-stu-id="4683e-1218">A collection of TLS certificates to send to authenticate requests.</span></span> |
| `Cookies` | <span data-ttu-id="4683e-1219">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="4683e-1219">Empty</span></span> | <span data-ttu-id="4683e-1220">Kolekce souborů cookie protokolu HTTP, které se mají odeslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="4683e-1220">A collection of HTTP cookies to send with every HTTP request.</span></span> |
| `Credentials` | <span data-ttu-id="4683e-1221">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="4683e-1221">Empty</span></span> | <span data-ttu-id="4683e-1222">Přihlašovací údaje, které se mají poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="4683e-1222">Credentials to send with every HTTP request.</span></span> |
| `CloseTimeout` | <span data-ttu-id="4683e-1223">5 sekund</span><span class="sxs-lookup"><span data-stu-id="4683e-1223">5 seconds</span></span> | <span data-ttu-id="4683e-1224">Jenom objekty WebSockets.</span><span class="sxs-lookup"><span data-stu-id="4683e-1224">WebSockets only.</span></span> <span data-ttu-id="4683e-1225">Maximální doba, po jejímž uplynutí bude klient čekat na ukončení žádosti o uzavření.</span><span class="sxs-lookup"><span data-stu-id="4683e-1225">The maximum amount of time the client waits after closing for the server to acknowledge the close request.</span></span> <span data-ttu-id="4683e-1226">Pokud server nepotvrdí zavření v této době, klient se odpojí.</span><span class="sxs-lookup"><span data-stu-id="4683e-1226">If the server doesn't acknowledge the close within this time, the client disconnects.</span></span> |
| `Headers` | <span data-ttu-id="4683e-1227">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="4683e-1227">Empty</span></span> | <span data-ttu-id="4683e-1228">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="4683e-1228">A Map of additional HTTP headers to send with every HTTP request.</span></span> |
| `HttpMessageHandlerFactory` | `null` | <span data-ttu-id="4683e-1229">Delegát, který lze použít ke konfiguraci nebo nahrazení `HttpMessageHandler` používaného k odeslání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="4683e-1229">A delegate that can be used to configure or replace the `HttpMessageHandler` used to send HTTP requests.</span></span> <span data-ttu-id="4683e-1230">Nepoužívá se pro připojení pomocí protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="4683e-1230">Not used for WebSocket connections.</span></span> <span data-ttu-id="4683e-1231">Tento delegát musí vracet hodnotu, která není null, a obdrží výchozí hodnotu jako parametr.</span><span class="sxs-lookup"><span data-stu-id="4683e-1231">This delegate must return a non-null value, and it receives the default value as a parameter.</span></span> <span data-ttu-id="4683e-1232">Buď změňte nastavení u této výchozí hodnoty a vraťte je, nebo vraťte novou `HttpMessageHandler` instanci.</span><span class="sxs-lookup"><span data-stu-id="4683e-1232">Either modify settings on that default value and return it, or return a new `HttpMessageHandler` instance.</span></span> <span data-ttu-id="4683e-1233">**Při nahrazování obslužné rutiny Nezapomeňte zkopírovat nastavení, která chcete zachovat, od poskytnuté obslužné rutiny, jinak konfigurované možnosti (například soubory cookie a záhlaví) nebudou použity pro novou obslužnou rutinu.**</span><span class="sxs-lookup"><span data-stu-id="4683e-1233">**When replacing the handler make sure to copy the settings you want to keep from the provided handler, otherwise, the configured options (such as Cookies and Headers) won't apply to the new handler.**</span></span> |
| `Proxy` | `null` | <span data-ttu-id="4683e-1234">Proxy server HTTP, který se má použít při odesílání požadavků HTTP.</span><span class="sxs-lookup"><span data-stu-id="4683e-1234">An HTTP proxy to use when sending HTTP requests.</span></span> |
| `UseDefaultCredentials` | `false` | <span data-ttu-id="4683e-1235">Nastavte tuto logickou hodnotu pro odeslání výchozích přihlašovacích údajů pro požadavky HTTP a WebSockets.</span><span class="sxs-lookup"><span data-stu-id="4683e-1235">Set this boolean to send the default credentials for HTTP and WebSockets requests.</span></span> <span data-ttu-id="4683e-1236">To umožňuje použití ověřování systému Windows.</span><span class="sxs-lookup"><span data-stu-id="4683e-1236">This enables the use of Windows authentication.</span></span> |
| `WebSocketConfiguration` | `null` | <span data-ttu-id="4683e-1237">Delegát, který lze použít ke konfiguraci dalších možností protokolu WebSocket.</span><span class="sxs-lookup"><span data-stu-id="4683e-1237">A delegate that can be used to configure additional WebSocket options.</span></span> <span data-ttu-id="4683e-1238">Přijímá instanci [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) , která se dá použít ke konfiguraci možností.</span><span class="sxs-lookup"><span data-stu-id="4683e-1238">Receives an instance of [ClientWebSocketOptions](/dotnet/api/system.net.websockets.clientwebsocketoptions) that can be used to configure the options.</span></span> |

# <a name="javascript"></a>[<span data-ttu-id="4683e-1239">JavaScript</span><span class="sxs-lookup"><span data-stu-id="4683e-1239">JavaScript</span></span>](#tab/javascript)

| <span data-ttu-id="4683e-1240">Možnost JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="4683e-1240">JavaScript Option</span></span> | <span data-ttu-id="4683e-1241">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-1241">Default Value</span></span> | <span data-ttu-id="4683e-1242">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-1242">Description</span></span> |
| ----------------- | ------------- | ----------- |
| `accessTokenFactory` | `null` | <span data-ttu-id="4683e-1243">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="4683e-1243">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `skipNegotiation` | `false` | <span data-ttu-id="4683e-1244">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="4683e-1244">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="4683e-1245">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="4683e-1245">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="4683e-1246">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="4683e-1246">This setting can't be enabled when using the Azure SignalR Service.</span></span> |

# <a name="java"></a>[<span data-ttu-id="4683e-1247">Java</span><span class="sxs-lookup"><span data-stu-id="4683e-1247">Java</span></span>](#tab/java)

| <span data-ttu-id="4683e-1248">Možnost jazyka Java</span><span class="sxs-lookup"><span data-stu-id="4683e-1248">Java Option</span></span> | <span data-ttu-id="4683e-1249">Výchozí hodnota</span><span class="sxs-lookup"><span data-stu-id="4683e-1249">Default Value</span></span> | <span data-ttu-id="4683e-1250">Description</span><span class="sxs-lookup"><span data-stu-id="4683e-1250">Description</span></span> |
| ----------- | ------------- | ----------- |
| `withAccessTokenProvider` | `null` | <span data-ttu-id="4683e-1251">Funkce, která vrací řetězec, který je poskytnut jako ověřovací token nosiče v požadavcích HTTP.</span><span class="sxs-lookup"><span data-stu-id="4683e-1251">A function returning a string that is provided as a Bearer authentication token in HTTP requests.</span></span> |
| `shouldSkipNegotiate` | `false` | <span data-ttu-id="4683e-1252">Nastavením tohoto `true` postupu přeskočíte krok vyjednávání.</span><span class="sxs-lookup"><span data-stu-id="4683e-1252">Set this to `true` to skip the negotiation step.</span></span> <span data-ttu-id="4683e-1253">**Podporuje se jenom v případě, že přenos WebSockets je jediným povoleným přenosem**.</span><span class="sxs-lookup"><span data-stu-id="4683e-1253">**Only supported when the WebSockets transport is the only enabled transport**.</span></span> <span data-ttu-id="4683e-1254">Toto nastavení se nedá povolit při použití služby Azure SignalR .</span><span class="sxs-lookup"><span data-stu-id="4683e-1254">This setting can't be enabled when using the Azure SignalR Service.</span></span> |
| <span data-ttu-id="4683e-1255">`withHeader` `withHeaders`</span><span class="sxs-lookup"><span data-stu-id="4683e-1255">`withHeader` `withHeaders`</span></span> | <span data-ttu-id="4683e-1256">Obsahovat</span><span class="sxs-lookup"><span data-stu-id="4683e-1256">Empty</span></span> | <span data-ttu-id="4683e-1257">Mapa dalších hlaviček protokolu HTTP, která se má poslat s každou žádostí HTTP</span><span class="sxs-lookup"><span data-stu-id="4683e-1257">A Map of additional HTTP headers to send with every HTTP request.</span></span> |

---

<span data-ttu-id="4683e-1258">V klientu .NET můžete tyto možnosti upravit pomocí delegáta možností, který je k dispozici pro `WithUrl` :</span><span class="sxs-lookup"><span data-stu-id="4683e-1258">In the .NET Client, these options can be modified by the options delegate provided to `WithUrl`:</span></span>

```csharp
var connection = new HubConnectionBuilder()
    .WithUrl("https://example.com/chathub", options => {
        options.Headers["Foo"] = "Bar";
        options.Cookies.Add(new Cookie(/* ... */);
        options.ClientCertificates.Add(/* ... */);
    })
    .Build();
```

<span data-ttu-id="4683e-1259">V klientovi JavaScriptu lze tyto možnosti poskytnout v objektu jazyka JavaScript, který je k dispozici pro `withUrl` :</span><span class="sxs-lookup"><span data-stu-id="4683e-1259">In the JavaScript Client, these options can be provided in a JavaScript object provided to `withUrl`:</span></span>

```javascript
let connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub", {
        skipNegotiation: true,
        transport: signalR.HttpTransportType.WebSockets
    })
    .build();
```

<span data-ttu-id="4683e-1260">V klientovi Java lze tyto možnosti nakonfigurovat s metodami `HttpHubConnectionBuilder` vrácenými z`HubConnectionBuilder.create("HUB URL")`</span><span class="sxs-lookup"><span data-stu-id="4683e-1260">In the Java client, these options can be configured with the methods on the `HttpHubConnectionBuilder` returned from the `HubConnectionBuilder.create("HUB URL")`</span></span>

```java
HubConnection hubConnection = HubConnectionBuilder.create("https://example.com/chathub")
        .withHeader("Foo", "Bar")
        .shouldSkipNegotiate(true)
        .withHandshakeResponseTimeout(30*1000)
        .build();
```

## <a name="additional-resources"></a><span data-ttu-id="4683e-1261">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="4683e-1261">Additional resources</span></span>

* <xref:tutorials/signalr>
* <xref:signalr/hubs>
* <xref:signalr/javascript-client>
* <xref:signalr/dotnet-client>
* <xref:signalr/messagepackhubprotocol>
* <xref:signalr/supported-platforms>

::: moniker-end
