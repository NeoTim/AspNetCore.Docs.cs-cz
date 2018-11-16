---
title: Rozdíly mezi SignalR a funkce SignalR technologie ASP.NET Core
author: tdykstra
description: Rozdíly mezi SignalR a funkce SignalR technologie ASP.NET Core
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.date: 11/14/2018
uid: signalr/version-differences
ms.openlocfilehash: c9302f1c9e7cd4e62eaeaef871feb54ef26aa3ca
ms.sourcegitcommit: 09bcda59a58019fdf47b2db5259fe87acf19dd38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51708410"
---
# <a name="differences-between-aspnet-signalr-and-aspnet-core-signalr"></a><span data-ttu-id="327d0-103">Rozdíly mezi funkce SignalR technologie ASP.NET a technologie SignalR technologie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="327d0-103">Differences between ASP.NET SignalR and ASP.NET Core SignalR</span></span>

<span data-ttu-id="327d0-104">Funkce SignalR technologie ASP.NET Core není kompatibilní s klientů nebo serverů pro funkci SignalR technologie ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="327d0-104">ASP.NET Core SignalR isn't compatible with clients or servers for ASP.NET SignalR.</span></span> <span data-ttu-id="327d0-105">Tento článek podrobně popisuje funkce, které byly odstraněny nebo změněny v knihovně SignalR technologie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="327d0-105">This article details features which have been removed or changed in ASP.NET Core SignalR.</span></span>

## <a name="how-to-identify-the-signalr-version"></a><span data-ttu-id="327d0-106">Jak určit verzi SignalR</span><span class="sxs-lookup"><span data-stu-id="327d0-106">How to identify the SignalR version</span></span>

