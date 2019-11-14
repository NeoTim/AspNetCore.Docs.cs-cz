---
title: Rozdíly mezi SignalR a ASP.NET Core SignalR
author: bradygaster
description: Rozdíly mezi SignalR a ASP.NET Core SignalR
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/version-differences
ms.openlocfilehash: 0f644c132b0fcf9a0ecf0ab181791a6477c97f76
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963726"
---
# <a name="differences-between-aspnet-opno-locsignalr-and-aspnet-core-opno-locsignalr"></a><span data-ttu-id="c347f-103">Rozdíly mezi ASP.NET SignalR a ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="c347f-103">Differences between ASP.NET SignalR and ASP.NET Core SignalR</span></span>

<span data-ttu-id="c347f-104">ASP.NET Core SignalR není kompatibilní s klienty nebo servery pro ASP.NET SignalR.</span><span class="sxs-lookup"><span data-stu-id="c347f-104">ASP.NET Core SignalR isn't compatible with clients or servers for ASP.NET SignalR.</span></span> <span data-ttu-id="c347f-105">Tento článek obsahuje podrobnosti o funkcích, které se v ASP.NET Core SignalRodebraly nebo změnily.</span><span class="sxs-lookup"><span data-stu-id="c347f-105">This article details features which have been removed or changed in ASP.NET Core SignalR.</span></span>

## <a name="how-to-identify-the-opno-locsignalr-version"></a><span data-ttu-id="c347f-106">Jak identifikovat SignalR verzi</span><span class="sxs-lookup"><span data-stu-id="c347f-106">How to identify the SignalR version</span></span>

