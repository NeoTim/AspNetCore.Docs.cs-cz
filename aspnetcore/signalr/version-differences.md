---
title: Rozdíly mezi signálem a signálem ASP.NET Core
author: bradygaster
description: Rozdíly mezi signálem a signálem ASP.NET Core
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/14/2018
uid: signalr/version-differences
ms.openlocfilehash: 70b09493d9b4c96c897465d60e53e93a793c42f9
ms.sourcegitcommit: 387cf29f5d5addef2cbc70670a11d612806b36b2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70746541"
---
# <a name="differences-between-aspnet-signalr-and-aspnet-core-signalr"></a><span data-ttu-id="cd182-103">Rozdíly mezi signálem ASP.NET a signálem ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cd182-103">Differences between ASP.NET SignalR and ASP.NET Core SignalR</span></span>

<span data-ttu-id="cd182-104">Signál ASP.NET Core není kompatibilní s klienty nebo servery pro signál ASP.NET.</span><span class="sxs-lookup"><span data-stu-id="cd182-104">ASP.NET Core SignalR isn't compatible with clients or servers for ASP.NET SignalR.</span></span> <span data-ttu-id="cd182-105">Tento článek obsahuje podrobnosti o funkcích, které byly v ASP.NET Coreovém signalizaci odebrány nebo změněny.</span><span class="sxs-lookup"><span data-stu-id="cd182-105">This article details features which have been removed or changed in ASP.NET Core SignalR.</span></span>

## <a name="how-to-identify-the-signalr-version"></a><span data-ttu-id="cd182-106">Jak identifikovat verzi signalizace</span><span class="sxs-lookup"><span data-stu-id="cd182-106">How to identify the SignalR version</span></span>

