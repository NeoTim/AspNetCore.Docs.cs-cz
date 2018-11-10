---
title: Rozdíly mezi SignalR a funkce SignalR technologie ASP.NET Core
author: tdykstra
description: Rozdíly mezi SignalR a funkce SignalR technologie ASP.NET Core
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.date: 09/10/2018
uid: signalr/version-differences
ms.openlocfilehash: 8f07647959b6ef815eed599703bdb1bfb446572f
ms.sourcegitcommit: edb9d2d78c9a4d68b397e74ae2aff088b325a143
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2018
ms.locfileid: "51505749"
---
# <a name="differences-between-aspnet-signalr-and-aspnet-core-signalr"></a><span data-ttu-id="dedbf-103">Rozdíly mezi funkce SignalR technologie ASP.NET a technologie SignalR technologie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dedbf-103">Differences between ASP.NET SignalR and ASP.NET Core SignalR</span></span>

<span data-ttu-id="dedbf-104">Funkce SignalR technologie ASP.NET Core není kompatibilní s klientů nebo serverů pro funkci SignalR technologie ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="dedbf-104">ASP.NET Core SignalR isn't compatible with clients or servers for ASP.NET SignalR.</span></span> <span data-ttu-id="dedbf-105">Tento článek podrobně popisuje funkce, které byly odstraněny nebo změněny v knihovně SignalR technologie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dedbf-105">This article details features which have been removed or changed in ASP.NET Core SignalR.</span></span>

## <a name="how-to-identify-the-signalr-version"></a><span data-ttu-id="dedbf-106">Jak určit verzi SignalR</span><span class="sxs-lookup"><span data-stu-id="dedbf-106">How to identify the SignalR version</span></span>