|                      | <span data-ttu-id="c347f-107">ASP.NET SignalR</span><span class="sxs-lookup"><span data-stu-id="c347f-107">ASP.NET SignalR</span></span> | <span data-ttu-id="c347f-108">ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="c347f-108">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="c347f-109">Balíček NuGet serveru</span><span class="sxs-lookup"><span data-stu-id="c347f-109">Server NuGet Package</span></span> | <span data-ttu-id="c347f-110">[Microsoft. AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="c347f-110">[Microsoft.AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span></span> | <span data-ttu-id="c347f-111">[Microsoft. AspNetCore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span><span class="sxs-lookup"><span data-stu-id="c347f-111">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span></span><br><span data-ttu-id="c347f-112">[Microsoft. AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="c347f-112">[Microsoft.AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span></span> <span data-ttu-id="c347f-113">(.NET Framework)</span><span class="sxs-lookup"><span data-stu-id="c347f-113">(.NET Framework)</span></span> |
| <span data-ttu-id="c347f-114">Klientské balíčky NuGet</span><span class="sxs-lookup"><span data-stu-id="c347f-114">Client NuGet Packages</span></span> | <span data-ttu-id="c347f-115">[Microsoft. AspNet.SignalR. Služba](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="c347f-115">[Microsoft.AspNet.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span></span><br><span data-ttu-id="c347f-116">[Microsoft. AspNet.SignalR. JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span><span class="sxs-lookup"><span data-stu-id="c347f-116">[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span></span> | <span data-ttu-id="c347f-117">[Microsoft. AspNetCore.SignalR. Služba](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="c347f-117">[Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span></span> |
| <span data-ttu-id="c347f-118">Balíček npm klienta</span><span class="sxs-lookup"><span data-stu-id="c347f-118">Client npm Package</span></span> | [<span data-ttu-id="c347f-119">SignalR</span><span class="sxs-lookup"><span data-stu-id="c347f-119">signalr</span></span>](https://www.npmjs.com/package/signalr) | [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) |
| <span data-ttu-id="c347f-120">Klient Java</span><span class="sxs-lookup"><span data-stu-id="c347f-120">Java Client</span></span> | <span data-ttu-id="c347f-121">[Úložiště GitHub](https://github.com/SignalR/java-client) (zastaralé)</span><span class="sxs-lookup"><span data-stu-id="c347f-121">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="c347f-122">Balíček Maven [com. Microsoft. Signal](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="c347f-122">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="c347f-123">Typ serverové aplikace</span><span class="sxs-lookup"><span data-stu-id="c347f-123">Server App Type</span></span> | <span data-ttu-id="c347f-124">ASP.NET (System. Web) nebo OWIN – samoobslužné hostování</span><span class="sxs-lookup"><span data-stu-id="c347f-124">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="c347f-125">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c347f-125">ASP.NET Core</span></span> |
| <span data-ttu-id="c347f-126">Podporované serverové platformy</span><span class="sxs-lookup"><span data-stu-id="c347f-126">Supported Server Platforms</span></span> | <span data-ttu-id="c347f-127">.NET Framework 4,5 nebo novější</span><span class="sxs-lookup"><span data-stu-id="c347f-127">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="c347f-128">.NET Framework 4.6.1 nebo novější</span><span class="sxs-lookup"><span data-stu-id="c347f-128">.NET Framework 4.6.1 or later</span></span><br><span data-ttu-id="c347f-129">.NET Core 2,1 nebo novější</span><span class="sxs-lookup"><span data-stu-id="c347f-129">.NET Core 2.1 or later</span></span> |

## <a name="feature-differences"></a><span data-ttu-id="c347f-130">Rozdíly ve funkcích</span><span class="sxs-lookup"><span data-stu-id="c347f-130">Feature differences</span></span>

### <a name="automatic-reconnects"></a><span data-ttu-id="c347f-131">Automatické opětovné připojení</span><span class="sxs-lookup"><span data-stu-id="c347f-131">Automatic reconnects</span></span>

<span data-ttu-id="c347f-132">Automatické opětovné připojení nejsou v ASP.NET Core SignalRpodporovaná.</span><span class="sxs-lookup"><span data-stu-id="c347f-132">Automatic reconnects aren't supported in ASP.NET Core SignalR.</span></span> <span data-ttu-id="c347f-133">Pokud je klient odpojený, uživatel musí explicitně spustit nové připojení, pokud se chce znovu připojit.</span><span class="sxs-lookup"><span data-stu-id="c347f-133">If the client is disconnected, the user must explicitly start a new connection if they want to reconnect.</span></span> <span data-ttu-id="c347f-134">V ASP.NET SignalRse SignalR pokusí znovu připojit k serveru, pokud je připojení vyřazené.</span><span class="sxs-lookup"><span data-stu-id="c347f-134">In ASP.NET SignalR, SignalR attempts to reconnect to the server if the connection is dropped.</span></span>

### <a name="protocol-support"></a><span data-ttu-id="c347f-135">Podpora protokolů</span><span class="sxs-lookup"><span data-stu-id="c347f-135">Protocol support</span></span>

<span data-ttu-id="c347f-136">ASP.NET Core SignalR podporuje JSON a také nový binární protokol založený na [MessagePack](xref:signalr/messagepackhubprotocol).</span><span class="sxs-lookup"><span data-stu-id="c347f-136">ASP.NET Core SignalR supports JSON, as well as a new binary protocol based on [MessagePack](xref:signalr/messagepackhubprotocol).</span></span> <span data-ttu-id="c347f-137">Navíc můžete vytvářet vlastní protokoly.</span><span class="sxs-lookup"><span data-stu-id="c347f-137">Additionally, custom protocols can be created.</span></span>

### <a name="transports"></a><span data-ttu-id="c347f-138">Přenosy</span><span class="sxs-lookup"><span data-stu-id="c347f-138">Transports</span></span>

<span data-ttu-id="c347f-139">Přenos snímků navždy se v ASP.NET Core SignalRnepodporuje.</span><span class="sxs-lookup"><span data-stu-id="c347f-139">The Forever Frame transport is not supported in ASP.NET Core SignalR.</span></span>

## <a name="differences-on-the-server"></a><span data-ttu-id="c347f-140">Rozdíly na serveru</span><span class="sxs-lookup"><span data-stu-id="c347f-140">Differences on the server</span></span>

<span data-ttu-id="c347f-141">ASP.NET Core SignalR knihovny na straně serveru jsou součástí balíčku [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) , který je součástí šablony **webové aplikace ASP.NET Core** pro projekty Razor a MVC.</span><span class="sxs-lookup"><span data-stu-id="c347f-141">The ASP.NET Core SignalR server-side libraries are included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) package that's part of the **ASP.NET Core Web Application** template for both Razor and MVC projects.</span></span>

<span data-ttu-id="c347f-142">ASP.NET Core SignalR je ASP.NET Core middleware, takže se musí nakonfigurovat voláním [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) v `Startup.ConfigureServices`.</span><span class="sxs-lookup"><span data-stu-id="c347f-142">ASP.NET Core SignalR is an ASP.NET Core middleware, so it must be configured by calling [AddSignalR](/dotnet/api/microsoft.extensions.dependencyinjection.signalrdependencyinjectionextensions.addsignalr) in `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="c347f-143">Chcete-li konfigurovat směrování, namapujte trasy na rozbočovače uvnitř volání metody [UseEndpoints](/dotnet/api/microsoft.aspnetcore.builder.endpointroutingapplicationbuilderextensions.useendpoints) v metodě `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="c347f-143">To configure routing, map routes to hubs inside the [UseEndpoints](/dotnet/api/microsoft.aspnetcore.builder.endpointroutingapplicationbuilderextensions.useendpoints) method call in the `Startup.Configure` method.</span></span>


```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="c347f-144">Chcete-li konfigurovat směrování, namapujte trasy na rozbočovače uvnitř volání metody [UseSignalR](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr) v metodě `Startup.Configure`.</span><span class="sxs-lookup"><span data-stu-id="c347f-144">To configure routing, map routes to hubs inside the [UseSignalR](/dotnet/api/microsoft.aspnetcore.builder.signalrappbuilderextensions.usesignalr) method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a><span data-ttu-id="c347f-145">Rychlé relace</span><span class="sxs-lookup"><span data-stu-id="c347f-145">Sticky sessions</span></span>

<span data-ttu-id="c347f-146">Model škálování pro ASP.NET SignalR umožňuje klientům znovu se připojit a odesílat zprávy na libovolný server ve farmě.</span><span class="sxs-lookup"><span data-stu-id="c347f-146">The scaleout model for ASP.NET SignalR allows clients to reconnect and send messages to any server in the farm.</span></span> <span data-ttu-id="c347f-147">V ASP.NET Core SignalRmusí klient spolupracovat se stejným serverem po dobu trvání připojení.</span><span class="sxs-lookup"><span data-stu-id="c347f-147">In ASP.NET Core SignalR, the client must interact with the same server for the duration of the connection.</span></span> <span data-ttu-id="c347f-148">Pro škálování pomocí Redis to znamená, že se vyžadují rychlé relace.</span><span class="sxs-lookup"><span data-stu-id="c347f-148">For scaleout using Redis, that means sticky sessions are required.</span></span> <span data-ttu-id="c347f-149">Pro škálování pomocí [služby Azure SignalR](/azure/azure-signalr/)se nevyžadují rychlé relace, protože služba zpracovává připojení ke klientům.</span><span class="sxs-lookup"><span data-stu-id="c347f-149">For scaleout using [Azure SignalR Service](/azure/azure-signalr/), sticky sessions are not required because the service handles connections to clients.</span></span>

### <a name="single-hub-per-connection"></a><span data-ttu-id="c347f-150">Jedno centrum na připojení</span><span class="sxs-lookup"><span data-stu-id="c347f-150">Single hub per connection</span></span>

<span data-ttu-id="c347f-151">V ASP.NET Core SignalRbyl model připojení zjednodušený.</span><span class="sxs-lookup"><span data-stu-id="c347f-151">In ASP.NET Core SignalR, the connection model has been simplified.</span></span> <span data-ttu-id="c347f-152">Připojení se vytvoří přímo v jednom rozbočovači, nikoli v jednom připojení, které se používá ke sdílení přístupu k více rozbočovačům.</span><span class="sxs-lookup"><span data-stu-id="c347f-152">Connections are made directly to a single hub, rather than a single connection being used to share access to multiple hubs.</span></span>

### <a name="streaming"></a><span data-ttu-id="c347f-153">Streamování</span><span class="sxs-lookup"><span data-stu-id="c347f-153">Streaming</span></span>

<span data-ttu-id="c347f-154">ASP.NET Core SignalR teď podporuje [streamování dat](xref:signalr/streaming) z rozbočovače na klienta.</span><span class="sxs-lookup"><span data-stu-id="c347f-154">ASP.NET Core SignalR now supports [streaming data](xref:signalr/streaming) from the hub to the client.</span></span>

### <a name="state"></a><span data-ttu-id="c347f-155">Stav</span><span class="sxs-lookup"><span data-stu-id="c347f-155">State</span></span>

<span data-ttu-id="c347f-156">Byla odebrána možnost předat libovolný stav mezi klienty a centrem (často označovaným jako HubState) a také podporovat zprávy o průběhu.</span><span class="sxs-lookup"><span data-stu-id="c347f-156">The ability to pass arbitrary state between clients and the hub (often called HubState) has been removed, as well as support for progress messages.</span></span> <span data-ttu-id="c347f-157">V tuto chvíli není k dispozici žádný protějšek serverů proxy hub.</span><span class="sxs-lookup"><span data-stu-id="c347f-157">There is no counterpart of hub proxies at the moment.</span></span>

### <a name="persistentconnection-removal"></a><span data-ttu-id="c347f-158">Odebrání PersistentConnection</span><span class="sxs-lookup"><span data-stu-id="c347f-158">PersistentConnection removal</span></span>

<span data-ttu-id="c347f-159">V ASP.NET Core SignalRbyla odebrána třída [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) .</span><span class="sxs-lookup"><span data-stu-id="c347f-159">In ASP.NET Core SignalR, the [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) class has been removed.</span></span>

### <a name="globalhost"></a><span data-ttu-id="c347f-160">GlobalHost</span><span class="sxs-lookup"><span data-stu-id="c347f-160">GlobalHost</span></span>

<span data-ttu-id="c347f-161">ASP.NET Core má vestavěnou vkládání závislostí (DI) do rozhraní.</span><span class="sxs-lookup"><span data-stu-id="c347f-161">ASP.NET Core has dependency injection (DI) built into the framework.</span></span> <span data-ttu-id="c347f-162">Služby můžou k přístupu k [HubContext](xref:signalr/hubcontext)používat di.</span><span class="sxs-lookup"><span data-stu-id="c347f-162">Services can use DI to access the [HubContext](xref:signalr/hubcontext).</span></span> <span data-ttu-id="c347f-163">Objekt `GlobalHost`, který se používá v ASP.NET SignalR k získání `HubContext` v ASP.NET Core SignalRneexistuje.</span><span class="sxs-lookup"><span data-stu-id="c347f-163">The `GlobalHost` object that is used in ASP.NET SignalR to get a `HubContext` doesn't exist in ASP.NET Core SignalR.</span></span>

### <a name="hubpipeline"></a><span data-ttu-id="c347f-164">HubPipeline</span><span class="sxs-lookup"><span data-stu-id="c347f-164">HubPipeline</span></span>

<span data-ttu-id="c347f-165">SignalR ASP.NET Core nemá podporu pro moduly `HubPipeline`.</span><span class="sxs-lookup"><span data-stu-id="c347f-165">ASP.NET Core SignalR doesn't have support for `HubPipeline` modules.</span></span>

## <a name="differences-on-the-client"></a><span data-ttu-id="c347f-166">Rozdíly na klientovi</span><span class="sxs-lookup"><span data-stu-id="c347f-166">Differences on the client</span></span>

### <a name="typescript"></a><span data-ttu-id="c347f-167">TypeScript</span><span class="sxs-lookup"><span data-stu-id="c347f-167">TypeScript</span></span>

<span data-ttu-id="c347f-168">Klient SignalR ASP.NET Core je napsán v [TypeScript](https://www.typescriptlang.org/).</span><span class="sxs-lookup"><span data-stu-id="c347f-168">The ASP.NET Core SignalR client is written in [TypeScript](https://www.typescriptlang.org/).</span></span> <span data-ttu-id="c347f-169">Při použití [klienta jazyka JavaScript](xref:signalr/javascript-client)můžete psát v JavaScriptu nebo TypeScript.</span><span class="sxs-lookup"><span data-stu-id="c347f-169">You can write in JavaScript or TypeScript when using the [JavaScript client](xref:signalr/javascript-client).</span></span>

### <a name="the-javascript-client-is-hosted-at-npmhttpswwwnpmjscom"></a><span data-ttu-id="c347f-170">JavaScriptový klient je hostovaný na [npm](https://www.npmjs.com/)</span><span class="sxs-lookup"><span data-stu-id="c347f-170">The JavaScript client is hosted at [npm](https://www.npmjs.com/)</span></span>

<span data-ttu-id="c347f-171">V předchozích verzích byl klient jazyka JavaScript získaný prostřednictvím balíčku NuGet v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="c347f-171">In previous versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="c347f-172">Pro základní verze obsahuje balíček [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) npm knihovny JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="c347f-172">For the Core versions, the [@aspnet/signalr](https://www.npmjs.com/package/@aspnet/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="c347f-173">Tento balíček není zahrnutý v šabloně **webové aplikace ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="c347f-173">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="c347f-174">K získání a instalaci balíčku `@aspnet/signalr` npm použijte npm.</span><span class="sxs-lookup"><span data-stu-id="c347f-174">Use npm to obtain and install the `@aspnet/signalr` npm package.</span></span>

```console
npm init -y
npm install @aspnet/signalr
```

### <a name="jquery"></a><span data-ttu-id="c347f-175">jQuery</span><span class="sxs-lookup"><span data-stu-id="c347f-175">jQuery</span></span>

<span data-ttu-id="c347f-176">Závislost na jQuery se odebrala, ale projekty můžou pořád používat jQuery.</span><span class="sxs-lookup"><span data-stu-id="c347f-176">The dependency on jQuery has been removed, however projects can still use jQuery.</span></span>

### <a name="internet-explorer-support"></a><span data-ttu-id="c347f-177">Podpora aplikace Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="c347f-177">Internet Explorer support</span></span>

<span data-ttu-id="c347f-178">ASP.NET Core SignalR vyžaduje Microsoft Internet Explorer 11 nebo novější (ASP.NET SignalR podporuje Microsoft Internet Explorer 8 a novější).</span><span class="sxs-lookup"><span data-stu-id="c347f-178">ASP.NET Core SignalR requires Microsoft Internet Explorer 11 or later (ASP.NET SignalR supported Microsoft Internet Explorer 8 and later).</span></span>

### <a name="javascript-client-method-syntax"></a><span data-ttu-id="c347f-179">Syntaxe metody klienta jazyka JavaScript</span><span class="sxs-lookup"><span data-stu-id="c347f-179">JavaScript client method syntax</span></span>

<span data-ttu-id="c347f-180">Syntaxe jazyka JavaScript se změnila z předchozí verze SignalR.</span><span class="sxs-lookup"><span data-stu-id="c347f-180">The JavaScript syntax has changed from the previous version of SignalR.</span></span> <span data-ttu-id="c347f-181">Místo použití objektu `$connection` vytvořte připojení pomocí rozhraní [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) API.</span><span class="sxs-lookup"><span data-stu-id="c347f-181">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/%40aspnet/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="c347f-182">Použijte metodu [on](/javascript/api/@aspnet/signalr/HubConnection#on) k určení klientských metod, které může centrum volat.</span><span class="sxs-lookup"><span data-stu-id="c347f-182">Use the [on](/javascript/api/@aspnet/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = user + " says " + msg;
    log(encodedMsg);
});
```

<span data-ttu-id="c347f-183">Po vytvoření metody klienta spusťte připojení k rozbočovači.</span><span class="sxs-lookup"><span data-stu-id="c347f-183">After creating the client method, start the hub connection.</span></span> <span data-ttu-id="c347f-184">Řetězení metody [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) pro protokolování a zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="c347f-184">Chain a [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) method to log or handle errors.</span></span>

```javascript
connection.start().catch(err => console.error(err.toString()));
```

### <a name="hub-proxies"></a><span data-ttu-id="c347f-185">Proxy servery centra</span><span class="sxs-lookup"><span data-stu-id="c347f-185">Hub proxies</span></span>

<span data-ttu-id="c347f-186">Proxy servery centra se už negenerují automaticky.</span><span class="sxs-lookup"><span data-stu-id="c347f-186">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="c347f-187">Místo toho je název metody předán do [vyvolání](/javascript/api/%40aspnet/signalr/hubconnection#invoke) rozhraní API jako řetězec.</span><span class="sxs-lookup"><span data-stu-id="c347f-187">Instead, the method name is passed into the [invoke](/javascript/api/%40aspnet/signalr/hubconnection#invoke) API as a string.</span></span>

### <a name="net-and-other-clients"></a><span data-ttu-id="c347f-188">.NET a další klienti</span><span class="sxs-lookup"><span data-stu-id="c347f-188">.NET and other clients</span></span>

<span data-ttu-id="c347f-189">Balíček NuGet `Microsoft.AspNetCore.SignalR.Client` obsahuje klientské knihovny .NET pro SignalRASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="c347f-189">The `Microsoft.AspNetCore.SignalR.Client` NuGet package contains the .NET client libraries for ASP.NET Core SignalR.</span></span>

<span data-ttu-id="c347f-190">Pomocí [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder) můžete vytvořit a sestavit instanci připojení k rozbočovači.</span><span class="sxs-lookup"><span data-stu-id="c347f-190">Use the [HubConnectionBuilder](/dotnet/api/microsoft.aspnetcore.signalr.client.hubconnectionbuilder) to create and build an instance of a connection to a hub.</span></span>

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a><span data-ttu-id="c347f-191">Rozdíly ve škálování</span><span class="sxs-lookup"><span data-stu-id="c347f-191">Scaleout differences</span></span>

<span data-ttu-id="c347f-192">ASP.NET SignalR podporuje SQL Server a Redis.</span><span class="sxs-lookup"><span data-stu-id="c347f-192">ASP.NET SignalR supports SQL Server and Redis.</span></span> <span data-ttu-id="c347f-193">ASP.NET Core SignalR podporuje službu Azure SignalR a Redis.</span><span class="sxs-lookup"><span data-stu-id="c347f-193">ASP.NET Core SignalR supports Azure SignalR Service and Redis.</span></span>

### <a name="aspnet"></a><span data-ttu-id="c347f-194">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="c347f-194">ASP.NET</span></span>

* <span data-ttu-id="c347f-195">[SignalR škálování s využitím Azure Service Bus](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)</span><span class="sxs-lookup"><span data-stu-id="c347f-195">[SignalR scaleout with Azure Service Bus](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)</span></span>
* <span data-ttu-id="c347f-196">[SignalR škálování pomocí Redis](/aspnet/signalr/overview/performance/scaleout-with-redis)</span><span class="sxs-lookup"><span data-stu-id="c347f-196">[SignalR scaleout with Redis](/aspnet/signalr/overview/performance/scaleout-with-redis)</span></span>
* <span data-ttu-id="c347f-197">[SignalR škálování s využitím SQL Server](/aspnet/signalr/overview/performance/scaleout-with-sql-server)</span><span class="sxs-lookup"><span data-stu-id="c347f-197">[SignalR scaleout with SQL Server](/aspnet/signalr/overview/performance/scaleout-with-sql-server)</span></span>

### <a name="aspnet-core"></a><span data-ttu-id="c347f-198">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="c347f-198">ASP.NET Core</span></span>

* <span data-ttu-id="c347f-199">[Služba Azure SignalR](/azure/azure-signalr/)</span><span class="sxs-lookup"><span data-stu-id="c347f-199">[Azure SignalR Service](/azure/azure-signalr/)</span></span>
* [<span data-ttu-id="c347f-200">Redis</span><span class="sxs-lookup"><span data-stu-id="c347f-200">Redis Backplane</span></span>](xref:signalr/redis-backplane)

## <a name="additional-resources"></a><span data-ttu-id="c347f-201">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="c347f-201">Additional resources</span></span>

* [<span data-ttu-id="c347f-202">Centra</span><span class="sxs-lookup"><span data-stu-id="c347f-202">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="c347f-203">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="c347f-203">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="c347f-204">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="c347f-204">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="c347f-205">Podporované platformy</span><span class="sxs-lookup"><span data-stu-id="c347f-205">Supported platforms</span></span>](xref:signalr/supported-platforms)