|                      | <span data-ttu-id="cd182-107">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="cd182-107">ASP.NET SignalR</span></span> | <span data-ttu-id="cd182-108">ASP.NET Core signál</span><span class="sxs-lookup"><span data-stu-id="cd182-108">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="cd182-109">Balíček NuGet serveru</span><span class="sxs-lookup"><span data-stu-id="cd182-109">Server NuGet Package</span></span> | [<span data-ttu-id="cd182-110">Microsoft.AspNet.SignalR</span><span class="sxs-lookup"><span data-stu-id="cd182-110">Microsoft.AspNet.SignalR</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/) | <span data-ttu-id="cd182-111">[Microsoft. AspNetCore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span><span class="sxs-lookup"><span data-stu-id="cd182-111">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span></span><br><span data-ttu-id="cd182-112">[Microsoft. AspNetCore. Signal](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework)</span><span class="sxs-lookup"><span data-stu-id="cd182-112">[Microsoft.AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) (.NET Framework)</span></span> |
| <span data-ttu-id="cd182-113">Klientské balíčky NuGet</span><span class="sxs-lookup"><span data-stu-id="cd182-113">Client NuGet Packages</span></span> | [<span data-ttu-id="cd182-114">Microsoft. AspNet. Signaler. Client</span><span class="sxs-lookup"><span data-stu-id="cd182-114">Microsoft.AspNet.SignalR.Client</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)<br>[<span data-ttu-id="cd182-115">Microsoft.AspNet.SignalR.JS</span><span class="sxs-lookup"><span data-stu-id="cd182-115">Microsoft.AspNet.SignalR.JS</span></span>](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/) | [<span data-ttu-id="cd182-116">Microsoft. AspNetCore. Signaler. Client</span><span class="sxs-lookup"><span data-stu-id="cd182-116">Microsoft.AspNetCore.SignalR.Client</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/) |
| <span data-ttu-id="cd182-117">Balíček npm klienta</span><span class="sxs-lookup"><span data-stu-id="cd182-117">Client npm Package</span></span> | [<span data-ttu-id="cd182-118">SignalR</span><span class="sxs-lookup"><span data-stu-id="cd182-118">signalr</span></span>](https://www.npmjs.com/package/signalr) | [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) |
| <span data-ttu-id="cd182-119">Klient Java</span><span class="sxs-lookup"><span data-stu-id="cd182-119">Java Client</span></span> | <span data-ttu-id="cd182-120">[Úložiště GitHub](https://github.com/SignalR/java-client) zastaralé</span><span class="sxs-lookup"><span data-stu-id="cd182-120">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="cd182-121">Balíček Maven [com. Microsoft. Signal](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="cd182-121">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="cd182-122">Typ serverové aplikace</span><span class="sxs-lookup"><span data-stu-id="cd182-122">Server App Type</span></span> | <span data-ttu-id="cd182-123">ASP.NET (System. Web) nebo OWIN – samoobslužné hostování</span><span class="sxs-lookup"><span data-stu-id="cd182-123">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="cd182-124">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cd182-124">ASP.NET Core</span></span> |
| <span data-ttu-id="cd182-125">Podporované serverové platformy</span><span class="sxs-lookup"><span data-stu-id="cd182-125">Supported Server Platforms</span></span> | <span data-ttu-id="cd182-126">.NET Framework 4,5 nebo novější</span><span class="sxs-lookup"><span data-stu-id="cd182-126">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="cd182-127">.NET Framework 4.6.1 nebo novější</span><span class="sxs-lookup"><span data-stu-id="cd182-127">.NET Framework 4.6.1 or later</span></span><br><span data-ttu-id="cd182-128">.NET Core 2,1 nebo novější</span><span class="sxs-lookup"><span data-stu-id="cd182-128">.NET Core 2.1 or later</span></span> |

## <a name="feature-differences"></a><span data-ttu-id="cd182-129">Rozdíly ve funkcích</span><span class="sxs-lookup"><span data-stu-id="cd182-129">Feature differences</span></span>

### <a name="automatic-reconnects"></a><span data-ttu-id="cd182-130">Automatické opětovné připojení</span><span class="sxs-lookup"><span data-stu-id="cd182-130">Automatic reconnects</span></span>

<span data-ttu-id="cd182-131">Automatické opětovné připojení není v nástroji ASP.NET Core signalizace podporováno.</span><span class="sxs-lookup"><span data-stu-id="cd182-131">Automatic reconnects aren't supported in ASP.NET Core SignalR.</span></span> <span data-ttu-id="cd182-132">Pokud je klient odpojený, uživatel musí explicitně spustit nové připojení, pokud se chce znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="cd182-132">If the client is disconnected, the user must explicitly start a new connection if they want to reconnect.</span></span> <span data-ttu-id="cd182-133">V nástroji ASP.NET Signal se signál pokusí znovu připojit k serveru, pokud je připojení vyřazené.</span><span class="sxs-lookup"><span data-stu-id="cd182-133">In ASP.NET SignalR, SignalR attempts to reconnect to the server if the connection is dropped.</span></span>

### <a name="protocol-support"></a><span data-ttu-id="cd182-134">Podpora protokolů</span><span class="sxs-lookup"><span data-stu-id="cd182-134">Protocol support</span></span>

<span data-ttu-id="cd182-135">ASP.NET Core Signal podporuje JSON a také nový binární protokol založený na [MessagePack](xref:signalr/messagepackhubprotocol).</span><span class="sxs-lookup"><span data-stu-id="cd182-135">ASP.NET Core SignalR supports JSON, as well as a new binary protocol based on [MessagePack](xref:signalr/messagepackhubprotocol).</span></span> <span data-ttu-id="cd182-136">Navíc můžete vytvářet vlastní protokoly.</span><span class="sxs-lookup"><span data-stu-id="cd182-136">Additionally, custom protocols can be created.</span></span>

### <a name="transports"></a><span data-ttu-id="cd182-137">Přenosy</span><span class="sxs-lookup"><span data-stu-id="cd182-137">Transports</span></span>

<span data-ttu-id="cd182-138">V nástroji ASP.NET Core Signal není podporován přenos snímků typu navždy.</span><span class="sxs-lookup"><span data-stu-id="cd182-138">The Forever Frame transport is not supported in ASP.NET Core SignalR.</span></span>

## <a name="differences-on-the-server"></a><span data-ttu-id="cd182-139">Rozdíly na serveru</span><span class="sxs-lookup"><span data-stu-id="cd182-139">Differences on the server</span></span>

<span data-ttu-id="cd182-140">Knihovny na straně serveru ASP.NET Core signalizace jsou součástí balíčku [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) , který je součástí šablony **webové aplikace ASP.NET Core** pro Razor i projekty MVC.</span><span class="sxs-lookup"><span data-stu-id="cd182-140">The ASP.NET Core SignalR server-side libraries are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) package that's part of the **ASP.NET Core Web Application** template for both Razor and MVC projects.</span></span>

<span data-ttu-id="cd182-141">ASP.NET Core Signaler je ASP.NET Core middleware, takže se musí nakonfigurovat voláním [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="cd182-141">ASP.NET Core SignalR is an ASP.NET Core middleware, so it must be configured by calling [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cd182-142">Chcete-li konfigurovat směrování, mapujte trasy k rozbočovačům uvnitř volání metody [UseEndpoints](/dotnet/api/microsoft.aspnetcore.builder.endpointroutingapplicationbuilderextensions.useendpoints) v `Startup.Configure` metodě.</span><span class="sxs-lookup"><span data-stu-id="cd182-142">To configure routing, map routes to hubs inside the [UseEndpoints](/dotnet/api/microsoft.aspnetcore.builder.endpointroutingapplicationbuilderextensions.useendpoints) method call in the `Startup.Configure` method.</span></span>


```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="cd182-143">Chcete-li konfigurovat směrování, mapujte trasy k rozbočovačům uvnitř volání metody [UseSignalR](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr) v `Startup.Configure` metodě.</span><span class="sxs-lookup"><span data-stu-id="cd182-143">To configure routing, map routes to hubs inside the [UseSignalR](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr) method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a><span data-ttu-id="cd182-144">Rychlé relace</span><span class="sxs-lookup"><span data-stu-id="cd182-144">Sticky sessions</span></span>

<span data-ttu-id="cd182-145">Model horizontálního navýšení kapacity pro signál ASP.NET umožňuje klientům znovu se připojit a odesílat zprávy na libovolný server ve farmě.</span><span class="sxs-lookup"><span data-stu-id="cd182-145">The scaleout model for ASP.NET SignalR allows clients to reconnect and send messages to any server in the farm.</span></span> <span data-ttu-id="cd182-146">V ASP.NET Core Signal musí klient komunikovat se stejným serverem po dobu trvání připojení.</span><span class="sxs-lookup"><span data-stu-id="cd182-146">In ASP.NET Core SignalR, the client must interact with the same server for the duration of the connection.</span></span> <span data-ttu-id="cd182-147">Pro škálování pomocí Redis to znamená, že se vyžadují rychlé relace.</span><span class="sxs-lookup"><span data-stu-id="cd182-147">For scaleout using Redis, that means sticky sessions are required.</span></span> <span data-ttu-id="cd182-148">Pro škálování pomocí [služby signalizace Azure](/azure/azure-signalr/)se nevyžadují rychlé relace, protože služba zpracovává připojení ke klientům.</span><span class="sxs-lookup"><span data-stu-id="cd182-148">For scaleout using [Azure SignalR Service](/azure/azure-signalr/), sticky sessions are not required because the service handles connections to clients.</span></span>

### <a name="single-hub-per-connection"></a><span data-ttu-id="cd182-149">Jedno centrum na připojení</span><span class="sxs-lookup"><span data-stu-id="cd182-149">Single hub per connection</span></span>

<span data-ttu-id="cd182-150">V nástroji ASP.NET Core Signal byl model připojení zjednodušený.</span><span class="sxs-lookup"><span data-stu-id="cd182-150">In ASP.NET Core SignalR, the connection model has been simplified.</span></span> <span data-ttu-id="cd182-151">Připojení se vytvoří přímo v jednom rozbočovači, nikoli v jednom připojení, které se používá ke sdílení přístupu k více rozbočovačům.</span><span class="sxs-lookup"><span data-stu-id="cd182-151">Connections are made directly to a single hub, rather than a single connection being used to share access to multiple hubs.</span></span>

### <a name="streaming"></a><span data-ttu-id="cd182-152">Streamování</span><span class="sxs-lookup"><span data-stu-id="cd182-152">Streaming</span></span>

<span data-ttu-id="cd182-153">ASP.NET Core Signal teď podporuje [streamování dat](xref:signalr/streaming) z rozbočovače na klienta.</span><span class="sxs-lookup"><span data-stu-id="cd182-153">ASP.NET Core SignalR now supports [streaming data](xref:signalr/streaming) from the hub to the client.</span></span>

### <a name="state"></a><span data-ttu-id="cd182-154">Stav</span><span class="sxs-lookup"><span data-stu-id="cd182-154">State</span></span>

<span data-ttu-id="cd182-155">Byla odebrána možnost předat libovolný stav mezi klienty a centrem (často označovaným jako HubState) a také podporovat zprávy o průběhu.</span><span class="sxs-lookup"><span data-stu-id="cd182-155">The ability to pass arbitrary state between clients and the hub (often called HubState) has been removed, as well as support for progress messages.</span></span> <span data-ttu-id="cd182-156">V tuto chvíli není k dispozici žádný protějšek serverů proxy hub.</span><span class="sxs-lookup"><span data-stu-id="cd182-156">There is no counterpart of hub proxies at the moment.</span></span>

### <a name="persistentconnection-removal"></a><span data-ttu-id="cd182-157">Odebrání PersistentConnection</span><span class="sxs-lookup"><span data-stu-id="cd182-157">PersistentConnection removal</span></span>

<span data-ttu-id="cd182-158">V nástroji ASP.NET Core Signal byla odebrána třída [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) .</span><span class="sxs-lookup"><span data-stu-id="cd182-158">In ASP.NET Core SignalR, the [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) class has been removed.</span></span>

### <a name="globalhost"></a><span data-ttu-id="cd182-159">GlobalHost</span><span class="sxs-lookup"><span data-stu-id="cd182-159">GlobalHost</span></span>

<span data-ttu-id="cd182-160">ASP.NET Core má vestavěnou vkládání závislostí (DI) do rozhraní.</span><span class="sxs-lookup"><span data-stu-id="cd182-160">ASP.NET Core has dependency injection (DI) built into the framework.</span></span> <span data-ttu-id="cd182-161">Služby můžou k přístupu k [HubContext](xref:signalr/hubcontext)používat di.</span><span class="sxs-lookup"><span data-stu-id="cd182-161">Services can use DI to access the [HubContext](xref:signalr/hubcontext).</span></span> <span data-ttu-id="cd182-162">Objekt, který se používá v nástroji ASP.NET Signal k `HubContext` získání neexistence v signálu ASP.NET Core. `GlobalHost`</span><span class="sxs-lookup"><span data-stu-id="cd182-162">The `GlobalHost` object that is used in ASP.NET SignalR to get a `HubContext` doesn't exist in ASP.NET Core SignalR.</span></span>

### <a name="hubpipeline"></a><span data-ttu-id="cd182-163">HubPipeline</span><span class="sxs-lookup"><span data-stu-id="cd182-163">HubPipeline</span></span>

<span data-ttu-id="cd182-164">Signál ASP.NET Core nemá podporu pro `HubPipeline` moduly.</span><span class="sxs-lookup"><span data-stu-id="cd182-164">ASP.NET Core SignalR doesn't have support for `HubPipeline` modules.</span></span>

## <a name="differences-on-the-client"></a><span data-ttu-id="cd182-165">Rozdíly na klientovi</span><span class="sxs-lookup"><span data-stu-id="cd182-165">Differences on the client</span></span>

### <a name="typescript"></a><span data-ttu-id="cd182-166">TypeScript</span><span class="sxs-lookup"><span data-stu-id="cd182-166">TypeScript</span></span>

<span data-ttu-id="cd182-167">Klient signalizace ASP.NET Core je napsán v [TypeScript](https://www.typescriptlang.org/).</span><span class="sxs-lookup"><span data-stu-id="cd182-167">The ASP.NET Core SignalR client is written in [TypeScript](https://www.typescriptlang.org/).</span></span> <span data-ttu-id="cd182-168">Při použití [klienta jazyka JavaScript](xref:signalr/javascript-client)můžete psát v JavaScriptu nebo TypeScript.</span><span class="sxs-lookup"><span data-stu-id="cd182-168">You can write in JavaScript or TypeScript when using the [JavaScript client](xref:signalr/javascript-client).</span></span>

### <a name="the-javascript-client-is-hosted-at-npmhttpswwwnpmjscom"></a><span data-ttu-id="cd182-169">JavaScriptový klient je hostovaný na [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="cd182-169">The JavaScript client is hosted at [npm](https://www.npmjs.com/)</span></span>

<span data-ttu-id="cd182-170">V předchozích verzích byl klient jazyka JavaScript získaný prostřednictvím balíčku NuGet v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="cd182-170">In previous versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="cd182-171">Pro základní verze [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) obsahuje balíček npm knihovny JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="cd182-171">For the Core versions, the [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="cd182-172">Tento balíček není zahrnutý v šabloně **webové aplikace ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="cd182-172">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="cd182-173">Použijte npm k získání a instalaci `@aspnet/signalr` balíčku npm.</span><span class="sxs-lookup"><span data-stu-id="cd182-173">Use npm to obtain and install the `@aspnet/signalr` npm package.</span></span>

```console
npm init -y
npm install @aspnet/signalr
```

### <a name="jquery"></a><span data-ttu-id="cd182-174">jQuery</span><span class="sxs-lookup"><span data-stu-id="cd182-174">jQuery</span></span>

<span data-ttu-id="cd182-175">Závislost na jQuery se odebrala, ale projekty můžou pořád používat jQuery.</span><span class="sxs-lookup"><span data-stu-id="cd182-175">The dependency on jQuery has been removed, however projects can still use jQuery.</span></span>

### <a name="internet-explorer-support"></a><span data-ttu-id="cd182-176">Podpora aplikace Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="cd182-176">Internet Explorer support</span></span>

<span data-ttu-id="cd182-177">ASP.NET Core Signal vyžaduje aplikaci Microsoft Internet Explorer 11 nebo novější (ASP.NET Signal podporuje Microsoft Internet Explorer 8 a novější).</span><span class="sxs-lookup"><span data-stu-id="cd182-177">ASP.NET Core SignalR requires Microsoft Internet Explorer 11 or later (ASP.NET SignalR supported Microsoft Internet Explorer 8 and later).</span></span>

### <a name="javascript-client-method-syntax"></a><span data-ttu-id="cd182-178">Syntaxe metody klienta jazyka JavaScript</span><span class="sxs-lookup"><span data-stu-id="cd182-178">JavaScript client method syntax</span></span>

<span data-ttu-id="cd182-179">Syntaxe jazyka JavaScript se změnila z předchozí verze nástroje Signal.</span><span class="sxs-lookup"><span data-stu-id="cd182-179">The JavaScript syntax has changed from the previous version of SignalR.</span></span> <span data-ttu-id="cd182-180">Místo použití `$connection` objektu vytvořte připojení pomocí rozhraní [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) API.</span><span class="sxs-lookup"><span data-stu-id="cd182-180">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="cd182-181">Použijte metodu [on](/javascript/api/@aspnet/signalr/HubConnection#on) k určení klientských metod, které může centrum volat.</span><span class="sxs-lookup"><span data-stu-id="cd182-181">Use the [on](/javascript/api/@aspnet/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = user + " says " + msg;
    log(encodedMsg);
});
```

<span data-ttu-id="cd182-182">Po vytvoření metody klienta spusťte připojení k rozbočovači.</span><span class="sxs-lookup"><span data-stu-id="cd182-182">After creating the client method, start the hub connection.</span></span> <span data-ttu-id="cd182-183">Řetězení metody [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) pro protokolování a zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="cd182-183">Chain a [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) method to log or handle errors.</span></span>

```javascript
connection.start().catch(err => console.error(err.toString()));
```

### <a name="hub-proxies"></a><span data-ttu-id="cd182-184">Proxy servery centra</span><span class="sxs-lookup"><span data-stu-id="cd182-184">Hub proxies</span></span>

<span data-ttu-id="cd182-185">Proxy servery centra se už negenerují automaticky.</span><span class="sxs-lookup"><span data-stu-id="cd182-185">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="cd182-186">Místo toho je název metody předán do [vyvolání](/javascript/api/%40aspnet/signalr/hubconnection#invoke) rozhraní API jako řetězec.</span><span class="sxs-lookup"><span data-stu-id="cd182-186">Instead, the method name is passed into the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) API as a string.</span></span>

### <a name="net-and-other-clients"></a><span data-ttu-id="cd182-187">.NET a další klienti</span><span class="sxs-lookup"><span data-stu-id="cd182-187">.NET and other clients</span></span>

<span data-ttu-id="cd182-188">Balíček `Microsoft.AspNetCore.SignalR.Client` NuGet obsahuje klientské knihovny .NET pro signál ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="cd182-188">The `Microsoft.AspNetCore.SignalR.Client` NuGet package contains the .NET client libraries for ASP.NET Core SignalR.</span></span>

<span data-ttu-id="cd182-189">Pomocí [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder) můžete vytvořit a sestavit instanci připojení k rozbočovači.</span><span class="sxs-lookup"><span data-stu-id="cd182-189">Use the [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder) to create and build an instance of a connection to a hub.</span></span>

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a><span data-ttu-id="cd182-190">Rozdíly ve škálování</span><span class="sxs-lookup"><span data-stu-id="cd182-190">Scaleout differences</span></span>

<span data-ttu-id="cd182-191">Signál ASP.NET podporuje SQL Server a Redis.</span><span class="sxs-lookup"><span data-stu-id="cd182-191">ASP.NET SignalR supports SQL Server and Redis.</span></span> <span data-ttu-id="cd182-192">ASP.NET Core Signal podporuje službu a Redis služby Azure Signal.</span><span class="sxs-lookup"><span data-stu-id="cd182-192">ASP.NET Core SignalR supports Azure SignalR Service and Redis.</span></span>

### <a name="aspnet"></a><span data-ttu-id="cd182-193">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="cd182-193">ASP.NET</span></span>

* [<span data-ttu-id="cd182-194">Škálování signálu pomocí Azure Service Bus</span><span class="sxs-lookup"><span data-stu-id="cd182-194">SignalR scaleout with Azure Service Bus</span></span>](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)
* [<span data-ttu-id="cd182-195">Škálování signálu pomocí Redis</span><span class="sxs-lookup"><span data-stu-id="cd182-195">SignalR scaleout with Redis</span></span>](/aspnet/signalr/overview/performance/scaleout-with-redis)
* [<span data-ttu-id="cd182-196">Škálování signálu pomocí SQL Server</span><span class="sxs-lookup"><span data-stu-id="cd182-196">SignalR scaleout with SQL Server</span></span>](/aspnet/signalr/overview/performance/scaleout-with-sql-server)

### <a name="aspnet-core"></a><span data-ttu-id="cd182-197">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="cd182-197">ASP.NET Core</span></span>

* [<span data-ttu-id="cd182-198">Služba Azure SignalR</span><span class="sxs-lookup"><span data-stu-id="cd182-198">Azure SignalR Service</span></span>](/azure/azure-signalr/)
* [<span data-ttu-id="cd182-199">Redis</span><span class="sxs-lookup"><span data-stu-id="cd182-199">Redis Backplane</span></span>](xref:signalr/redis-backplane)

## <a name="additional-resources"></a><span data-ttu-id="cd182-200">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="cd182-200">Additional resources</span></span>

* [<span data-ttu-id="cd182-201">Centra</span><span class="sxs-lookup"><span data-stu-id="cd182-201">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="cd182-202">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="cd182-202">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="cd182-203">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="cd182-203">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="cd182-204">Podporované platformy</span><span class="sxs-lookup"><span data-stu-id="cd182-204">Supported platforms</span></span>](xref:signalr/supported-platforms)
