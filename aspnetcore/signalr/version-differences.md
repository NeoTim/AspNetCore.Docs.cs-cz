---
title: Rozdíly mezi SignalR a ASP.NET CoreSignalR
author: bradygaster
description: Rozdíly mezi SignalR a ASP.NET CoreSignalR
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.date: 11/21/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/version-differences
ms.openlocfilehash: b5899f816dc5a5f8ff4c3f05c8e2c54ded5fc47b
ms.sourcegitcommit: a423e8fcde4b6181a3073ed646a603ba20bfa5f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2020
ms.locfileid: "84756038"
---
# <a name="differences-between-aspnet-signalr-and-aspnet-core-signalr"></a><span data-ttu-id="ae83d-103">Rozdíly mezi ASP.NET SignalR a ASP.NET CoreSignalR</span><span class="sxs-lookup"><span data-stu-id="ae83d-103">Differences between ASP.NET SignalR and ASP.NET Core SignalR</span></span>

<span data-ttu-id="ae83d-104">ASP.NET Core SignalR není kompatibilní s klienty nebo servery pro ASP.NET SignalR .</span><span class="sxs-lookup"><span data-stu-id="ae83d-104">ASP.NET Core SignalR isn't compatible with clients or servers for ASP.NET SignalR.</span></span> <span data-ttu-id="ae83d-105">Tento článek obsahuje podrobnosti o funkcích, které byly v ASP.NET Core odebrány nebo změněny SignalR .</span><span class="sxs-lookup"><span data-stu-id="ae83d-105">This article details features which have been removed or changed in ASP.NET Core SignalR.</span></span>

## <a name="how-to-identify-the-signalr-version"></a><span data-ttu-id="ae83d-106">Jak identifikovat SignalR verzi</span><span class="sxs-lookup"><span data-stu-id="ae83d-106">How to identify the SignalR version</span></span>

::: moniker range=">= aspnetcore-3.0"

