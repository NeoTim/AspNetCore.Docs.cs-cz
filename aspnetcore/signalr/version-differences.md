---
title: Rozdíly mezi SignalR a funkce SignalR technologie ASP.NET Core
author: tdykstra
description: Rozdíly mezi SignalR a funkce SignalR technologie ASP.NET Core
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.date: 11/14/2018
uid: signalr/version-differences
ms.openlocfilehash: fb10d6e62ff28128e6e9e5dcef55e44f25de8ad0
ms.sourcegitcommit: 6548c19f345850ee22b50f7ef9fca732895d9e08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2018
ms.locfileid: "53425117"
---
# <a name="differences-between-aspnet-signalr-and-aspnet-core-signalr"></a><span data-ttu-id="0e088-103">Rozdíly mezi funkce SignalR technologie ASP.NET a technologie SignalR technologie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0e088-103">Differences between ASP.NET SignalR and ASP.NET Core SignalR</span></span>

<span data-ttu-id="0e088-104">Funkce SignalR technologie ASP.NET Core není kompatibilní s klientů nebo serverů pro funkci SignalR technologie ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="0e088-104">ASP.NET Core SignalR isn't compatible with clients or servers for ASP.NET SignalR.</span></span> <span data-ttu-id="0e088-105">Tento článek podrobně popisuje funkce, které byly odstraněny nebo změněny v knihovně SignalR technologie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0e088-105">This article details features which have been removed or changed in ASP.NET Core SignalR.</span></span>

## <a name="how-to-identify-the-signalr-version"></a><span data-ttu-id="0e088-106">Jak určit verzi SignalR</span><span class="sxs-lookup"><span data-stu-id="0e088-106">How to identify the SignalR version</span></span>