|                      | <span data-ttu-id="327d0-107">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="327d0-107">ASP.NET SignalR</span></span> | <span data-ttu-id="327d0-108">Funkce SignalR technologie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="327d0-108">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="327d0-109">Balíček NuGet server</span><span class="sxs-lookup"><span data-stu-id="327d0-109">Server NuGet Package</span></span> | [<span data-ttu-id="327d0-110">Microsoft.AspNet.SignalR</span><span class="sxs-lookup"><span data-stu-id="327d0-110">Microsoft.AspNet.SignalR</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | <span data-ttu-id="327d0-111">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span><span class="sxs-lookup"><span data-stu-id="327d0-111">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span></span><br><span data-ttu-id="327d0-112">[Microsoft.AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework)</span><span class="sxs-lookup"><span data-stu-id="327d0-112">[Microsoft.AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework)</span></span> |
| <span data-ttu-id="327d0-113">Balíčky NuGet pro klienta</span><span class="sxs-lookup"><span data-stu-id="327d0-113">Client NuGet Packages</span></span> | [<span data-ttu-id="327d0-114">Microsoft.AspNet.SignalR.Client</span><span class="sxs-lookup"><span data-stu-id="327d0-114">Microsoft.AspNet.SignalR.Client</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[<span data-ttu-id="327d0-115">Microsoft.AspNet.SignalR.JS</span><span class="sxs-lookup"><span data-stu-id="327d0-115">Microsoft.AspNet.SignalR.JS</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [<span data-ttu-id="327d0-116">Microsoft.AspNetCore.SignalR.Client</span><span class="sxs-lookup"><span data-stu-id="327d0-116">Microsoft.AspNetCore.SignalR.Client</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| <span data-ttu-id="327d0-117">Npm Package klienta</span><span class="sxs-lookup"><span data-stu-id="327d0-117">Client npm Package</span></span> | [<span data-ttu-id="327d0-118">Funkce signalr</span><span class="sxs-lookup"><span data-stu-id="327d0-118">signalr</span></span>](https://www.npmjs.com/package/signalr) | [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) |
| <span data-ttu-id="327d0-119">Typ aplikace serveru</span><span class="sxs-lookup"><span data-stu-id="327d0-119">Server App Type</span></span> | <span data-ttu-id="327d0-120">Technologie ASP.NET (System.Web) nebo samoobslužné hostování OWIN</span><span class="sxs-lookup"><span data-stu-id="327d0-120">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="327d0-121">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="327d0-121">ASP.NET Core</span></span> |
| <span data-ttu-id="327d0-122">Podporované serverové platformy</span><span class="sxs-lookup"><span data-stu-id="327d0-122">Supported Server Platforms</span></span> | <span data-ttu-id="327d0-123">Rozhraní .NET framework 4.5 nebo novější</span><span class="sxs-lookup"><span data-stu-id="327d0-123">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="327d0-124">Rozhraní .NET framework 4.6.1 nebo novější</span><span class="sxs-lookup"><span data-stu-id="327d0-124">.NET Framework 4.6.1 or later</span></span><br><span data-ttu-id="327d0-125">.NET core 2.1 nebo novější</span><span class="sxs-lookup"><span data-stu-id="327d0-125">.NET Core 2.1 or later</span></span> |

## <a name="feature-differences"></a><span data-ttu-id="327d0-126">Rozdíly ve funkcích</span><span class="sxs-lookup"><span data-stu-id="327d0-126">Feature differences</span></span>

### <a name="automatic-reconnects"></a><span data-ttu-id="327d0-127">Automatické připojování</span><span class="sxs-lookup"><span data-stu-id="327d0-127">Automatic reconnects</span></span>

<span data-ttu-id="327d0-128">Automatické připojování nepodporují funkce SignalR technologie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="327d0-128">Automatic reconnects aren't supported in ASP.NET Core SignalR.</span></span> <span data-ttu-id="327d0-129">Pokud klient je odpojen, uživatel musí explicitně spustit nové připojení, aby připojení bylo možné.</span><span class="sxs-lookup"><span data-stu-id="327d0-129">If the client is disconnected, the user must explicitly start a new connection if they want to reconnect.</span></span> <span data-ttu-id="327d0-130">V funkce SignalR technologie ASP.NET SignalR pokusí znovu připojit k serveru, pokud připojení se ukončí.</span><span class="sxs-lookup"><span data-stu-id="327d0-130">In ASP.NET SignalR, SignalR attempts to reconnect to the server if the connection is dropped.</span></span> 

### <a name="protocol-support"></a><span data-ttu-id="327d0-131">Podpora protokolu</span><span class="sxs-lookup"><span data-stu-id="327d0-131">Protocol support</span></span>

<span data-ttu-id="327d0-132">Funkce SignalR technologie ASP.NET Core podporuje JSON, jakož i nové binární protokol založený na [MessagePack](xref:signalr/messagepackhubprotocol).</span><span class="sxs-lookup"><span data-stu-id="327d0-132">ASP.NET Core SignalR supports JSON, as well as a new binary protocol based on [MessagePack](xref:signalr/messagepackhubprotocol).</span></span> <span data-ttu-id="327d0-133">Kromě toho je možné vytvářet vlastní protokoly.</span><span class="sxs-lookup"><span data-stu-id="327d0-133">Additionally, custom protocols can be created.</span></span>

### <a name="transports"></a><span data-ttu-id="327d0-134">Přenosy</span><span class="sxs-lookup"><span data-stu-id="327d0-134">Transports</span></span>

<span data-ttu-id="327d0-135">Přenos navždy rámce není podporován v knihovně SignalR technologie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="327d0-135">The Forever Frame transport is not supported in ASP.NET Core SignalR.</span></span>

## <a name="differences-on-the-server"></a><span data-ttu-id="327d0-136">Rozdíly na serveru</span><span class="sxs-lookup"><span data-stu-id="327d0-136">Differences on the server</span></span>

<span data-ttu-id="327d0-137">Jsou součástí knihoven na straně serveru funkce SignalR technologie ASP.NET Core [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) balíček, který je součástí **webové aplikace ASP.NET Core** šablony pro syntaxi Razor a MVC projekty.</span><span class="sxs-lookup"><span data-stu-id="327d0-137">The ASP.NET Core SignalR server-side libraries are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) package that's part of the **ASP.NET Core Web Application** template for both Razor and MVC projects.</span></span>

<span data-ttu-id="327d0-138">Funkce SignalR technologie ASP.NET Core je middleware ASP.NET Core, takže musí být nakonfigurovaný pomocí volání [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="327d0-138">ASP.NET Core SignalR is an ASP.NET Core middleware, so it must be configured by calling [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSignalR()
```

<span data-ttu-id="327d0-139">Konfigurace směrování, mapování tras k rozbočovačům uvnitř [UseSignalR](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr) volání metody `Startup.Configure` metoda.</span><span class="sxs-lookup"><span data-stu-id="327d0-139">To configure routing, map routes to hubs inside the [UseSignalR](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr) method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

### <a name="sticky-sessions"></a><span data-ttu-id="327d0-140">Rychlé relace</span><span class="sxs-lookup"><span data-stu-id="327d0-140">Sticky sessions</span></span>

<span data-ttu-id="327d0-141">Model horizontálním navýšením kapacity pro funkci SignalR technologie ASP.NET umožňuje klientům znovu připojit a odesílání zpráv na libovolný server ve farmě.</span><span class="sxs-lookup"><span data-stu-id="327d0-141">The scaleout model for ASP.NET SignalR allows clients to reconnect and send messages to any server in the farm.</span></span> <span data-ttu-id="327d0-142">V knihovně SignalR technologie ASP.NET Core musí klient pracovat na stejném serveru po dobu trvání připojení.</span><span class="sxs-lookup"><span data-stu-id="327d0-142">In ASP.NET Core SignalR, the client must interact with the same server for the duration of the connection.</span></span> <span data-ttu-id="327d0-143">Pro škálování, použití Redis, to znamená, že se vyžaduje rychlé relace.</span><span class="sxs-lookup"><span data-stu-id="327d0-143">For scaleout using Redis, that means sticky sessions are required.</span></span> <span data-ttu-id="327d0-144">Pro práci s horizontálním navýšením kapacity [služby Azure SignalR](/azure/azure-signalr/), rychlé relace nejsou vyžadovány, protože služba zpracovává připojení ke klientům.</span><span class="sxs-lookup"><span data-stu-id="327d0-144">For scaleout using [Azure SignalR Service](/azure/azure-signalr/), sticky sessions are not required because the service handles connections to clients.</span></span> 

### <a name="single-hub-per-connection"></a><span data-ttu-id="327d0-145">Jedno Centrum za připojení</span><span class="sxs-lookup"><span data-stu-id="327d0-145">Single hub per connection</span></span>

<span data-ttu-id="327d0-146">Funkce SignalR technologie ASP.NET Core je zjednodušený model připojení.</span><span class="sxs-lookup"><span data-stu-id="327d0-146">In ASP.NET Core SignalR, the connection model has been simplified.</span></span> <span data-ttu-id="327d0-147">Připojení jsou provedeny přímo do jednoho rozbočovače, nikoli jednoho připojení používá sdílet přístup k více rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="327d0-147">Connections are made directly to a single hub, rather than a single connection being used to share access to multiple hubs.</span></span>

### <a name="streaming"></a><span data-ttu-id="327d0-148">Streamování</span><span class="sxs-lookup"><span data-stu-id="327d0-148">Streaming</span></span>

<span data-ttu-id="327d0-149">ASP.NET Core SignalR teď podporuje [streamovaná data](xref:signalr/streaming) z rozbočovače klientovi.</span><span class="sxs-lookup"><span data-stu-id="327d0-149">ASP.NET Core SignalR now supports [streaming data](xref:signalr/streaming) from the hub to the client.</span></span>

### <a name="state"></a><span data-ttu-id="327d0-150">Stav</span><span class="sxs-lookup"><span data-stu-id="327d0-150">State</span></span>

<span data-ttu-id="327d0-151">Umožňuje předat libovolný stav mezi klienty a centrum (často označované jako HubState) byla odebrána a také podporu pro zprávy o průběhu.</span><span class="sxs-lookup"><span data-stu-id="327d0-151">The ability to pass arbitrary state between clients and the hub (often called HubState) has been removed, as well as support for progress messages.</span></span> <span data-ttu-id="327d0-152">V tuto chvíli není nevyskytují proxy rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="327d0-152">There is no counterpart of hub proxies at the moment.</span></span>

### <a name="persistentconnection-removal"></a><span data-ttu-id="327d0-153">Odebrání PersistentConnection</span><span class="sxs-lookup"><span data-stu-id="327d0-153">PersistentConnection removal</span></span>

<span data-ttu-id="327d0-154">V knihovně SignalR technologie ASP.NET Core [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) třída odebrala.</span><span class="sxs-lookup"><span data-stu-id="327d0-154">In ASP.NET Core SignalR, the [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) class has been removed.</span></span> 

### <a name="globalhost"></a><span data-ttu-id="327d0-155">GlobalHost</span><span class="sxs-lookup"><span data-stu-id="327d0-155">GlobalHost</span></span>

<span data-ttu-id="327d0-156">ASP.NET Core je integrovaný do rozhraní injektáž závislostí (DI).</span><span class="sxs-lookup"><span data-stu-id="327d0-156">ASP.NET Core has dependency injection (DI) built into the framework.</span></span> <span data-ttu-id="327d0-157">Služby využívat pro přístup k DI [HubContext](xref:signalr/hubcontext).</span><span class="sxs-lookup"><span data-stu-id="327d0-157">Services can use DI to access the [HubContext](xref:signalr/hubcontext).</span></span> <span data-ttu-id="327d0-158">`GlobalHost` Objekt, který se používá v knihovně SignalR technologie ASP.NET k získání `HubContext` neexistuje v knihovně SignalR technologie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="327d0-158">The `GlobalHost` object that is used in ASP.NET SignalR to get a `HubContext` doesn't exist in ASP.NET Core SignalR.</span></span>

### <a name="hubpipeline"></a><span data-ttu-id="327d0-159">Kanálu rozbočovače přidán</span><span class="sxs-lookup"><span data-stu-id="327d0-159">HubPipeline</span></span>

<span data-ttu-id="327d0-160">Funkce SignalR technologie ASP.NET Core nemá podporu `HubPipeline` moduly.</span><span class="sxs-lookup"><span data-stu-id="327d0-160">ASP.NET Core SignalR doesn't have support for `HubPipeline` modules.</span></span>

## <a name="differences-on-the-client"></a><span data-ttu-id="327d0-161">Rozdíly v klientovi</span><span class="sxs-lookup"><span data-stu-id="327d0-161">Differences on the client</span></span>

### <a name="typescript"></a><span data-ttu-id="327d0-162">TypeScript</span><span class="sxs-lookup"><span data-stu-id="327d0-162">TypeScript</span></span>

<span data-ttu-id="327d0-163">Klient funkce SignalR technologie ASP.NET Core je napsána v [TypeScript](https://www.typescriptlang.org/).</span><span class="sxs-lookup"><span data-stu-id="327d0-163">The ASP.NET Core SignalR client is written in [TypeScript](https://www.typescriptlang.org/).</span></span> <span data-ttu-id="327d0-164">Můžete psát v jazyce JavaScript nebo TypeScript při použití [javascriptový klient](xref:signalr/javascript-client).</span><span class="sxs-lookup"><span data-stu-id="327d0-164">You can write in JavaScript or TypeScript when using the [JavaScript client](xref:signalr/javascript-client).</span></span>

### <a name="the-javascript-client-is-hosted-at-npmhttpswwwnpmjscom"></a><span data-ttu-id="327d0-165">JavaScript klienta je hostovaná na [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="327d0-165">The JavaScript client is hosted at [npm](https://www.npmjs.com/)</span></span>

<span data-ttu-id="327d0-166">V předchozích verzích klienta JavaScript byl získán prostřednictvím balíčku NuGet v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="327d0-166">In previous versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="327d0-167">Verze jádra [ @aspnet/signalr ](https://www.npmjs.com/package/@aspnet/signalr) balíčku npm obsahuje knihovny jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="327d0-167">For the Core versions, the [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="327d0-168">Není součástí tohoto balíčku **webové aplikace ASP.NET Core** šablony.</span><span class="sxs-lookup"><span data-stu-id="327d0-168">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="327d0-169">Získejte a nainstalujte pomocí npm `@aspnet/signalr` balíčku npm.</span><span class="sxs-lookup"><span data-stu-id="327d0-169">Use npm to obtain and install the `@aspnet/signalr` npm package.</span></span>

```console
npm init -y
npm install @aspnet/signalr
```

### <a name="jquery"></a><span data-ttu-id="327d0-170">jQuery</span><span class="sxs-lookup"><span data-stu-id="327d0-170">jQuery</span></span>

<span data-ttu-id="327d0-171">Závislost na jQuery byla odebrána, ale projekty můžete dál používat jQuery.</span><span class="sxs-lookup"><span data-stu-id="327d0-171">The dependency on jQuery has been removed, however projects can still use jQuery.</span></span>

### <a name="internet-explorer-support"></a><span data-ttu-id="327d0-172">Podpora aplikace Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="327d0-172">Internet Explorer support</span></span>

<span data-ttu-id="327d0-173">Funkce SignalR technologie ASP.NET Core vyžaduje Microsoft Internet Explorer 11 nebo novější (funkce SignalR technologie ASP.NET podporovány Microsoft Internet Explorer 8 a novější).</span><span class="sxs-lookup"><span data-stu-id="327d0-173">ASP.NET Core SignalR requires Microsoft Internet Explorer 11 or later (ASP.NET SignalR supported Microsoft Internet Explorer 8 and later).</span></span>

### <a name="javascript-client-method-syntax"></a><span data-ttu-id="327d0-174">Syntaxe využívající metody JavaScript klienta</span><span class="sxs-lookup"><span data-stu-id="327d0-174">JavaScript client method syntax</span></span>

<span data-ttu-id="327d0-175">Syntaxe jazyka JavaScript byl změněn z předchozí verze funkce SignalR.</span><span class="sxs-lookup"><span data-stu-id="327d0-175">The JavaScript syntax has changed from the previous version of SignalR.</span></span> <span data-ttu-id="327d0-176">Místo použití `$connection` objektu, vytvořte pomocí připojení [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="327d0-176">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="327d0-177">Použití [na](/javascript/api/@aspnet/signalr/HubConnection#on) metoda určují metody klienta můžete volání rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="327d0-177">Use the [on](/javascript/api/@aspnet/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = user + " says " + msg;
    log(encodedMsg);
});
```

<span data-ttu-id="327d0-178">Po vytvoření metody klienta, spusťte připojení rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="327d0-178">After creating the client method, start the hub connection.</span></span> <span data-ttu-id="327d0-179">Řetězce [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) metodou protokolu nebo zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="327d0-179">Chain a [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) method to log or handle errors.</span></span>

```javascript
connection.start().catch(err => console.error(err.toString()));
```

### <a name="hub-proxies"></a><span data-ttu-id="327d0-180">Proxy servery hub</span><span class="sxs-lookup"><span data-stu-id="327d0-180">Hub proxies</span></span>

<span data-ttu-id="327d0-181">Proxy servery hub už nebude automaticky generovány.</span><span class="sxs-lookup"><span data-stu-id="327d0-181">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="327d0-182">Místo toho název metody je předán [vyvolat](/javascript/api/%40aspnet/signalr/hubconnection#invoke) rozhraní API jako řetězec.</span><span class="sxs-lookup"><span data-stu-id="327d0-182">Instead, the method name is passed into the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) API as a string.</span></span>

### <a name="net-and-other-clients"></a><span data-ttu-id="327d0-183">.NET a další klienti</span><span class="sxs-lookup"><span data-stu-id="327d0-183">.NET and other clients</span></span>

<span data-ttu-id="327d0-184">`Microsoft.AspNetCore.SignalR.Client` Balíček NuGet obsahuje klientské knihovny .NET pro funkci SignalR technologie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="327d0-184">The `Microsoft.AspNetCore.SignalR.Client` NuGet package contains the .NET client libraries for ASP.NET Core SignalR.</span></span>

<span data-ttu-id="327d0-185">Použití [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder) vytvářet a sestavovat instanci, připojení k rozbočovači.</span><span class="sxs-lookup"><span data-stu-id="327d0-185">Use the [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder) to create and build an instance of a connection to a hub.</span></span>

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a><span data-ttu-id="327d0-186">Rozdíly horizontálním navýšením kapacity</span><span class="sxs-lookup"><span data-stu-id="327d0-186">Scaleout differences</span></span>

<span data-ttu-id="327d0-187">Funkce SignalR technologie ASP.NET podporuje SQL Server a Redis.</span><span class="sxs-lookup"><span data-stu-id="327d0-187">ASP.NET SignalR supports SQL Server and Redis.</span></span> <span data-ttu-id="327d0-188">Funkce SignalR technologie ASP.NET Core podporuje služby Azure SignalR a Redis.</span><span class="sxs-lookup"><span data-stu-id="327d0-188">ASP.NET Core SignalR supports Azure SignalR Service and Redis.</span></span>

### <a name="aspnet"></a><span data-ttu-id="327d0-189">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="327d0-189">ASP.NET</span></span>

* [<span data-ttu-id="327d0-190">Škálování aplikace SignalR službou Azure Service Bus</span><span class="sxs-lookup"><span data-stu-id="327d0-190">SignalR scaleout with Azure Service Bus</span></span>](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [<span data-ttu-id="327d0-191">Škálování aplikace SignalR službou Redis</span><span class="sxs-lookup"><span data-stu-id="327d0-191">SignalR scaleout with Redis</span></span>](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [<span data-ttu-id="327d0-192">Škálování aplikace SignalR službou SQL Server</span><span class="sxs-lookup"><span data-stu-id="327d0-192">SignalR scaleout with SQL Server</span></span>](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a><span data-ttu-id="327d0-193">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="327d0-193">ASP.NET Core</span></span>

* [<span data-ttu-id="327d0-194">Službě Azure SignalR</span><span class="sxs-lookup"><span data-stu-id="327d0-194">Azure SignalR Service</span></span>](/azure/azure-signalr/)

## <a name="additional-resources"></a><span data-ttu-id="327d0-195">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="327d0-195">Additional resources</span></span>

* [<span data-ttu-id="327d0-196">Centra</span><span class="sxs-lookup"><span data-stu-id="327d0-196">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="327d0-197">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="327d0-197">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="327d0-198">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="327d0-198">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="327d0-199">Podporované platformy</span><span class="sxs-lookup"><span data-stu-id="327d0-199">Supported platforms</span></span>](xref:signalr/supported-platforms)