|                      | <span data-ttu-id="ae83d-107">ASP.NETSignalR</span><span class="sxs-lookup"><span data-stu-id="ae83d-107">ASP.NET SignalR</span></span> | <span data-ttu-id="ae83d-108">ASP.NET CoreSignalR</span><span class="sxs-lookup"><span data-stu-id="ae83d-108">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="ae83d-109">Balíček NuGet serveru</span><span class="sxs-lookup"><span data-stu-id="ae83d-109">Server NuGet package</span></span> | <span data-ttu-id="ae83d-110">[Microsoft. AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="ae83d-110">[Microsoft.AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span></span> | <span data-ttu-id="ae83d-111">Žádné</span><span class="sxs-lookup"><span data-stu-id="ae83d-111">None.</span></span> <span data-ttu-id="ae83d-112">Je součástí sdílené architektury [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app) .</span><span class="sxs-lookup"><span data-stu-id="ae83d-112">Included in the [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) shared framework.</span></span> |
| <span data-ttu-id="ae83d-113">Klientské balíčky NuGet</span><span class="sxs-lookup"><span data-stu-id="ae83d-113">Client NuGet packages</span></span> | <span data-ttu-id="ae83d-114">[Microsoft. AspNet. SignalR . Služba](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="ae83d-114">[Microsoft.AspNet.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span></span><br><span data-ttu-id="ae83d-115">[Microsoft. AspNet. SignalR . JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span><span class="sxs-lookup"><span data-stu-id="ae83d-115">[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span></span> | <span data-ttu-id="ae83d-116">[Microsoft. AspNetCore. SignalR . Služba](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="ae83d-116">[Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span></span> |
| <span data-ttu-id="ae83d-117">JavaScript – balíček npm klienta</span><span class="sxs-lookup"><span data-stu-id="ae83d-117">JavaScript client npm package</span></span> | [<span data-ttu-id="ae83d-118">SignalR</span><span class="sxs-lookup"><span data-stu-id="ae83d-118">signalr</span></span>](https://www.npmjs.com/package/signalr) | [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) |
| <span data-ttu-id="ae83d-119">Klient Java</span><span class="sxs-lookup"><span data-stu-id="ae83d-119">Java client</span></span> | <span data-ttu-id="ae83d-120">[Úložiště GitHub](https://github.com/SignalR/java-client) (zastaralé)</span><span class="sxs-lookup"><span data-stu-id="ae83d-120">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="ae83d-121">Balíček Maven [com. Microsoft. Signal](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="ae83d-121">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="ae83d-122">Typ serverové aplikace</span><span class="sxs-lookup"><span data-stu-id="ae83d-122">Server app type</span></span> | <span data-ttu-id="ae83d-123">ASP.NET (System. Web) nebo OWIN – samoobslužné hostování</span><span class="sxs-lookup"><span data-stu-id="ae83d-123">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="ae83d-124">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ae83d-124">ASP.NET Core</span></span> |
| <span data-ttu-id="ae83d-125">Podporované serverové platformy</span><span class="sxs-lookup"><span data-stu-id="ae83d-125">Supported server platforms</span></span> | <span data-ttu-id="ae83d-126">.NET Framework 4,5 nebo novější</span><span class="sxs-lookup"><span data-stu-id="ae83d-126">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="ae83d-127">.NET Core 3,0 nebo novější</span><span class="sxs-lookup"><span data-stu-id="ae83d-127">.NET Core 3.0 or later</span></span> |

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

|                      | <span data-ttu-id="ae83d-128">ASP.NETSignalR</span><span class="sxs-lookup"><span data-stu-id="ae83d-128">ASP.NET SignalR</span></span> | <span data-ttu-id="ae83d-129">ASP.NET CoreSignalR</span><span class="sxs-lookup"><span data-stu-id="ae83d-129">ASP.NET Core SignalR</span></span> |
| -------------------- | --------------- | -------------------- |
| <span data-ttu-id="ae83d-130">Balíček NuGet serveru</span><span class="sxs-lookup"><span data-stu-id="ae83d-130">Server NuGet package</span></span> | <span data-ttu-id="ae83d-131">[Microsoft. AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="ae83d-131">[Microsoft.AspNet.SignalR](https://www.nuget.org/packages/Microsoft.AspNet.SignalR/)</span></span> | <span data-ttu-id="ae83d-132">[Microsoft. AspNetCore. app](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span><span class="sxs-lookup"><span data-stu-id="ae83d-132">[Microsoft.AspNetCore.App](https://www.nuget.org/packages/Microsoft.AspNetCore.App/) (.NET Core)</span></span><br><span data-ttu-id="ae83d-133">[Microsoft. AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span><span class="sxs-lookup"><span data-stu-id="ae83d-133">[Microsoft.AspNetCore.SignalR](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)</span></span> <span data-ttu-id="ae83d-134">(.NET Framework)</span><span class="sxs-lookup"><span data-stu-id="ae83d-134">(.NET Framework)</span></span> |
| <span data-ttu-id="ae83d-135">Klientské balíčky NuGet</span><span class="sxs-lookup"><span data-stu-id="ae83d-135">Client NuGet packages</span></span> | <span data-ttu-id="ae83d-136">[Microsoft. AspNet. SignalR . Služba](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="ae83d-136">[Microsoft.AspNet.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.Client/)</span></span><br><span data-ttu-id="ae83d-137">[Microsoft. AspNet. SignalR . JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span><span class="sxs-lookup"><span data-stu-id="ae83d-137">[Microsoft.AspNet.SignalR.JS](https://www.nuget.org/packages/Microsoft.AspNet.SignalR.JS/)</span></span> | <span data-ttu-id="ae83d-138">[Microsoft. AspNetCore. SignalR . Služba](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span><span class="sxs-lookup"><span data-stu-id="ae83d-138">[Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client/)</span></span> |
| <span data-ttu-id="ae83d-139">JavaScript – balíček npm klienta</span><span class="sxs-lookup"><span data-stu-id="ae83d-139">JavaScript client npm package</span></span> | [<span data-ttu-id="ae83d-140">SignalR</span><span class="sxs-lookup"><span data-stu-id="ae83d-140">signalr</span></span>](https://www.npmjs.com/package/signalr) | [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) |
| <span data-ttu-id="ae83d-141">Klient Java</span><span class="sxs-lookup"><span data-stu-id="ae83d-141">Java client</span></span> | <span data-ttu-id="ae83d-142">[Úložiště GitHub](https://github.com/SignalR/java-client) (zastaralé)</span><span class="sxs-lookup"><span data-stu-id="ae83d-142">[GitHub Repository](https://github.com/SignalR/java-client) (deprecated)</span></span>  | <span data-ttu-id="ae83d-143">Balíček Maven [com. Microsoft. Signal](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span><span class="sxs-lookup"><span data-stu-id="ae83d-143">Maven package [com.microsoft.signalr](https://search.maven.org/artifact/com.microsoft.signalr/signalr)</span></span> |
| <span data-ttu-id="ae83d-144">Typ serverové aplikace</span><span class="sxs-lookup"><span data-stu-id="ae83d-144">Server app type</span></span> | <span data-ttu-id="ae83d-145">ASP.NET (System. Web) nebo OWIN – samoobslužné hostování</span><span class="sxs-lookup"><span data-stu-id="ae83d-145">ASP.NET (System.Web) or OWIN Self-Host</span></span> | <span data-ttu-id="ae83d-146">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ae83d-146">ASP.NET Core</span></span> |
| <span data-ttu-id="ae83d-147">Podporované serverové platformy</span><span class="sxs-lookup"><span data-stu-id="ae83d-147">Supported server platforms</span></span> | <span data-ttu-id="ae83d-148">.NET Framework 4,5 nebo novější</span><span class="sxs-lookup"><span data-stu-id="ae83d-148">.NET Framework 4.5 or later</span></span> | <span data-ttu-id="ae83d-149">.NET Framework 4.6.1 nebo novější</span><span class="sxs-lookup"><span data-stu-id="ae83d-149">.NET Framework 4.6.1 or later</span></span><br><span data-ttu-id="ae83d-150">.NET Core 2,1 nebo novější</span><span class="sxs-lookup"><span data-stu-id="ae83d-150">.NET Core 2.1 or later</span></span> |

::: moniker-end

## <a name="feature-differences"></a><span data-ttu-id="ae83d-151">Rozdíly ve funkcích</span><span class="sxs-lookup"><span data-stu-id="ae83d-151">Feature differences</span></span>

### <a name="automatic-reconnects"></a><span data-ttu-id="ae83d-152">Automatické opětovné připojení</span><span class="sxs-lookup"><span data-stu-id="ae83d-152">Automatic reconnects</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ae83d-153">V ASP.NET SignalR :</span><span class="sxs-lookup"><span data-stu-id="ae83d-153">In ASP.NET SignalR:</span></span>

* <span data-ttu-id="ae83d-154">Ve výchozím nastavení se aplikace SignalR pokusí znovu připojit k serveru, pokud je připojení zrušeno.</span><span class="sxs-lookup"><span data-stu-id="ae83d-154">By default, SignalR attempts to reconnect to the server if the connection is dropped.</span></span> 

<span data-ttu-id="ae83d-155">V ASP.NET Core SignalR :</span><span class="sxs-lookup"><span data-stu-id="ae83d-155">In ASP.NET Core SignalR:</span></span>

* <span data-ttu-id="ae83d-156">Automatické opětovné připojení jsou výslovným souhlasem s [klientem rozhraní .NET](xref:signalr/dotnet-client#automatically-reconnect) i s [klientem JavaScriptu](xref:signalr/javascript-client#automatically-reconnect):</span><span class="sxs-lookup"><span data-stu-id="ae83d-156">Automatic reconnects are opt-in with both the [.NET client](xref:signalr/dotnet-client#automatically-reconnect) and the [JavaScript client](xref:signalr/javascript-client#automatically-reconnect):</span></span>

```csharp
HubConnection connection = new HubConnectionBuilder()
    .WithUrl(new Uri("http://127.0.0.1:5000/chathub"))
    .WithAutomaticReconnect()
    .Build();
```

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/chathub")
    .withAutomaticReconnect()
    .build();
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ae83d-157">Před ASP.NET Core 3,0 SignalR nepodporuje automatické opětovné připojení.</span><span class="sxs-lookup"><span data-stu-id="ae83d-157">Prior to ASP.NET Core 3.0, SignalR doesn't support automatic reconnects.</span></span> <span data-ttu-id="ae83d-158">Pokud je klient odpojený, uživatel musí explicitně spustit nové připojení, aby se znovu připojil.</span><span class="sxs-lookup"><span data-stu-id="ae83d-158">If the client is disconnected, the user must explicitly start a new connection to reconnect.</span></span> <span data-ttu-id="ae83d-159">V ASP.NET SignalR se SignalR pokusí znovu připojit k serveru, pokud je připojení vyřazené.</span><span class="sxs-lookup"><span data-stu-id="ae83d-159">In ASP.NET SignalR, SignalR attempts to reconnect to the server if the connection is dropped.</span></span>

::: moniker-end

### <a name="protocol-support"></a><span data-ttu-id="ae83d-160">Podpora protokolu</span><span class="sxs-lookup"><span data-stu-id="ae83d-160">Protocol support</span></span>

<span data-ttu-id="ae83d-161">ASP.NET Core SignalR podporuje JSON a také nový binární protokol založený na [MessagePack](xref:signalr/messagepackhubprotocol).</span><span class="sxs-lookup"><span data-stu-id="ae83d-161">ASP.NET Core SignalR supports JSON, as well as a new binary protocol based on [MessagePack](xref:signalr/messagepackhubprotocol).</span></span> <span data-ttu-id="ae83d-162">Navíc můžete vytvářet vlastní protokoly.</span><span class="sxs-lookup"><span data-stu-id="ae83d-162">Additionally, custom protocols can be created.</span></span>

### <a name="transports"></a><span data-ttu-id="ae83d-163">Přenosy</span><span class="sxs-lookup"><span data-stu-id="ae83d-163">Transports</span></span>

<span data-ttu-id="ae83d-164">Přenos snímků navždy není v ASP.NET Core podporován SignalR .</span><span class="sxs-lookup"><span data-stu-id="ae83d-164">The Forever Frame transport isn't supported in ASP.NET Core SignalR.</span></span>

## <a name="differences-on-the-server"></a><span data-ttu-id="ae83d-165">Rozdíly na serveru</span><span class="sxs-lookup"><span data-stu-id="ae83d-165">Differences on the server</span></span>

<span data-ttu-id="ae83d-166">ASP.NET Core SignalR knihovny na straně serveru jsou součástí [Microsoft. AspNetCore. app](xref:fundamentals/metapackage-app), který se používá v šabloně **webové aplikace ASP.NET Core** i pro Razor projekty MVC.</span><span class="sxs-lookup"><span data-stu-id="ae83d-166">The ASP.NET Core SignalR server-side libraries are included in [Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app), which is used in the **ASP.NET Core Web Application** template for both Razor and MVC projects.</span></span>

<span data-ttu-id="ae83d-167">ASP.NET Core SignalR je ASP.NET Core middleware.</span><span class="sxs-lookup"><span data-stu-id="ae83d-167">ASP.NET Core SignalR is an ASP.NET Core middleware.</span></span> <span data-ttu-id="ae83d-168">Musí být nakonfigurováno voláním <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> v `Startup.ConfigureServices` .</span><span class="sxs-lookup"><span data-stu-id="ae83d-168">It must be configured by calling <xref:Microsoft.Extensions.DependencyInjection.SignalRDependencyInjectionExtensions.AddSignalR%2A> in `Startup.ConfigureServices`.</span></span>

```csharp
services.AddSignalR()
```

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ae83d-169">Chcete-li konfigurovat směrování, namapujte trasy na rozbočovače uvnitř <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> volání metody v `Startup.Configure` metodě.</span><span class="sxs-lookup"><span data-stu-id="ae83d-169">To configure routing, map routes to hubs inside the <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseRouting();

app.UseEndpoints(endpoints =>
{
    endpoints.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="ae83d-170">Chcete-li konfigurovat směrování, namapujte trasy na rozbočovače uvnitř <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> volání metody v `Startup.Configure` metodě.</span><span class="sxs-lookup"><span data-stu-id="ae83d-170">To configure routing, map routes to hubs inside the <xref:Microsoft.AspNetCore.Builder.SignalRAppBuilderExtensions.UseSignalR%2A> method call in the `Startup.Configure` method.</span></span>

```csharp
app.UseSignalR(routes =>
{
    routes.MapHub<ChatHub>("/hub");
});
```

::: moniker-end

### <a name="sticky-sessions"></a><span data-ttu-id="ae83d-171">Rychlé relace</span><span class="sxs-lookup"><span data-stu-id="ae83d-171">Sticky sessions</span></span>

<span data-ttu-id="ae83d-172">Model horizontálního navýšení kapacity pro ASP.NET SignalR umožňuje klientům znovu se připojit a odesílat zprávy na libovolný server ve farmě.</span><span class="sxs-lookup"><span data-stu-id="ae83d-172">The scaleout model for ASP.NET SignalR allows clients to reconnect and send messages to any server in the farm.</span></span> <span data-ttu-id="ae83d-173">V ASP.NET Core SignalR musí klient spolupracovat se stejným serverem po dobu trvání připojení.</span><span class="sxs-lookup"><span data-stu-id="ae83d-173">In ASP.NET Core SignalR, the client must interact with the same server for the duration of the connection.</span></span> <span data-ttu-id="ae83d-174">Pro škálování pomocí Redis to znamená, že se vyžadují rychlé relace.</span><span class="sxs-lookup"><span data-stu-id="ae83d-174">For scaleout using Redis, that means sticky sessions are required.</span></span> <span data-ttu-id="ae83d-175">Pro škálování pomocí [ SignalR služby Azure](/azure/azure-signalr/)se nevyžadují rychlé relace, protože služba zpracovává připojení ke klientům.</span><span class="sxs-lookup"><span data-stu-id="ae83d-175">For scaleout using [Azure SignalR Service](/azure/azure-signalr/), sticky sessions aren't required because the service handles connections to clients.</span></span>

### <a name="single-hub-per-connection"></a><span data-ttu-id="ae83d-176">Jedno centrum na připojení</span><span class="sxs-lookup"><span data-stu-id="ae83d-176">Single hub per connection</span></span>

<span data-ttu-id="ae83d-177">V ASP.NET Core byl SignalR Model připojení zjednodušený.</span><span class="sxs-lookup"><span data-stu-id="ae83d-177">In ASP.NET Core SignalR, the connection model has been simplified.</span></span> <span data-ttu-id="ae83d-178">Připojení se vytvoří přímo v jednom rozbočovači, nikoli v jednom připojení, které se používá ke sdílení přístupu k více rozbočovačům.</span><span class="sxs-lookup"><span data-stu-id="ae83d-178">Connections are made directly to a single hub, rather than a single connection being used to share access to multiple hubs.</span></span>

### <a name="streaming"></a><span data-ttu-id="ae83d-179">Streamování</span><span class="sxs-lookup"><span data-stu-id="ae83d-179">Streaming</span></span>

<span data-ttu-id="ae83d-180">ASP.NET Core SignalR teď podporuje [streamování dat](xref:signalr/streaming) z rozbočovače na klienta.</span><span class="sxs-lookup"><span data-stu-id="ae83d-180">ASP.NET Core SignalR now supports [streaming data](xref:signalr/streaming) from the hub to the client.</span></span>

### <a name="state"></a><span data-ttu-id="ae83d-181">State</span><span class="sxs-lookup"><span data-stu-id="ae83d-181">State</span></span>

<span data-ttu-id="ae83d-182">Byla odebrána možnost předat libovolný stav mezi klienty a centrem (často označovaným jako `HubState` ) a také podporovat zprávy o průběhu.</span><span class="sxs-lookup"><span data-stu-id="ae83d-182">The ability to pass arbitrary state between clients and the hub (often called `HubState`) has been removed, as well as support for progress messages.</span></span> <span data-ttu-id="ae83d-183">V tuto chvíli není k dispozici žádný protějšek serverů proxy hub.</span><span class="sxs-lookup"><span data-stu-id="ae83d-183">There is no counterpart of hub proxies at the moment.</span></span>

### <a name="persistentconnection-removal"></a><span data-ttu-id="ae83d-184">Odebrání PersistentConnection</span><span class="sxs-lookup"><span data-stu-id="ae83d-184">PersistentConnection removal</span></span>

<span data-ttu-id="ae83d-185">V ASP.NET Core byla SignalR odebrána třída [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) .</span><span class="sxs-lookup"><span data-stu-id="ae83d-185">In ASP.NET Core SignalR, the [PersistentConnection](https://docs.microsoft.com/previous-versions/aspnet/jj919047(v%3dvs.118)) class has been removed.</span></span>

### <a name="globalhost"></a><span data-ttu-id="ae83d-186">GlobalHost</span><span class="sxs-lookup"><span data-stu-id="ae83d-186">GlobalHost</span></span>

<span data-ttu-id="ae83d-187">ASP.NET Core má vestavěnou vkládání závislostí (DI) do rozhraní.</span><span class="sxs-lookup"><span data-stu-id="ae83d-187">ASP.NET Core has dependency injection (DI) built into the framework.</span></span> <span data-ttu-id="ae83d-188">Služby můžou k přístupu k [HubContext](xref:signalr/hubcontext)používat di.</span><span class="sxs-lookup"><span data-stu-id="ae83d-188">Services can use DI to access the [HubContext](xref:signalr/hubcontext).</span></span> <span data-ttu-id="ae83d-189">`GlobalHost`Objekt, který se používá v ASP.NET SignalR k získání, `HubContext` neexistuje v ASP.NET Core SignalR .</span><span class="sxs-lookup"><span data-stu-id="ae83d-189">The `GlobalHost` object that is used in ASP.NET SignalR to get a `HubContext` doesn't exist in ASP.NET Core SignalR.</span></span>

### <a name="hubpipeline"></a><span data-ttu-id="ae83d-190">HubPipeline</span><span class="sxs-lookup"><span data-stu-id="ae83d-190">HubPipeline</span></span>

<span data-ttu-id="ae83d-191">ASP.NET Core nemá SignalR podporu pro `HubPipeline` moduly.</span><span class="sxs-lookup"><span data-stu-id="ae83d-191">ASP.NET Core SignalR doesn't have support for `HubPipeline` modules.</span></span>

## <a name="differences-on-the-client"></a><span data-ttu-id="ae83d-192">Rozdíly na klientovi</span><span class="sxs-lookup"><span data-stu-id="ae83d-192">Differences on the client</span></span>

### <a name="typescript"></a><span data-ttu-id="ae83d-193">TypeScript</span><span class="sxs-lookup"><span data-stu-id="ae83d-193">TypeScript</span></span>

<span data-ttu-id="ae83d-194">ASP.NET Core SignalR klient je napsán v [TypeScript](https://www.typescriptlang.org/).</span><span class="sxs-lookup"><span data-stu-id="ae83d-194">The ASP.NET Core SignalR client is written in [TypeScript](https://www.typescriptlang.org/).</span></span> <span data-ttu-id="ae83d-195">Při použití [klienta jazyka JavaScript](xref:signalr/javascript-client)můžete psát v JavaScriptu nebo TypeScript.</span><span class="sxs-lookup"><span data-stu-id="ae83d-195">You can write in JavaScript or TypeScript when using the [JavaScript client](xref:signalr/javascript-client).</span></span>

### <a name="the-javascript-client-is-hosted-at-npm"></a><span data-ttu-id="ae83d-196">JavaScriptový klient je hostovaný na npm</span><span class="sxs-lookup"><span data-stu-id="ae83d-196">The JavaScript client is hosted at npm</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ae83d-197">Ve verzích ASP.NET byl klient jazyka JavaScript získaný prostřednictvím balíčku NuGet v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ae83d-197">In ASP.NET versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="ae83d-198">V ASP.NET Core verzích [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) obsahuje balíček npm knihovny JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="ae83d-198">In the ASP.NET Core versions, the [`@microsoft/signalr`](https://www.npmjs.com/package/@microsoft/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="ae83d-199">Tento balíček není zahrnutý v šabloně **webové aplikace ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="ae83d-199">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="ae83d-200">Použijte npm k získání a instalaci `@microsoft/signalr` balíčku npm.</span><span class="sxs-lookup"><span data-stu-id="ae83d-200">Use npm to obtain and install the `@microsoft/signalr` npm package.</span></span>

```console
npm init -y
npm install @microsoft/signalr
```

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="ae83d-201">Ve verzích ASP.NET byl klient jazyka JavaScript získaný prostřednictvím balíčku NuGet v aplikaci Visual Studio.</span><span class="sxs-lookup"><span data-stu-id="ae83d-201">In ASP.NET versions, the JavaScript client was obtained through a NuGet package in Visual Studio.</span></span> <span data-ttu-id="ae83d-202">V ASP.NET Core verzích [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) obsahuje balíček npm knihovny JavaScriptu.</span><span class="sxs-lookup"><span data-stu-id="ae83d-202">In the ASP.NET Core versions, the [`@aspnet/signalr`](https://www.npmjs.com/package/@aspnet/signalr) npm package contains the JavaScript libraries.</span></span> <span data-ttu-id="ae83d-203">Tento balíček není zahrnutý v šabloně **webové aplikace ASP.NET Core** .</span><span class="sxs-lookup"><span data-stu-id="ae83d-203">This package isn't included in the **ASP.NET Core Web Application** template.</span></span> <span data-ttu-id="ae83d-204">Použijte npm k získání a instalaci `@aspnet/signalr` balíčku npm.</span><span class="sxs-lookup"><span data-stu-id="ae83d-204">Use npm to obtain and install the `@aspnet/signalr` npm package.</span></span>

```console
npm init -y
npm install @aspnet/signalr
```

::: moniker-end

### <a name="jquery"></a><span data-ttu-id="ae83d-205">jQuery</span><span class="sxs-lookup"><span data-stu-id="ae83d-205">jQuery</span></span>

<span data-ttu-id="ae83d-206">Závislost na jQuery se odebrala, ale projekty můžou pořád používat jQuery.</span><span class="sxs-lookup"><span data-stu-id="ae83d-206">The dependency on jQuery has been removed, however projects can still use jQuery.</span></span>

### <a name="internet-explorer-support"></a><span data-ttu-id="ae83d-207">Podpora aplikace Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="ae83d-207">Internet Explorer support</span></span>

<span data-ttu-id="ae83d-208">ASP.NET Core SignalR vyžaduje aplikaci Microsoft Internet Explorer 11 nebo novější (ASP.NET SignalR podporuje Microsoft Internet Explorer 8 a novější).</span><span class="sxs-lookup"><span data-stu-id="ae83d-208">ASP.NET Core SignalR requires Microsoft Internet Explorer 11 or later (ASP.NET SignalR supported Microsoft Internet Explorer 8 and later).</span></span>

### <a name="javascript-client-method-syntax"></a><span data-ttu-id="ae83d-209">Syntaxe metody klienta jazyka JavaScript</span><span class="sxs-lookup"><span data-stu-id="ae83d-209">JavaScript client method syntax</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ae83d-210">Syntaxe jazyka JavaScript se změnila z verze ASP.NET systému SignalR .</span><span class="sxs-lookup"><span data-stu-id="ae83d-210">The JavaScript syntax has changed from the ASP.NET version of SignalR.</span></span> <span data-ttu-id="ae83d-211">Místo použití `$connection` objektu vytvořte připojení pomocí rozhraní [HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) API.</span><span class="sxs-lookup"><span data-stu-id="ae83d-211">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/@aspnet/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="ae83d-212">Použijte metodu [on](/javascript/api/@microsoft/signalr/HubConnection#on) k určení klientských metod, které může centrum volat.</span><span class="sxs-lookup"><span data-stu-id="ae83d-212">Use the [on](/javascript/api/@microsoft/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="ae83d-213">Syntaxe jazyka JavaScript se změnila z verze ASP.NET systému SignalR .</span><span class="sxs-lookup"><span data-stu-id="ae83d-213">The JavaScript syntax has changed from the ASP.NET version of SignalR.</span></span> <span data-ttu-id="ae83d-214">Místo použití `$connection` objektu vytvořte připojení pomocí rozhraní [HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) API.</span><span class="sxs-lookup"><span data-stu-id="ae83d-214">Rather than using the `$connection` object, create a connection using the [HubConnectionBuilder](/javascript/api/@microsoft/signalr/hubconnectionbuilder) API.</span></span>

```javascript
const connection = new signalR.HubConnectionBuilder()
    .withUrl("/hub")
    .build();
```

<span data-ttu-id="ae83d-215">Použijte metodu [on](/javascript/api/@aspnet/signalr/HubConnection#on) k určení klientských metod, které může centrum volat.</span><span class="sxs-lookup"><span data-stu-id="ae83d-215">Use the [on](/javascript/api/@aspnet/signalr/HubConnection#on) method to specify client methods that the hub can call.</span></span>

::: moniker-end

```javascript
connection.on("ReceiveMessage", (user, message) => {
    const msg = message.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");
    const encodedMsg = `${user} says ${msg}`;
    console.log(encodedMsg);
});
```

<span data-ttu-id="ae83d-216">Po vytvoření metody klienta spusťte připojení k rozbočovači.</span><span class="sxs-lookup"><span data-stu-id="ae83d-216">After creating the client method, start the hub connection.</span></span> <span data-ttu-id="ae83d-217">Řetězení metody [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) pro protokolování a zpracování chyb.</span><span class="sxs-lookup"><span data-stu-id="ae83d-217">Chain a [catch](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch) method to log or handle errors.</span></span>

```javascript
connection.start().catch(err => console.error(err));
```

### <a name="hub-proxies"></a><span data-ttu-id="ae83d-218">Proxy servery centra</span><span class="sxs-lookup"><span data-stu-id="ae83d-218">Hub proxies</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ae83d-219">Proxy servery centra se už negenerují automaticky.</span><span class="sxs-lookup"><span data-stu-id="ae83d-219">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="ae83d-220">Místo toho je název metody předán do [vyvolání](/javascript/api/@microsoft/signalr/hubconnection#invoke) rozhraní API jako řetězec.</span><span class="sxs-lookup"><span data-stu-id="ae83d-220">Instead, the method name is passed into the [invoke](/javascript/api/@microsoft/signalr/hubconnection#invoke) API as a string.</span></span>

::: moniker-end

::: moniker range="<= aspnetcore-2.2"

<span data-ttu-id="ae83d-221">Proxy servery centra se už negenerují automaticky.</span><span class="sxs-lookup"><span data-stu-id="ae83d-221">Hub proxies are no longer automatically generated.</span></span> <span data-ttu-id="ae83d-222">Místo toho je název metody předán do [vyvolání](/javascript/api/@aspnet/signalr/hubconnection#invoke) rozhraní API jako řetězec.</span><span class="sxs-lookup"><span data-stu-id="ae83d-222">Instead, the method name is passed into the [invoke](/javascript/api/@aspnet/signalr/hubconnection#invoke) API as a string.</span></span>

::: moniker-end

### <a name="net-and-other-clients"></a><span data-ttu-id="ae83d-223">.NET a další klienti</span><span class="sxs-lookup"><span data-stu-id="ae83d-223">.NET and other clients</span></span>

<span data-ttu-id="ae83d-224">[Microsoft. AspNetCore. SignalR . ](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client)Balíček NuGet klienta obsahuje klientské knihovny .NET pro ASP.NET Core SignalR .</span><span class="sxs-lookup"><span data-stu-id="ae83d-224">The [Microsoft.AspNetCore.SignalR.Client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) NuGet package contains the .NET client libraries for ASP.NET Core SignalR.</span></span>

<span data-ttu-id="ae83d-225">Použijte <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> k vytvoření a sestavení instance připojení k rozbočovači.</span><span class="sxs-lookup"><span data-stu-id="ae83d-225">Use the <xref:Microsoft.AspNetCore.SignalR.Client.HubConnectionBuilder> to create and build an instance of a connection to a hub.</span></span>

```csharp
connection = new HubConnectionBuilder()
    .WithUrl("url")
    .Build();
```

## <a name="scaleout-differences"></a><span data-ttu-id="ae83d-226">Rozdíly ve škálování</span><span class="sxs-lookup"><span data-stu-id="ae83d-226">Scaleout differences</span></span>

<span data-ttu-id="ae83d-227">ASP.NET SignalR podporuje SQL Server a Redis.</span><span class="sxs-lookup"><span data-stu-id="ae83d-227">ASP.NET SignalR supports SQL Server and Redis.</span></span> <span data-ttu-id="ae83d-228">ASP.NET Core SignalR podporuje Azure SignalR Service a Redis.</span><span class="sxs-lookup"><span data-stu-id="ae83d-228">ASP.NET Core SignalR supports Azure SignalR Service and Redis.</span></span>

### <a name="aspnet"></a><span data-ttu-id="ae83d-229">ASP.NET</span><span class="sxs-lookup"><span data-stu-id="ae83d-229">ASP.NET</span></span>

* <span data-ttu-id="ae83d-230">[SignalRhorizontální navýšení kapacity pomocí Azure Service Bus](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)</span><span class="sxs-lookup"><span data-stu-id="ae83d-230">[SignalR scaleout with Azure Service Bus](/aspnet/signalr/overview/performance/scaleout-with-windows-azure-service-bus)</span></span>
* <span data-ttu-id="ae83d-231">[SignalRhorizontální navýšení kapacity pomocí Redis](/aspnet/signalr/overview/performance/scaleout-with-redis)</span><span class="sxs-lookup"><span data-stu-id="ae83d-231">[SignalR scaleout with Redis](/aspnet/signalr/overview/performance/scaleout-with-redis)</span></span>
* <span data-ttu-id="ae83d-232">[SignalRhorizontální navýšení kapacity pomocí SQL Server](/aspnet/signalr/overview/performance/scaleout-with-sql-server)</span><span class="sxs-lookup"><span data-stu-id="ae83d-232">[SignalR scaleout with SQL Server](/aspnet/signalr/overview/performance/scaleout-with-sql-server)</span></span>

### <a name="aspnet-core"></a><span data-ttu-id="ae83d-233">ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ae83d-233">ASP.NET Core</span></span>

* <span data-ttu-id="ae83d-234">[SignalRSlužba Azure](/azure/azure-signalr/)</span><span class="sxs-lookup"><span data-stu-id="ae83d-234">[Azure SignalR Service](/azure/azure-signalr/)</span></span>
* [<span data-ttu-id="ae83d-235">Redis</span><span class="sxs-lookup"><span data-stu-id="ae83d-235">Redis Backplane</span></span>](xref:signalr/redis-backplane)

## <a name="additional-resources"></a><span data-ttu-id="ae83d-236">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="ae83d-236">Additional resources</span></span>

* [<span data-ttu-id="ae83d-237">Centra</span><span class="sxs-lookup"><span data-stu-id="ae83d-237">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="ae83d-238">Klient JavaScriptu</span><span class="sxs-lookup"><span data-stu-id="ae83d-238">JavaScript client</span></span>](xref:signalr/javascript-client)
* [<span data-ttu-id="ae83d-239">Klient .NET</span><span class="sxs-lookup"><span data-stu-id="ae83d-239">.NET client</span></span>](xref:signalr/dotnet-client)
* [<span data-ttu-id="ae83d-240">Podporované platformy</span><span class="sxs-lookup"><span data-stu-id="ae83d-240">Supported platforms</span></span>](xref:signalr/supported-platforms)