|                      | <span data-ttu-id="0e088-107">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="0e088-107">ASP.NET SignalR</span></span> | <span data-ttu-id="0e088-108">Funkce SignalR technologie ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0e088-108">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="0e088-109">Balíček NuGet server</span><span class="sxs-lookup"><span data-stu-id="0e088-109">Server NuGet Package</span></span> | [<span data-ttu-id="0e088-110">Microsoft.AspNet.SignalR</span><span class="sxs-lookup"><span data-stu-id="0e088-110">Microsoft.AspNet.SignalR</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | <span data-ttu-id="0e088-111">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span><span class="sxs-lookup"><span data-stu-id="0e088-111">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span></span><br><span data-ttu-id="0e088-112">[Microsoft.AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework)</span><span class="sxs-lookup"><span data-stu-id="0e088-112">[Microsoft.AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework)</span></span> |
| <span data-ttu-id="0e088-113">Balíčky NuGet pro klienta</span><span class="sxs-lookup"><span data-stu-id="0e088-113">Client NuGet Packages</span></span> | [<span data-ttu-id="0e088-114">Microsoft.AspNet.SignalR.Client</span><span class="sxs-lookup"><span data-stu-id="0e088-114">Microsoft.AspNet.SignalR.Client</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[<span data-ttu-id="0e088-115">Microsoft.AspNet.SignalR.JS</span><span class="sxs-lookup"><span data-stu-id="0e088-115">Microsoft.AspNet.SignalR.JS</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [<span data-ttu-id="0e088-116">Microsoft.AspNetCore.SignalR.Client</span><span class="sxs-lookup"><span data-stu-id="0e088-116">Microsoft.AspNetCore.SignalR.Client</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| <span data-ttu-id="0e088-117">Npm Package klienta</span><span class="sxs-lookup"><span data-stu-id="0e088-117">Client npm Package</span></span> | [<span data-ttu-id="0e088-118">Funkce signalr</span><span class="sxs-lookup"><span data-stu-id="0e088-118">signalr</span></span>](https://www.npmjs.com/package/signalr) | [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) |
| <span data-ttu-id="0e088-119">Klientskou sadou Java</span><span class="sxs-lookup"><span data-stu-id="0e088-119">Java Client</span></span> | <span data-ttu-id="0e088-120">[Úložiště GitHub](https://github.com/SignalR/java-client) (zastaralé)</span><span class="sxs-lookup"><span data-stu-id="0e088-120">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="0e088-121">Maven balíček [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="0e088-121">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="0e088-122">Typ aplikace serveru</span><span class="sxs-lookup"><span data-stu-id="0e088-122">Server App Type</span></span> | <span data-ttu-id="0e088-123">Technologie ASP.NET (System.Web) nebo samoobslužné hostování OWIN</span><span class="sxs-lookup"><span data-stu-id="0e088-123">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="0e088-124">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0e088-124">ASP.NET Core</span></span> |
| <span data-ttu-id="0e088-125">Podporované serverové platformy</span><span class="sxs-lookup"><span data-stu-id="0e088-125">Supported Server Platforms</span></span> | <span data-ttu-id="0e088-126">Rozhraní .NET framework 4.5 nebo novější</span><span class="sxs-lookup"><span data-stu-id="0e088-126">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="0e088-127">Rozhraní .NET framework 4.6.1 nebo novější</span><span class="sxs-lookup"><span data-stu-id="0e088-127">.NET Framework 4.6.1 or later</span></span><br><span data-ttu-id="0e088-128">.NET core 2.1 nebo novější</span><span class="sxs-lookup"><span data-stu-id="0e088-128">.NET Core 2.1 or later</span></span> |

## <a name="feature-differences"></a><span data-ttu-id="0e088-129">Rozdíly ve funkcích</span><span class="sxs-lookup"><span data-stu-id="0e088-129">Feature differences</span></span>

### <a name="automatic-reconnects"></a><span data-ttu-id="0e088-130">Automatické připojování</span><span class="sxs-lookup"><span data-stu-id="0e088-130">Automatic reconnects</span></span>

<span data-ttu-id="0e088-131">Automatické připojování nepodporují funkce SignalR technologie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0e088-131">Automatic reconnects aren't supported in ASP.NET Core SignalR.</span></span> <span data-ttu-id="0e088-132">Pokud klient je odpojen, uživatel musí explicitně spustit nové připojení, aby připojení bylo možné.</span><span class="sxs-lookup"><span data-stu-id="0e088-132">If the client is disconnected, the user must explicitly start a new connection if they want to reconnect.</span></span> <span data-ttu-id="0e088-133">V funkce SignalR technologie ASP.NET SignalR pokusí znovu připojit k serveru, pokud připojení se ukončí.</span><span class="sxs-lookup"><span data-stu-id="0e088-133">In ASP.NET SignalR, SignalR attempts to reconnect to the server if the connection is dropped.</span></span> 

### <a name="protocol-support"></a><span data-ttu-id="0e088-134">Podpora protokolu</span><span class="sxs-lookup"><span data-stu-id="0e088-134">Protocol support</span></span>

<span data-ttu-id="0e088-135">Funkce SignalR technologie ASP.NET Core podporuje JSON, jakož i nové binární protokol založený na [MessagePack](xref:signalr/messagepackhubprotocol).</span><span class="sxs-lookup"><span data-stu-id="0e088-135">ASP.NET Core SignalR supports JSON, as well as a new binary protocol based on [MessagePack](xref:signalr/messagepackhubprotocol).</span></span> <span data-ttu-id="0e088-136">Kromě toho je možné vytvářet vlastní protokoly.</span><span class="sxs-lookup"><span data-stu-id="0e088-136">Additionally, custom protocols can be created.</span></span>

### <a name="transports"></a><span data-ttu-id="0e088-137">Přenosy</span><span class="sxs-lookup"><span data-stu-id="0e088-137">Transports</span></span>

<span data-ttu-id="0e088-138">Přenos navždy rámce není podporován v knihovně SignalR technologie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0e088-138">The Forever Frame transport is not supported in ASP.NET Core SignalR.</span></span>

## <a name="differences-on-the-server"></a><span data-ttu-id="0e088-139">Rozdíly na serveru</span><span class="sxs-lookup"><span data-stu-id="0e088-139">Differences on the server</span></span>

<span data-ttu-id="0e088-140">Jsou součástí knihoven na straně serveru funkce SignalR technologie ASP.NET Core [Microsoft.AspNetCore.App Microsoft.aspnetcore.all](xref:fundamentals/metapackage-app) balíček, který je součástí **webové aplikace ASP.NET Core** šablony pro syntaxi Razor a MVC projekty.</span><span class="sxs-lookup"><span data-stu-id="0e088-140">The ASP.NET Core SignalR server-side libraries are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) package that's part of the **ASP.NET Core Web Application** template for both Razor and MVC projects.</span></span>

<span data-ttu-id="0e088-141">Funkce SignalR technologie ASP.NET Core je middleware ASP.NET Core, takže musí být nakonfigurovaný pomocí volání [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="0e088-141">ASP.NET Core SignalR is an ASP.NET Core middleware, so it must be configured by calling [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSignalR()
```

<span data-ttu-id="0e088-142">Konfigurace směrování, mapování tras k rozbočovačům uvnitř [UseSignalR](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr) volání metody `Startup.Configure` metoda.</span><span class="sxs-lookup"><span data-stu-id="0e088-142">To configure routing, map routes to hubs inside the [UseSignalR](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr) method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

### <a name="sticky-sessions"></a><span data-ttu-id="0e088-143">Rychlé relace</span><span class="sxs-lookup"><span data-stu-id="0e088-143">Sticky sessions</span></span>

<span data-ttu-id="0e088-144">Model horizontálním navýšením kapacity pro funkci SignalR technologie ASP.NET umožňuje klientům znovu připojit a odesílání zpráv na libovolný server ve farmě.</span><span class="sxs-lookup"><span data-stu-id="0e088-144">The scaleout model for ASP.NET SignalR allows clients to reconnect and send messages to any server in the farm.</span></span> <span data-ttu-id="0e088-145">V knihovně SignalR technologie ASP.NET Core musí klient pracovat na stejném serveru po dobu trvání připojení.</span><span class="sxs-lookup"><span data-stu-id="0e088-145">In ASP.NET Core SignalR, the client must interact with the same server for the duration of the connection.</span></span> <span data-ttu-id="0e088-146">Pro škálování, použití Redis, to znamená, že se vyžaduje rychlé relace.</span><span class="sxs-lookup"><span data-stu-id="0e088-146">For scaleout using Redis, that means sticky sessions are required.</span></span> <span data-ttu-id="0e088-147">Pro práci s horizontálním navýšením kapacity [služby Azure SignalR](/azure/azure-signalr/), rychlé relace nejsou vyžadovány, protože služba zpracovává připojení ke klientům.</span><span class="sxs-lookup"><span data-stu-id="0e088-147">For scaleout using [Azure SignalR Service](/azure/azure-signalr/), sticky sessions are not required because the service handles connections to clients.</span></span> 

### <a name="single-hub-per-connection"></a><span data-ttu-id="0e088-148">Jedno Centrum za připojení</span><span class="sxs-lookup"><span data-stu-id="0e088-148">Single hub per connection</span></span>

<span data-ttu-id="0e088-149">Funkce SignalR technologie ASP.NET Core je zjednodušený model připojení.</span><span class="sxs-lookup"><span data-stu-id="0e088-149">In ASP.NET Core SignalR, the connection model has been simplified.</span></span> <span data-ttu-id="0e088-150">Připojení jsou provedeny přímo do jednoho rozbočovače, nikoli jednoho připojení používá sdílet přístup k více rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="0e088-150">Connections are made directly to a single hub, rather than a single connection being used to share access to multiple hubs.</span></span>

### <a name="streaming"></a><span data-ttu-id="0e088-151">Streamování</span><span class="sxs-lookup"><span data-stu-id="0e088-151">Streaming</span></span>

<span data-ttu-id="0e088-152">ASP.NET Core SignalR teď podporuje [streamovaná data](xref:signalr/streaming) z rozbočovače klientovi.</span><span class="sxs-lookup"><span data-stu-id="0e088-152">ASP.NET Core SignalR now supports [streaming data](xref:signalr/streaming) from the hub to the client.</span></span>

### <a name="state"></a><span data-ttu-id="0e088-153">Stav</span><span class="sxs-lookup"><span data-stu-id="0e088-153">State</span></span>

<span data-ttu-id="0e088-154">Umožňuje předat libovolný stav mezi klienty a centrum (často označované jako HubState) byla odebrána a také podporu pro zprávy o průběhu.</span><span class="sxs-lookup"><span data-stu-id="0e088-154">The ability to pass arbitrary state between clients and the hub (often called HubState) has been removed, as well as support for progress messages.</span></span> <span data-ttu-id="0e088-155">V tuto chvíli není nevyskytují proxy rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="0e088-155">There is no counterpart of hub proxies at the moment.</span></span>

### <a name="persistentconnection-removal"></a><span data-ttu-id="0e088-156">Odebrání PersistentConnection</span><span class="sxs-lookup"><span data-stu-id="0e088-156">PersistentConnection removal</span></span>

<span data-ttu-id="0e088-157">V knihovně SignalR technologie ASP.NET Core [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) třída odebrala.</span><span class="sxs-lookup"><span data-stu-id="0e088-157">In ASP.NET Core SignalR, the [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) class has been removed.</span></span> 

### <a name="globalhost"></a><span data-ttu-id="0e088-158">GlobalHost</span><span class="sxs-lookup"><span data-stu-id="0e088-158">GlobalHost</span></span>

<span data-ttu-id="0e088-159">ASP.NET Core je integrovaný do rozhraní injektáž závislostí (DI).</span><span class="sxs-lookup"><span data-stu-id="0e088-159">ASP.NET Core has dependency injection (DI) built into the framework.</span></span> <span data-ttu-id="0e088-160">Služby využívat pro přístup k DI [HubContext](xref:signalr/hubcontext).</span><span class="sxs-lookup"><span data-stu-id="0e088-160">Services can use DI to access the [HubContext](xref:signalr/hubcontext).</span></span> <span data-ttu-id="0e088-161">`GlobalHost` Objekt, který se používá v knihovně SignalR technologie ASP.NET k získání `HubContext` neexistuje v knihovně SignalR technologie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0e088-161">The `GlobalHost` object that is used in ASP.NET SignalR to get a `HubContext` doesn't exist in ASP.NET Core SignalR.</span></span>

### <a name="hubpipeline"></a><span data-ttu-id="0e088-162">Kanálu rozbočovače přidán</span><span class="sxs-lookup"><span data-stu-id="0e088-162">HubPipeline</span></span>

<span data-ttu-id="0e088-163">Funkce SignalR technologie ASP.NET Core nemá podporu `HubPipeline` moduly.</span><span class="sxs-lookup"><span data-stu-id="0e088-163">ASP.NET Core SignalR doesn't have support for `HubPipeline` modules.</span></span>

## <a name="differences-on-the-client"></a><span data-ttu-id="0e088-164">Rozdíly v klientovi</span><span class="sxs-lookup"><span data-stu-id="0e088-164">Differences on the client</span></span>

### <a name="typescript"></a><span data-ttu-id="0e088-165">TypeScript</span><span class="sxs-lookup"><span data-stu-id="0e088-165">TypeScript</span></span>

<span data-ttu-id="0e088-166">Klient funkce SignalR technologie ASP.NET Core je napsána v [TypeScript](https://www.typescriptlang.org/).</span><span class="sxs-lookup"><span data-stu-id="0e088-166">The ASP.NET Core SignalR client is written in [TypeScript](https://www.typescriptlang.org/).</span></span> <span data-ttu-id="0e088-167">Můžete psát v jazyce JavaScript nebo TypeScript při použití [javascriptový klient](xref:signalr/javascript-client).</span><span class="sxs-lookup"><span data-stu-id="0e088-167">You can write in JavaScript or TypeScript when using the [JavaScript client](xref:signalr/javascript-client).</span></span>

### <a name="the-javascript-client-is-hosted-at-npmhttpswwwnpmjscom"></a><span data-ttu-id="0e088-168">JavaScript klienta je hostovaná na [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="0e088-168">The JavaScript client is hosted at [npm](https://www.npmjs.com/)</span></span>

<span data-ttu-id="0e088-169">V předchozích verzích klienta JavaScript byl získán prostřednictvím balíčku NuGet v sadě Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="0e088-169">In previous versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="0e088-170">Verze jádra [ @aspnet/signalr ](https://www.npmjs.com/package/@aspnet/signalr) balíčku npm obsahuje knihovny jazyka JavaScript.</span><span class="sxs-lookup"><span data-stu-id="0e088-170">For the Core versions, the [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="0e088-171">Není součástí tohoto balíčku **webové aplikace ASP.NET Core** šablony.</span><span class="sxs-lookup"><span data-stu-id="0e088-171">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="0e088-172">Získejte a nainstalujte pomocí npm `@aspnet/signalr` balíčku npm.</span><span class="sxs-lookup"><span data-stu-id="0e088-172">Use npm to obtain and install the `@aspnet/signalr` npm package.</span></span>

```console
npm init -y
npm install @aspnet/signalr
```

### <a name="jquery"></a><span data-ttu-id="0e088-173">jQuery</span><span class="sxs-lookup"><span data-stu-id="0e088-173">jQuery</span></span>

<span data-ttu-id="0e088-174">Závislost na jQuery byla odebrána, ale projekty můžete dál používat jQuery.</span><span class="sxs-lookup"><span data-stu-id="0e088-174">The dependency on jQuery has been removed, however projects can still use jQuery.</span></span>

### <a name="internet-explorer-support"></a><span data-ttu-id="0e088-175">Podpora aplikace Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="0e088-175">Internet Explorer support</span></span>

<span data-ttu-id="0e088-176">Funkce SignalR technologie ASP.NET Core vyžaduje Microsoft Internet Explorer 11 nebo novější (funkce SignalR technologie ASP.NET podporovány Microsoft Internet Explorer 8 a novější).</span><span class="sxs-lookup"><span data-stu-id="0e088-176">ASP.NET Core SignalR requires Microsoft Internet Explorer 11 or later (ASP.NET SignalR supported Microsoft Internet Explorer 8 and later).</span></span>

### <a name="javascript-client-method-syntax"></a><span data-ttu-id="0e088-177">Syntaxe využívající metody JavaScript klienta</span><span class="sxs-lookup"><span data-stu-id="0e088-177">JavaScript client method syntax</span></span>

<span data-ttu-id="0e088-178">Syntaxe jazyka JavaScript byl změněn z předchozí verze funkce SignalR.</span><span class="sxs-lookup"><span data-stu-id="0e088-178">The JavaScript syntax has changed from the previous version of SignalR.</span></span> <span data-ttu-id="0e088-179">Místo použití `$connection` objektu, vytvořte pomocí připojení [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="0e088-179">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="0e088-180">Použití [na](/javascript/api/@aspnet/signalr/HubConnection#on) metoda určují metody klienta můžete volání rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="0e088-180">Use the [on](/javascript/api/@aspnet/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = user + " says " + msg;
    log(encodedMsg);
});
```

<span data-ttu-id="0e088-181">Po vytvoření metody klienta, spusťte připojení rozbočovače.</span><span class="sxs-lookup"><span data-stu-id="0e088-181">After creating the client method, start the hub connection.</span></span> <span data-ttu-id="0e088-182">Řetězce [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) metodou protokolu nebo zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="0e088-182">Chain a [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) method to log or handle errors.</span></span>

```javascript
connection.start().catch(err => console.error(err.toString()));
```

### <a name="hub-proxies"></a><span data-ttu-id="0e088-183">Proxy servery hub</span><span class="sxs-lookup"><span data-stu-id="0e088-183">Hub proxies</span></span>

<span data-ttu-id="0e088-184">Proxy servery hub už nebude automaticky generovány.</span><span class="sxs-lookup"><span data-stu-id="0e088-184">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="0e088-185">Místo toho název metody je předán [vyvolat](/javascript/api/%40aspnet/signalr/hubconnection#invoke) rozhraní API jako řetězec.</span><span class="sxs-lookup"><span data-stu-id="0e088-185">Instead, the method name is passed into the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) API as a string.</span></span>

### <a name="net-and-other-clients"></a><span data-ttu-id="0e088-186">.NET a další klienti</span><span class="sxs-lookup"><span data-stu-id="0e088-186">.NET and other clients</span></span>

<span data-ttu-id="0e088-187">`Microsoft.AspNetCore.SignalR.Client` Balíček NuGet obsahuje klientské knihovny .NET pro funkci SignalR technologie ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="0e088-187">The `Microsoft.AspNetCore.SignalR.Client` NuGet package contains the .NET client libraries for ASP.NET Core SignalR.</span></span>

<span data-ttu-id="0e088-188">Použití [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder) vytvářet a sestavovat instanci, připojení k rozbočovači.</span><span class="sxs-lookup"><span data-stu-id="0e088-188">Use the [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder) to create and build an instance of a connection to a hub.</span></span>

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a><span data-ttu-id="0e088-189">Rozdíly horizontálním navýšením kapacity</span><span class="sxs-lookup"><span data-stu-id="0e088-189">Scaleout differences</span></span>

<span data-ttu-id="0e088-190">Funkce SignalR technologie ASP.NET podporuje SQL Server a Redis.</span><span class="sxs-lookup"><span data-stu-id="0e088-190">ASP.NET SignalR supports SQL Server and Redis.</span></span> <span data-ttu-id="0e088-191">Funkce SignalR technologie ASP.NET Core podporuje služby Azure SignalR a Redis.</span><span class="sxs-lookup"><span data-stu-id="0e088-191">ASP.NET Core SignalR supports Azure SignalR Service and Redis.</span></span>

### <a name="aspnet"></a><span data-ttu-id="0e088-192">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="0e088-192">ASP.NET</span></span>

* [<span data-ttu-id="0e088-193">Škálování aplikace SignalR službou Azure Service Bus</span><span class="sxs-lookup"><span data-stu-id="0e088-193">SignalR scaleout with Azure Service Bus</span></span>](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [<span data-ttu-id="0e088-194">Škálování aplikace SignalR službou Redis</span><span class="sxs-lookup"><span data-stu-id="0e088-194">SignalR scaleout with Redis</span></span>](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [<span data-ttu-id="0e088-195">Škálování aplikace SignalR službou SQL Server</span><span class="sxs-lookup"><span data-stu-id="0e088-195">SignalR scaleout with SQL Server</span></span>](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a><span data-ttu-id="0e088-196">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="0e088-196">ASP.NET Core</span></span>

* [<span data-ttu-id="0e088-197">Služba Azure SignalR</span><span class="sxs-lookup"><span data-stu-id="0e088-197">Azure SignalR Service</span></span>](/azure/azure-signalr/)

## <a name="additional-resources"></a><span data-ttu-id="0e088-198">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="0e088-198">Additional resources</span></span>

* [<span data-ttu-id="0e088-199">Centra</span><span class="sxs-lookup"><span data-stu-id="0e088-199">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="0e088-200">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="0e088-200">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="0e088-201">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="0e088-201">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="0e088-202">Podporované platformy</span><span class="sxs-lookup"><span data-stu-id="0e088-202">Supported platforms</span></span>](xref:signalr/supported-platforms)