|                      | <span data-ttu-id="dedbf-107">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="dedbf-107">ASP.NET SignalR</span></span> | <span data-ttu-id="dedbf-108">Funkce SignalR technologie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dedbf-108">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="dedbf-109">Balíček NuGet server</span><span class="sxs-lookup"><span data-stu-id="dedbf-109">Server NuGet Package</span></span> | [<span data-ttu-id="dedbf-110">Microsoft.AspNet.SignalR</span><span class="sxs-lookup"><span data-stu-id="dedbf-110">Microsoft.AspNet.SignalR</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | <span data-ttu-id="dedbf-111">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span><span class="sxs-lookup"><span data-stu-id="dedbf-111">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span></span><br><span data-ttu-id="dedbf-112">[Microsoft.AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework)</span><span class="sxs-lookup"><span data-stu-id="dedbf-112">[Microsoft.AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework)</span></span> |
| <span data-ttu-id="dedbf-113">Balíčky NuGet pro klienta</span><span class="sxs-lookup"><span data-stu-id="dedbf-113">Client NuGet Packages</span></span> | [<span data-ttu-id="dedbf-114">Microsoft.AspNet.SignalR.Client</span><span class="sxs-lookup"><span data-stu-id="dedbf-114">Microsoft.AspNet.SignalR.Client</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[<span data-ttu-id="dedbf-115">Microsoft.AspNet.SignalR.JS</span><span class="sxs-lookup"><span data-stu-id="dedbf-115">Microsoft.AspNet.SignalR.JS</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [<span data-ttu-id="dedbf-116">Microsoft.AspNetCore.SignalR.Client</span><span class="sxs-lookup"><span data-stu-id="dedbf-116">Microsoft.AspNetCore.SignalR.Client</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| <span data-ttu-id="dedbf-117">Npm Package klienta</span><span class="sxs-lookup"><span data-stu-id="dedbf-117">Client npm Package</span></span> | [<span data-ttu-id="dedbf-118">Funkce signalr</span><span class="sxs-lookup"><span data-stu-id="dedbf-118">signalr</span></span>](https://www.npmjs.com/package/signalr) | [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) |
| <span data-ttu-id="dedbf-119">Typ aplikace serveru</span><span class="sxs-lookup"><span data-stu-id="dedbf-119">Server App Type</span></span> | <span data-ttu-id="dedbf-120">Technologie ASP.NET (System.Web) nebo samoobslužné hostování OWIN</span><span class="sxs-lookup"><span data-stu-id="dedbf-120">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="dedbf-121">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dedbf-121">ASP.NET Core</span></span> |
| <span data-ttu-id="dedbf-122">Podporované serverové platformy</span><span class="sxs-lookup"><span data-stu-id="dedbf-122">Supported Server Platforms</span></span> | <span data-ttu-id="dedbf-123">Rozhraní .NET framework 4.5 nebo novější</span><span class="sxs-lookup"><span data-stu-id="dedbf-123">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="dedbf-124">Rozhraní .NET framework 4.6.1 nebo novější</span><span class="sxs-lookup"><span data-stu-id="dedbf-124">.NET Framework 4.6.1 or later</span></span><br><span data-ttu-id="dedbf-125">.NET core 2.1 nebo novější</span><span class="sxs-lookup"><span data-stu-id="dedbf-125">.NET Core 2.1 or later</span></span> |

## <a name="feature-differences"></a><span data-ttu-id="dedbf-126">Rozdíly ve funkcích</span><span class="sxs-lookup"><span data-stu-id="dedbf-126">Feature differences</span></span>

### <a name="automatic-reconnects"></a><span data-ttu-id="dedbf-127">Automatické připojování</span><span class="sxs-lookup"><span data-stu-id="dedbf-127">Automatic reconnects</span></span>

<span data-ttu-id="dedbf-128">Automatické připojování nepodporují funkce SignalR technologie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dedbf-128">Automatic reconnects aren't supported in ASP.NET Core SignalR.</span></span> <span data-ttu-id="dedbf-129">Pokud klient je odpojen, uživatel musí explicitně spustit nové připojení, aby připojení bylo možné.</span><span class="sxs-lookup"><span data-stu-id="dedbf-129">If the client is disconnected, the user must explicitly start a new connection if they want to reconnect.</span></span> <span data-ttu-id="dedbf-130">V funkce SignalR technologie ASP.NET SignalR pokusí znovu připojit k serveru, pokud připojení se ukončí.</span><span class="sxs-lookup"><span data-stu-id="dedbf-130">In ASP.NET SignalR, SignalR attempts to reconnect to the server if the connection is dropped.</span></span> 

### <a name="protocol-support"></a><span data-ttu-id="dedbf-131">Podpora protokolu</span><span class="sxs-lookup"><span data-stu-id="dedbf-131">Protocol support</span></span>

<span data-ttu-id="dedbf-132">Funkce SignalR technologie ASP.NET Core podporuje JSON, jakož i nové binární protokol založený na [MessagePack](xref:signalr/messagepackhubprotocol).</span><span class="sxs-lookup"><span data-stu-id="dedbf-132">ASP.NET Core SignalR supports JSON, as well as a new binary protocol based on [MessagePack](xref:signalr/messagepackhubprotocol).</span></span> <span data-ttu-id="dedbf-133">Kromě toho je možné vytvářet vlastní protokoly.</span><span class="sxs-lookup"><span data-stu-id="dedbf-133">Additionally, custom protocols can be created.</span></span>

### <a name="transports"></a><span data-ttu-id="dedbf-134">Přenosy</span><span class="sxs-lookup"><span data-stu-id="dedbf-134">Transports</span></span>

<span data-ttu-id="dedbf-135">Přenos navždy rámce není podporován v knihovně SignalR technologie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dedbf-135">The Forever Frame transport is not supported in ASP.NET Core SignalR.</span></span>

## <a name="differences-on-the-server"></a><span data-ttu-id="dedbf-136">Rozdíly na serveru</span><span class="sxs-lookup"><span data-stu-id="dedbf-136">Differences on the server</span></span>

<span data-ttu-id="dedbf-137">Jsou součástí knihoven na straně serveru funkce SignalR technologie ASP.NET Core [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) balíček, který je součástí **webové aplikace ASP.NET Core** šablony pro syntaxi Razor a MVC projekty.</span><span class="sxs-lookup"><span data-stu-id="dedbf-137">The ASP.NET Core SignalR server-side libraries are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) package that's part of the **ASP.NET Core Web Application** template for both Razor and MVC projects.</span></span>

<span data-ttu-id="dedbf-138">Funkce SignalR technologie ASP.NET Core je middleware ASP.NET Core, takže musí být nakonfigurovaný pomocí volání [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="dedbf-138">ASP.NET Core SignalR is an ASP.NET Core middleware, so it must be configured by calling [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSignalR()
```

<span data-ttu-id="dedbf-139">Konfigurace směrování, mapování tras k rozbočovačům uvnitř [UseSignalR](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr) volání metody `Startup.Configure` metoda.</span><span class="sxs-lookup"><span data-stu-id="dedbf-139">To configure routing, map routes to hubs inside the [UseSignalR](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr) method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

### <a name="sticky-sessions"></a><span data-ttu-id="dedbf-140">Rychlé relace</span><span class="sxs-lookup"><span data-stu-id="dedbf-140">Sticky sessions</span></span>

<span data-ttu-id="dedbf-141">Model horizontálním navýšením kapacity pro funkci SignalR technologie ASP.NET umožňuje klientům znovu připojit a odesílání zpráv na libovolný server ve farmě.</span><span class="sxs-lookup"><span data-stu-id="dedbf-141">The scaleout model for ASP.NET SignalR allows clients to reconnect and send messages to any server in the farm.</span></span> <span data-ttu-id="dedbf-142">V knihovně SignalR technologie ASP.NET Core musí klient pracovat na stejném serveru po dobu trvání připojení.</span><span class="sxs-lookup"><span data-stu-id="dedbf-142">In ASP.NET Core SignalR, the client must interact with the same server for the duration of the connection.</span></span> <span data-ttu-id="dedbf-143">Pro škálování, použití Redis, to znamená, že se vyžaduje rychlé relace.</span><span class="sxs-lookup"><span data-stu-id="dedbf-143">For scaleout using Redis, that means sticky sessions are required.</span></span> <span data-ttu-id="dedbf-144">Pro práci s horizontálním navýšením kapacity [služby Azure SignalR](/azure/azure-signalr/), rychlé relace nejsou vyžadovány, protože služba zpracovává připojení ke klientům.</span><span class="sxs-lookup"><span data-stu-id="dedbf-144">For scaleout using [Azure SignalR Service](/azure/azure-signalr/), sticky sessions are not required because the service handles connections to clients.</span></span> 

### <a name="single-hub-per-connection"></a><span data-ttu-id="dedbf-145">Jedno Centrum za připojení</span><span class="sxs-lookup"><span data-stu-id="dedbf-145">Single hub per connection</span></span>

<span data-ttu-id="dedbf-146">Funkce SignalR technologie ASP.NET Core je zjednodušený model připojení.</span><span class="sxs-lookup"><span data-stu-id="dedbf-146">In ASP.NET Core SignalR, the connection model has been simplified.</span></span> <span data-ttu-id="dedbf-147">Připojení jsou provedeny přímo do jednoho rozbočovače, nikoli jednoho připojení používá sdílet přístup k více rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="dedbf-147">Connections are made directly to a single hub, rather than a single connection being used to share access to multiple hubs.</span></span>

### <a name="streaming"></a><span data-ttu-id="dedbf-148">Streamování</span><span class="sxs-lookup"><span data-stu-id="dedbf-148">Streaming</span></span>

<span data-ttu-id="dedbf-149">ASP.NET Core SignalR teď podporuje [streamovaná data](xref:signalr/streaming) z rozbočovače klientovi.</span><span class="sxs-lookup"><span data-stu-id="dedbf-149">ASP.NET Core SignalR now supports [streaming data](xref:signalr/streaming) from the hub to the client.</span></span>

### <a name="state"></a><span data-ttu-id="dedbf-150">Stav</span><span class="sxs-lookup"><span data-stu-id="dedbf-150">State</span></span>

<span data-ttu-id="dedbf-151">Umožňuje předat libovolný stav mezi klienty a centrum (často označované jako HubState) byla odebrána a také podporu pro zprávy o průběhu.</span><span class="sxs-lookup"><span data-stu-id="dedbf-151">The ability to pass arbitrary state between clients and the hub (often called HubState) has been removed, as well as support for progress messages.</span></span> <span data-ttu-id="dedbf-152">V tuto chvíli není nevyskytují proxy rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="dedbf-152">There is no counterpart of hub proxies at the moment.</span></span>

### <a name="globalhost"></a><span data-ttu-id="dedbf-153">GlobalHost</span><span class="sxs-lookup"><span data-stu-id="dedbf-153">GlobalHost</span></span>

<span data-ttu-id="dedbf-154">ASP.NET Core je integrovaný do rozhraní injektáž závislostí (DI).</span><span class="sxs-lookup"><span data-stu-id="dedbf-154">ASP.NET Core has dependency injection (DI) built into the framework.</span></span> <span data-ttu-id="dedbf-155">Služby využívat pro přístup k DI [HubContext](xref:signalr/hubcontext).</span><span class="sxs-lookup"><span data-stu-id="dedbf-155">Services can use DI to access the [HubContext](xref:signalr/hubcontext).</span></span> <span data-ttu-id="dedbf-156">`GlobalHost` Objekt, který se používá v knihovně SignalR technologie ASP.NET k získání `HubContext` neexistuje v knihovně SignalR technologie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dedbf-156">The `GlobalHost` object that is used in ASP.NET SignalR to get a `HubContext` doesn't exist in ASP.NET Core SignalR.</span></span>

### <a name="hubpipeline"></a><span data-ttu-id="dedbf-157">Kanálu rozbočovače přidán</span><span class="sxs-lookup"><span data-stu-id="dedbf-157">HubPipeline</span></span>

<span data-ttu-id="dedbf-158">Funkce SignalR technologie ASP.NET Core nemá podporu `HubPipeline` moduly.</span><span class="sxs-lookup"><span data-stu-id="dedbf-158">ASP.NET Core SignalR doesn't have support for `HubPipeline` modules.</span></span>

## <a name="differences-on-the-client"></a><span data-ttu-id="dedbf-159">Rozdíly v klientovi</span><span class="sxs-lookup"><span data-stu-id="dedbf-159">Differences on the client</span></span>

### <a name="typescript"></a><span data-ttu-id="dedbf-160">TypeScript</span><span class="sxs-lookup"><span data-stu-id="dedbf-160">TypeScript</span></span>

<span data-ttu-id="dedbf-161">Klient funkce SignalR technologie ASP.NET Core je napsána v [TypeScript](https://www.typescriptlang.org/).</span><span class="sxs-lookup"><span data-stu-id="dedbf-161">The ASP.NET Core SignalR client is written in [TypeScript](https://www.typescriptlang.org/).</span></span> <span data-ttu-id="dedbf-162">Můžete psát v jazyce JavaScript nebo TypeScript při použití [javascriptový klient](xref:signalr/javascript-client).</span><span class="sxs-lookup"><span data-stu-id="dedbf-162">You can write in JavaScript or TypeScript when using the [JavaScript client](xref:signalr/javascript-client).</span></span>

### <a name="the-javascript-client-is-hosted-at-npmhttpswwwnpmjscom"></a><span data-ttu-id="dedbf-163">JavaScript klienta je hostovaná na [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="dedbf-163">The JavaScript client is hosted at [npm](https://www.npmjs.com/)</span></span>

<span data-ttu-id="dedbf-164">V předchozích verzích klienta JavaScript byl získán prostřednictvím balíčku NuGet v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="dedbf-164">In previous versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="dedbf-165">Verze jádra [ @aspnet/signalr ](https://www.npmjs.com/package/@aspnet/signalr) balíčku npm obsahuje knihovny jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="dedbf-165">For the Core versions, the [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="dedbf-166">Není součástí tohoto balíčku **webové aplikace ASP.NET Core** šablony.</span><span class="sxs-lookup"><span data-stu-id="dedbf-166">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="dedbf-167">Získejte a nainstalujte pomocí npm `@aspnet/signalr` balíčku npm.</span><span class="sxs-lookup"><span data-stu-id="dedbf-167">Use npm to obtain and install the `@aspnet/signalr` npm package.</span></span>

```console
npm init -y
npm install @aspnet/signalr
```

### <a name="jquery"></a><span data-ttu-id="dedbf-168">jQuery</span><span class="sxs-lookup"><span data-stu-id="dedbf-168">jQuery</span></span>

<span data-ttu-id="dedbf-169">Závislost na jQuery byla odebrána, ale projekty můžete dál používat jQuery.</span><span class="sxs-lookup"><span data-stu-id="dedbf-169">The dependency on jQuery has been removed, however projects can still use jQuery.</span></span>

### <a name="internet-explorer-support"></a><span data-ttu-id="dedbf-170">Podpora aplikace Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="dedbf-170">Internet Explorer support</span></span>

<span data-ttu-id="dedbf-171">Funkce SignalR technologie ASP.NET Core vyžaduje Microsoft Internet Explorer 11 nebo novější (funkce SignalR technologie ASP.NET podporovány Microsoft Internet Explorer 8 a novější).</span><span class="sxs-lookup"><span data-stu-id="dedbf-171">ASP.NET Core SignalR requires Microsoft Internet Explorer 11 or later (ASP.NET SignalR supported Microsoft Internet Explorer 8 and later).</span></span>

### <a name="javascript-client-method-syntax"></a><span data-ttu-id="dedbf-172">Syntaxe využívající metody JavaScript klienta</span><span class="sxs-lookup"><span data-stu-id="dedbf-172">JavaScript client method syntax</span></span>

<span data-ttu-id="dedbf-173">Syntaxe jazyka JavaScript byl změněn z předchozí verze funkce SignalR.</span><span class="sxs-lookup"><span data-stu-id="dedbf-173">The JavaScript syntax has changed from the previous version of SignalR.</span></span> <span data-ttu-id="dedbf-174">Místo použití `$connection` objektu, vytvořte pomocí připojení [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="dedbf-174">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="dedbf-175">Použití [na](/javascript/api/@aspnet/signalr/HubConnection#on) metoda určují metody klienta můžete volání rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="dedbf-175">Use the [on](/javascript/api/@aspnet/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = user + " says " + msg;
    log(encodedMsg);
});
```

<span data-ttu-id="dedbf-176">Po vytvoření metody klienta, spusťte připojení rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="dedbf-176">After creating the client method, start the hub connection.</span></span> <span data-ttu-id="dedbf-177">Řetězce [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) metodou protokolu nebo zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="dedbf-177">Chain a [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) method to log or handle errors.</span></span>

```javascript
connection.start().catch(err => console.error(err.toString()));
```

### <a name="hub-proxies"></a><span data-ttu-id="dedbf-178">Proxy servery hub</span><span class="sxs-lookup"><span data-stu-id="dedbf-178">Hub proxies</span></span>

<span data-ttu-id="dedbf-179">Proxy servery hub už nebude automaticky generovány.</span><span class="sxs-lookup"><span data-stu-id="dedbf-179">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="dedbf-180">Místo toho název metody je předán [vyvolat](/javascript/api/%40aspnet/signalr/hubconnection#invoke) rozhraní API jako řetězec.</span><span class="sxs-lookup"><span data-stu-id="dedbf-180">Instead, the method name is passed into the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) API as a string.</span></span>

### <a name="net-and-other-clients"></a><span data-ttu-id="dedbf-181">.NET a další klienti</span><span class="sxs-lookup"><span data-stu-id="dedbf-181">.NET and other clients</span></span>

<span data-ttu-id="dedbf-182">`Microsoft.AspNetCore.SignalR.Client` Balíček NuGet obsahuje klientské knihovny .NET pro funkci SignalR technologie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="dedbf-182">The `Microsoft.AspNetCore.SignalR.Client` NuGet package contains the .NET client libraries for ASP.NET Core SignalR.</span></span>

<span data-ttu-id="dedbf-183">Použití [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder) vytvářet a sestavovat instanci, připojení k rozbočovači.</span><span class="sxs-lookup"><span data-stu-id="dedbf-183">Use the [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder) to create and build an instance of a connection to a hub.</span></span>

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a><span data-ttu-id="dedbf-184">Rozdíly horizontálním navýšením kapacity</span><span class="sxs-lookup"><span data-stu-id="dedbf-184">Scaleout differences</span></span>

<span data-ttu-id="dedbf-185">Funkce SignalR technologie ASP.NET podporuje SQL Server a Redis.</span><span class="sxs-lookup"><span data-stu-id="dedbf-185">ASP.NET SignalR supports SQL Server and Redis.</span></span> <span data-ttu-id="dedbf-186">Funkce SignalR technologie ASP.NET Core podporuje služby Azure SignalR a Redis.</span><span class="sxs-lookup"><span data-stu-id="dedbf-186">ASP.NET Core SignalR supports Azure SignalR Service and Redis.</span></span>

### <a name="aspnet"></a><span data-ttu-id="dedbf-187">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="dedbf-187">ASP.NET</span></span>

* [<span data-ttu-id="dedbf-188">Škálování aplikace SignalR službou Azure Service Bus</span><span class="sxs-lookup"><span data-stu-id="dedbf-188">SignalR scaleout with Azure Service Bus</span></span>](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [<span data-ttu-id="dedbf-189">Škálování aplikace SignalR službou Redis</span><span class="sxs-lookup"><span data-stu-id="dedbf-189">SignalR scaleout with Redis</span></span>](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [<span data-ttu-id="dedbf-190">Škálování aplikace SignalR službou SQL Server</span><span class="sxs-lookup"><span data-stu-id="dedbf-190">SignalR scaleout with SQL Server</span></span>](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a><span data-ttu-id="dedbf-191">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="dedbf-191">ASP.NET Core</span></span>

* [<span data-ttu-id="dedbf-192">Službě Azure SignalR</span><span class="sxs-lookup"><span data-stu-id="dedbf-192">Azure SignalR Service</span></span>](/azure/azure-signalr/)

## <a name="additional-resources"></a><span data-ttu-id="dedbf-193">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="dedbf-193">Additional resources</span></span>

* [<span data-ttu-id="dedbf-194">Centra</span><span class="sxs-lookup"><span data-stu-id="dedbf-194">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="dedbf-195">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="dedbf-195">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="dedbf-196">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="dedbf-196">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="dedbf-197">Podporované platformy</span><span class="sxs-lookup"><span data-stu-id="dedbf-197">Supported platforms</span></span>](xref:signalr/supported-platforms)
