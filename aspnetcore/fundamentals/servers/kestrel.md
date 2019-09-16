---
title: Implementace webového serveru Kestrel v ASP.NET Core
author: guardrex
description: Přečtěte si o Kestrel, webovém serveru pro různé platformy pro ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 09/13/2019
uid: fundamentals/servers/kestrel
ms.openlocfilehash: b1c28f084e67d9cce74258433aa0c884878c2520
ms.sourcegitcommit: dc5b293e08336dc236de66ed1834f7ef78359531
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2019
ms.locfileid: "71011166"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="ee417-103">Implementace webového serveru Kestrel v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ee417-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="ee417-104">Tím, že [Dykstra](https://github.com/tdykstra), [Chris Rossův](https://github.com/Tratcher), [Stephen zastavili](https://twitter.com/halter73)a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="ee417-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), [Stephen Halter](https://twitter.com/halter73), and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ee417-105">Kestrel je [webový server pro různé platformy pro ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="ee417-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="ee417-106">Kestrel je webový server, který je ve výchozím nastavení součástí ASP.NET Core šablon projektů.</span><span class="sxs-lookup"><span data-stu-id="ee417-106">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="ee417-107">Kestrel podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="ee417-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="ee417-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="ee417-108">HTTPS</span></span>
* <span data-ttu-id="ee417-109">Neprůhledný upgrade používaný k povolení [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="ee417-109">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="ee417-110">Technologie UNIX Sockets s vysokým výkonem za Nginx</span><span class="sxs-lookup"><span data-stu-id="ee417-110">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="ee417-111">HTTP/2 (kromě macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="ee417-111">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="ee417-112">&dagger;HTTP/2 se v budoucí verzi podporuje v macOS.</span><span class="sxs-lookup"><span data-stu-id="ee417-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="ee417-113">Kestrel se podporuje na všech platformách a verzích, které podporuje .NET Core.</span><span class="sxs-lookup"><span data-stu-id="ee417-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="ee417-114">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ee417-114">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="ee417-115">Podpora HTTP/2</span><span class="sxs-lookup"><span data-stu-id="ee417-115">HTTP/2 support</span></span>

<span data-ttu-id="ee417-116">[Http/2](https://httpwg.org/specs/rfc7540.html) je k dispozici pro ASP.NET Core aplikace, pokud jsou splněné následující základní požadavky:</span><span class="sxs-lookup"><span data-stu-id="ee417-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="ee417-117">Operační systém&dagger;</span><span class="sxs-lookup"><span data-stu-id="ee417-117">Operating system&dagger;</span></span>
  * <span data-ttu-id="ee417-118">Windows Server 2016/Windows 10 nebo novější&Dagger;</span><span class="sxs-lookup"><span data-stu-id="ee417-118">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="ee417-119">Linux s OpenSSL 1.0.2 nebo novějším (například Ubuntu 16,04 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="ee417-119">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="ee417-120">Cílová architektura: .NET Core 2,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="ee417-120">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="ee417-121">Připojení [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="ee417-121">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="ee417-122">Protokol TLS 1.2 nebo vyšší připojení</span><span class="sxs-lookup"><span data-stu-id="ee417-122">TLS 1.2 or later connection</span></span>

<span data-ttu-id="ee417-123">&dagger;HTTP/2 se v budoucí verzi podporuje v macOS.</span><span class="sxs-lookup"><span data-stu-id="ee417-123">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="ee417-124">&Dagger;Kestrel má omezená podpora HTTP/2 na Windows Serveru 2012 R2 a Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="ee417-124">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="ee417-125">Podpora je omezená, protože seznam podporovaných šifrovacích sad TLS, které jsou k dispozici v těchto operačních systémech, je omezený.</span><span class="sxs-lookup"><span data-stu-id="ee417-125">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="ee417-126">Pro zabezpečení připojení TLS může být vyžadován certifikát vygenerovaný pomocí algoritmu ECDSA (s připojením typu eliptická křivka).</span><span class="sxs-lookup"><span data-stu-id="ee417-126">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="ee417-127">Pokud se připojení HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) sestavy `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="ee417-127">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="ee417-128">HTTP/2 je ve výchozím nastavení zakázané.</span><span class="sxs-lookup"><span data-stu-id="ee417-128">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="ee417-129">Další informace o konfiguraci najdete v částech [Možnosti Kestrel](#kestrel-options) a [ListenOptions. Protocols](#listenoptionsprotocols) .</span><span class="sxs-lookup"><span data-stu-id="ee417-129">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="ee417-130">Kdy použít Kestrel s reverzním proxy serverem</span><span class="sxs-lookup"><span data-stu-id="ee417-130">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="ee417-131">Kestrel může používat sám sebe nebo s *reverzními proxy server*, jako je [Internetová informační služba (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="ee417-131">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="ee417-132">Reverzní proxy server přijímá požadavky HTTP ze sítě a přesměruje je na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ee417-132">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="ee417-133">Kestrel použitý jako hraniční webový server (internetový):</span><span class="sxs-lookup"><span data-stu-id="ee417-133">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel komunikuje přímo s internetem bez zpětného proxy server](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="ee417-135">Kestrel se používá v konfiguraci reverzního proxy serveru:</span><span class="sxs-lookup"><span data-stu-id="ee417-135">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel nepřímo komunikuje s internetem prostřednictvím reverzní proxy server, jako je IIS, Nginx nebo Apache.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="ee417-137">Buď konfigurace, s zpětným proxy server nebo bez něj, je podporovaná konfigurace hostování.</span><span class="sxs-lookup"><span data-stu-id="ee417-137">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="ee417-138">Kestrel používané jako hraniční Server bez reverzních proxy server nepodporuje sdílení stejné IP adresy a portu mezi více procesy.</span><span class="sxs-lookup"><span data-stu-id="ee417-138">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="ee417-139">Když je Kestrel nakonfigurovaný k naslouchání na portu, Kestrel zpracovává veškerý provoz tohoto portu bez ohledu na `Host` hlavičky požadavků.</span><span class="sxs-lookup"><span data-stu-id="ee417-139">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="ee417-140">Reverzní proxy server, který může sdílet porty, má schopnost přesměrovat požadavky na Kestrel na jedinečné IP adresy a porty.</span><span class="sxs-lookup"><span data-stu-id="ee417-140">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="ee417-141">I v případě, že se zpětný proxy server nepožaduje, může být použití reverzní proxy server vhodnou volbou.</span><span class="sxs-lookup"><span data-stu-id="ee417-141">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="ee417-142">Reverzní proxy:</span><span class="sxs-lookup"><span data-stu-id="ee417-142">A reverse proxy:</span></span>

* <span data-ttu-id="ee417-143">Může omezit vystavenou veřejnou plochu v aplikacích, které hostuje.</span><span class="sxs-lookup"><span data-stu-id="ee417-143">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="ee417-144">Poskytněte další vrstvu konfigurace a obrany.</span><span class="sxs-lookup"><span data-stu-id="ee417-144">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="ee417-145">Může se lépe integrovat se stávající infrastrukturou.</span><span class="sxs-lookup"><span data-stu-id="ee417-145">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="ee417-146">Zjednodušte vyrovnávání zatížení a konfiguraci zabezpečené komunikace (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="ee417-146">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="ee417-147">Pouze reverzní proxy server vyžaduje certifikát X. 509 a tento server může komunikovat se servery aplikace v interní síti pomocí prostého protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="ee417-147">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="ee417-148">Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="ee417-148">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="ee417-149">Použití Kestrel v aplikacích ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ee417-149">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="ee417-150">ASP.NET Core šablony projektu ve výchozím nastavení používají Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ee417-150">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="ee417-151">V *program.cs*kód šablony volá <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>, který volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> na pozadí.</span><span class="sxs-lookup"><span data-stu-id="ee417-151">In *Program.cs*, the template code calls <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="ee417-152">Pro zajištění další konfigurace po volání `CreateDefaultBuilder` a `ConfigureWebHostDefaults`použijte `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="ee417-152">To provide additional configuration after calling `CreateDefaultBuilder` and `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(serverOptions =>
            {
                // Set properties and call methods on options
            })
            .UseStartup<Startup>();
        });
```

<span data-ttu-id="ee417-153">`CreateDefaultBuilder` Pokud aplikace nevolá pro nastavení hostitele, <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> zavolejte **před** voláním `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="ee417-153">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

```csharp
public static void Main(string[] args)
{
    var host = new HostBuilder()
        .UseContentRoot(Directory.GetCurrentDirectory())
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseKestrel(serverOptions =>
            {
                // Set properties and call methods on options
            })
            .UseIISIntegration()
            .UseStartup<Startup>();
        })
        .Build();

    host.Run();
}
```

## <a name="kestrel-options"></a><span data-ttu-id="ee417-154">Kestrel možnosti</span><span class="sxs-lookup"><span data-stu-id="ee417-154">Kestrel options</span></span>

<span data-ttu-id="ee417-155">Webový server Kestrel má možnosti konfigurace omezení, které jsou zvláště užitečné v internetových nasazeních.</span><span class="sxs-lookup"><span data-stu-id="ee417-155">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="ee417-156">Nastavte omezení pro <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> vlastnost <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> třídy.</span><span class="sxs-lookup"><span data-stu-id="ee417-156">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="ee417-157">Vlastnost obsahuje instanci <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>třídy. `Limits`</span><span class="sxs-lookup"><span data-stu-id="ee417-157">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="ee417-158">V následujících příkladech se <xref:Microsoft.AspNetCore.Server.Kestrel.Core> používá obor názvů:</span><span class="sxs-lookup"><span data-stu-id="ee417-158">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

### <a name="keep-alive-timeout"></a><span data-ttu-id="ee417-159">Časový limit Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="ee417-159">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="ee417-160">Získá nebo nastaví [časový limit zachování](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="ee417-160">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="ee417-161">Výchozí hodnota je 2 minuty.</span><span class="sxs-lookup"><span data-stu-id="ee417-161">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="ee417-162">Maximální počet připojení klientů</span><span class="sxs-lookup"><span data-stu-id="ee417-162">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="ee417-163">Maximální počet souběžných otevřených připojení TCP lze pro celou aplikaci nastavit pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="ee417-163">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="ee417-164">Existuje zvláštní omezení pro připojení, která byla upgradována z protokolu HTTP nebo HTTPS na jiný protokol (například na žádost WebSockets).</span><span class="sxs-lookup"><span data-stu-id="ee417-164">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="ee417-165">Po upgradu připojení se nepočítá na `MaxConcurrentConnections` základě limitu.</span><span class="sxs-lookup"><span data-stu-id="ee417-165">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="ee417-166">Ve výchozím nastavení je maximální počet připojení neomezené (null).</span><span class="sxs-lookup"><span data-stu-id="ee417-166">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="ee417-167">Maximální velikost textu požadavku</span><span class="sxs-lookup"><span data-stu-id="ee417-167">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="ee417-168">Výchozí maximální velikost textu požadavku je 30 000 000 bajtů, což je přibližně 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="ee417-168">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="ee417-169">Doporučený postup pro přepsání limitu v aplikaci ASP.NET Core MVC je použití <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> atributu pro metodu akce:</span><span class="sxs-lookup"><span data-stu-id="ee417-169">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="ee417-170">Tady je příklad, který ukazuje, jak nakonfigurovat omezení pro aplikaci na každém požadavku:</span><span class="sxs-lookup"><span data-stu-id="ee417-170">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="ee417-171">Přepsat nastavení u konkrétního požadavku v middleware:</span><span class="sxs-lookup"><span data-stu-id="ee417-171">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="ee417-172">Výjimka je vyvolána, pokud aplikace po zahájení aplikace nakonfiguruje limit na žádost.</span><span class="sxs-lookup"><span data-stu-id="ee417-172">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="ee417-173">Existuje vlastnost, která určuje, `MaxRequestBodySize` zda je vlastnost ve stavu jen pro čtení, což znamená, že je příliš pozdě pro konfiguraci limitu. `IsReadOnly`</span><span class="sxs-lookup"><span data-stu-id="ee417-173">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="ee417-174">Když je aplikace spouštěna [mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za [ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module), je omezení velikosti textu požadavku Kestrel zakázané, protože služba IIS tento limit již nastavuje.</span><span class="sxs-lookup"><span data-stu-id="ee417-174">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="ee417-175">Minimální rychlost těla zprávy požadavku</span><span class="sxs-lookup"><span data-stu-id="ee417-175">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="ee417-176">Kestrel kontroluje každou sekundu, pokud data přicházejí za zadanou rychlost v bajtech za sekundu.</span><span class="sxs-lookup"><span data-stu-id="ee417-176">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="ee417-177">Pokud frekvence klesne pod minimální hodnotu, vypršel časový limit připojení. Doba odkladu je doba, kterou Kestrel umožňuje klientovi zvýšit rychlost odesílání až na minimum; frekvence není během této doby kontrolována.</span><span class="sxs-lookup"><span data-stu-id="ee417-177">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="ee417-178">Doba odkladu pomáhá vyhnout se vyřazení připojení, která při počátečním posílání dat zpomalují rychlost, kvůli pomalému spuštění protokolu TCP.</span><span class="sxs-lookup"><span data-stu-id="ee417-178">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="ee417-179">Výchozí minimální sazba je 240 bajtů za sekundu s obdobím 5 sekund odkladu.</span><span class="sxs-lookup"><span data-stu-id="ee417-179">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="ee417-180">Pro odpověď platí také minimální sazba.</span><span class="sxs-lookup"><span data-stu-id="ee417-180">A minimum rate also applies to the response.</span></span> <span data-ttu-id="ee417-181">Kód pro nastavení limitu požadavku a limit odezvy je stejný, s výjimkou `RequestBody` , že existují nebo `Response` v názvech vlastností a rozhraní.</span><span class="sxs-lookup"><span data-stu-id="ee417-181">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="ee417-182">Tady je příklad, který ukazuje, jak nakonfigurovat minimální datové tarify v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="ee417-182">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="ee417-183">Přepsat minimální limity přenosové rychlosti na žádost v middlewaru:</span><span class="sxs-lookup"><span data-stu-id="ee417-183">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="ee417-184">Odkazovaná v předchozí ukázce není `HttpContext.Features` k dispozici pro požadavky HTTP/2, protože Změna omezení přenosové rychlosti u jednotlivých požadavků je obecně Nepodporovaná pro HTTP/2 kvůli podpoře protokolu pro multiplexování požadavků. <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature></span><span class="sxs-lookup"><span data-stu-id="ee417-184">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="ee417-185">`HttpContext.Features` `IHttpMinRequestBodyDataRateFeature.MinDataRate` `null`Propožadavky http/2 sealestálepoužívá,protožeomezeníčetnostičtenímůžebýtpořádzcelazakázanénazákladějednotlivýchpožadavkůnastavenímnaipropožadavekHTTP/2.<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature></span><span class="sxs-lookup"><span data-stu-id="ee417-185">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="ee417-186">Když se pokusíte o jeho navýšení `null` `NotSupportedException` nebopokusojehonastavenínajinouhodnotunež,vygenerujesemupožadavek`IHttpMinRequestBodyDataRateFeature.MinDataRate` http/2.</span><span class="sxs-lookup"><span data-stu-id="ee417-186">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="ee417-187">Omezení přenosové rychlosti pro všechny servery `KestrelServerOptions.Limits` jsou nakonfigurovaná přes protokol HTTP/1. x i pro připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="ee417-187">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="ee417-188">Časový limit hlaviček žádostí</span><span class="sxs-lookup"><span data-stu-id="ee417-188">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="ee417-189">Získá nebo nastaví maximální dobu, po kterou server stráví příjem hlaviček požadavků.</span><span class="sxs-lookup"><span data-stu-id="ee417-189">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="ee417-190">Výchozí hodnota je 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="ee417-190">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="ee417-191">Maximální počet proudů na připojení</span><span class="sxs-lookup"><span data-stu-id="ee417-191">Maximum streams per connection</span></span>

<span data-ttu-id="ee417-192">`Http2.MaxStreamsPerConnection`omezuje počet souběžných datových proudů požadavků na připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="ee417-192">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="ee417-193">Nadbytečné proudy jsou odmítnuty.</span><span class="sxs-lookup"><span data-stu-id="ee417-193">Excess streams are refused.</span></span>

```csharp
.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="ee417-194">Výchozí hodnota je 100.</span><span class="sxs-lookup"><span data-stu-id="ee417-194">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="ee417-195">Velikost tabulky hlaviček</span><span class="sxs-lookup"><span data-stu-id="ee417-195">Header table size</span></span>

<span data-ttu-id="ee417-196">Dekodér HPACK dekomprimuje hlavičky HTTP pro připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="ee417-196">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="ee417-197">`Http2.HeaderTableSize`omezuje velikost kompresní tabulky hlaviček, kterou používá dekodér HPACK.</span><span class="sxs-lookup"><span data-stu-id="ee417-197">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="ee417-198">Hodnota je uvedena v oktetech a musí být větší než nula (0).</span><span class="sxs-lookup"><span data-stu-id="ee417-198">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="ee417-199">Výchozí hodnota je 4096.</span><span class="sxs-lookup"><span data-stu-id="ee417-199">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="ee417-200">Maximální velikost rámce</span><span class="sxs-lookup"><span data-stu-id="ee417-200">Maximum frame size</span></span>

<span data-ttu-id="ee417-201">`Http2.MaxFrameSize`Určuje maximální povolenou velikost datové části rámce připojení HTTP/2 přijatého nebo odesílaného serverem.</span><span class="sxs-lookup"><span data-stu-id="ee417-201">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="ee417-202">Hodnota je uvedena v oktetech a musí být v rozmezí 2 ^ 14 (16 384) až 2 ^ 24-1 (16 777 215).</span><span class="sxs-lookup"><span data-stu-id="ee417-202">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="ee417-203">Výchozí hodnota je 2 ^ 14 (16 384).</span><span class="sxs-lookup"><span data-stu-id="ee417-203">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="ee417-204">Maximální velikost hlavičky požadavku</span><span class="sxs-lookup"><span data-stu-id="ee417-204">Maximum request header size</span></span>

<span data-ttu-id="ee417-205">`Http2.MaxRequestHeaderFieldSize`Určuje maximální povolenou velikost v oktetech hodnot hlaviček požadavku.</span><span class="sxs-lookup"><span data-stu-id="ee417-205">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="ee417-206">Toto omezení se vztahuje na název i hodnotu v jejich komprimovaných a nekomprimovaných reprezentujcích.</span><span class="sxs-lookup"><span data-stu-id="ee417-206">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="ee417-207">Hodnota musí být větší než nula (0).</span><span class="sxs-lookup"><span data-stu-id="ee417-207">The value must be greater than zero (0).</span></span>

```csharp
.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="ee417-208">Výchozí hodnota je 8 192.</span><span class="sxs-lookup"><span data-stu-id="ee417-208">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="ee417-209">Velikost okna počátečního připojení</span><span class="sxs-lookup"><span data-stu-id="ee417-209">Initial connection window size</span></span>

<span data-ttu-id="ee417-210">`Http2.InitialConnectionWindowSize`Určuje maximální velikost textu požadavku v bajtech, které jsou v jednom okamžiku agregovány v rámci všech požadavků (datových proudů) na připojení.</span><span class="sxs-lookup"><span data-stu-id="ee417-210">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="ee417-211">Žádosti jsou také omezené pomocí `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="ee417-211">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="ee417-212">Hodnota musí být větší než nebo rovna 65 535 a menší než 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="ee417-212">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="ee417-213">Výchozí hodnota je 128 KB (131 072).</span><span class="sxs-lookup"><span data-stu-id="ee417-213">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="ee417-214">Počáteční velikost okna datového proudu</span><span class="sxs-lookup"><span data-stu-id="ee417-214">Initial stream window size</span></span>

<span data-ttu-id="ee417-215">`Http2.InitialStreamWindowSize`Určuje maximální velikost textu požadavku v bajtech na jednu žádost (Stream) na jednom místě.</span><span class="sxs-lookup"><span data-stu-id="ee417-215">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="ee417-216">Žádosti jsou také omezené pomocí `Http2.InitialConnectionWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="ee417-216">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="ee417-217">Hodnota musí být větší než nebo rovna 65 535 a menší než 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="ee417-217">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="ee417-218">Výchozí hodnota je 96 KB (98 304).</span><span class="sxs-lookup"><span data-stu-id="ee417-218">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="ee417-219">Synchronní v/v</span><span class="sxs-lookup"><span data-stu-id="ee417-219">Synchronous IO</span></span>

<span data-ttu-id="ee417-220"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>Určuje, zda je pro požadavek a odpověď povolena synchronní v/v.</span><span class="sxs-lookup"><span data-stu-id="ee417-220"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="ee417-221">Výchozí hodnota je `false`.</span><span class="sxs-lookup"><span data-stu-id="ee417-221">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="ee417-222">Velký počet blokování synchronních vstupně-výstupních operací může vést ke vyčerpání fondu vláken, což aplikaci nereaguje.</span><span class="sxs-lookup"><span data-stu-id="ee417-222">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="ee417-223">Povolit `AllowSynchronousIO` pouze při použití knihovny, která nepodporuje asynchronní v/v.</span><span class="sxs-lookup"><span data-stu-id="ee417-223">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="ee417-224">Následující příklad povoluje synchronní vstupně-výstupní operace:</span><span class="sxs-lookup"><span data-stu-id="ee417-224">The following example enables synchronous IO:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="ee417-225">Informace o dalších možnostech a omezeních Kestrel najdete v tématech:</span><span class="sxs-lookup"><span data-stu-id="ee417-225">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="ee417-226">Konfigurace koncového bodu</span><span class="sxs-lookup"><span data-stu-id="ee417-226">Endpoint configuration</span></span>

<span data-ttu-id="ee417-227">Ve výchozím nastavení ASP.NET Core váže k:</span><span class="sxs-lookup"><span data-stu-id="ee417-227">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="ee417-228">`https://localhost:5001`(Pokud je k dispozici místní vývojový certifikát)</span><span class="sxs-lookup"><span data-stu-id="ee417-228">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="ee417-229">Zadejte adresy URL pomocí:</span><span class="sxs-lookup"><span data-stu-id="ee417-229">Specify URLs using the:</span></span>

* <span data-ttu-id="ee417-230">`ASPNETCORE_URLS` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="ee417-230">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="ee417-231">`--urls`argument příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="ee417-231">`--urls` command-line argument.</span></span>
* <span data-ttu-id="ee417-232">`urls`konfigurační klíč hostitele.</span><span class="sxs-lookup"><span data-stu-id="ee417-232">`urls` host configuration key.</span></span>
* <span data-ttu-id="ee417-233">`UseUrls`Metoda rozšíření</span><span class="sxs-lookup"><span data-stu-id="ee417-233">`UseUrls` extension method.</span></span>

<span data-ttu-id="ee417-234">Hodnota zadaná pomocí těchto přístupů může být jeden nebo více koncových bodů HTTP a HTTPS (HTTPS, pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="ee417-234">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="ee417-235">Nakonfigurujte hodnotu jako seznam oddělený středníkem (například `"Urls": "http://localhost:8000; http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="ee417-235">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="ee417-236">Další informace o těchto přístupůch najdete v tématu [adresy URL serveru](xref:fundamentals/host/web-host#server-urls) a [potlačení konfigurace](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="ee417-236">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="ee417-237">Vytvoří se vývojový certifikát:</span><span class="sxs-lookup"><span data-stu-id="ee417-237">A development certificate is created:</span></span>

* <span data-ttu-id="ee417-238">Po instalaci [.NET Core SDK](/dotnet/core/sdk) .</span><span class="sxs-lookup"><span data-stu-id="ee417-238">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="ee417-239">[Nástroj pro vývoj a certifikáty](xref:aspnetcore-2.1#https) slouží k vytvoření certifikátu.</span><span class="sxs-lookup"><span data-stu-id="ee417-239">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="ee417-240">Některé prohlížeče vyžadují udělení explicitního oprávnění pro důvěřování místního vývojového certifikátu.</span><span class="sxs-lookup"><span data-stu-id="ee417-240">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="ee417-241">Šablony projektů konfigurují aplikace tak, aby běžely ve výchozím nastavení HTTPS a zahrnovaly [přesměrování https a podporu HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="ee417-241">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="ee417-242">Volání <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> nebo <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> metody<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> pro konfiguraci předpon adres URL a portů pro Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ee417-242">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="ee417-243">`UseUrls`, argument `urls` `ASPNETCORE_URLS` příkazového řádku, konfigurační klíč hostitele a proměnná prostředí fungují také, ale mají omezení zaznamenaná později v této části (pro koncový bod HTTPS musí být dostupný výchozí certifikát.) `--urls` konfigurace).</span><span class="sxs-lookup"><span data-stu-id="ee417-243">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="ee417-244">`KestrelServerOptions`rozšířeného</span><span class="sxs-lookup"><span data-stu-id="ee417-244">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="ee417-245">ConfigureEndpointDefaults (akce\<ListenOptions >)</span><span class="sxs-lookup"><span data-stu-id="ee417-245">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="ee417-246">Určuje konfiguraci `Action` , která se spustí pro každý zadaný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="ee417-246">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="ee417-247">Volání `ConfigureEndpointDefaults` několikrát nahrazuje předchozí `Action` `Action` zadanou parametr s.</span><span class="sxs-lookup"><span data-stu-id="ee417-247">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(serverOptions =>
            {
                serverOptions.ConfigureEndpointDefaults(listenOptions =>
                {
                    // Configure endpoint defaults
                });
            })
            .UseStartup<Startup>();
        });
}
```

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="ee417-248">ConfigureHttpsDefaults (akce\<HttpsConnectionAdapterOptions >)</span><span class="sxs-lookup"><span data-stu-id="ee417-248">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="ee417-249">Určuje konfiguraci `Action` , která se má spustit pro každý koncový bod HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ee417-249">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="ee417-250">Volání `ConfigureHttpsDefaults` několikrát nahrazuje předchozí `Action` `Action` zadanou parametr s.</span><span class="sxs-lookup"><span data-stu-id="ee417-250">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(serverOptions =>
            {
                serverOptions.ConfigureHttpsDefaults(listenOptions =>
                {
                    // certificate is an X509Certificate2
                    listenOptions.ServerCertificate = certificate;
                });
            })
            .UseStartup<Startup>();
        });
}
```

### <a name="configureiconfiguration"></a><span data-ttu-id="ee417-251">Konfigurace (IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="ee417-251">Configure(IConfiguration)</span></span>

<span data-ttu-id="ee417-252">Vytvoří zavaděč konfigurace pro nastavení Kestrel, který přebírá <xref:Microsoft.Extensions.Configuration.IConfiguration> jako vstup.</span><span class="sxs-lookup"><span data-stu-id="ee417-252">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="ee417-253">Konfigurace musí být vymezená na konfigurační oddíl pro Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ee417-253">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="ee417-254">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="ee417-254">ListenOptions.UseHttps</span></span>

<span data-ttu-id="ee417-255">Nakonfigurujte Kestrel tak, aby používal protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ee417-255">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="ee417-256">`ListenOptions.UseHttps`SND</span><span class="sxs-lookup"><span data-stu-id="ee417-256">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="ee417-257">`UseHttps`&ndash; Nakonfigurujte Kestrel tak, aby používal HTTPS s výchozím certifikátem.</span><span class="sxs-lookup"><span data-stu-id="ee417-257">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="ee417-258">Vyvolá výjimku, pokud není nakonfigurován žádný výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="ee417-258">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="ee417-259">`ListenOptions.UseHttps`ukazatelů</span><span class="sxs-lookup"><span data-stu-id="ee417-259">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="ee417-260">`filename`je cesta a název souboru certifikátu vzhledem k adresáři, který obsahuje soubory obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="ee417-260">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="ee417-261">`password`je vyžadováno heslo pro přístup k datům certifikátu X. 509.</span><span class="sxs-lookup"><span data-stu-id="ee417-261">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="ee417-262">`configureOptions``Action` je ke`HttpsConnectionAdapterOptions`konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="ee417-262">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="ee417-263">`ListenOptions`Vrátí.</span><span class="sxs-lookup"><span data-stu-id="ee417-263">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="ee417-264">`storeName`je úložiště certifikátů, ze kterého se má certifikát načíst.</span><span class="sxs-lookup"><span data-stu-id="ee417-264">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="ee417-265">`subject`je název subjektu certifikátu.</span><span class="sxs-lookup"><span data-stu-id="ee417-265">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="ee417-266">`allowInvalid`Určuje, zda by měly být zváženy neplatné certifikáty, například certifikáty podepsané svým držitelem.</span><span class="sxs-lookup"><span data-stu-id="ee417-266">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="ee417-267">`location`je umístěním úložiště, ze kterého se má certifikát načíst.</span><span class="sxs-lookup"><span data-stu-id="ee417-267">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="ee417-268">`serverCertificate`je certifikát X. 509.</span><span class="sxs-lookup"><span data-stu-id="ee417-268">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="ee417-269">V produkčním prostředí musí být HTTPS explicitně nakonfigurovaný.</span><span class="sxs-lookup"><span data-stu-id="ee417-269">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="ee417-270">Musí být zadán minimálně výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="ee417-270">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="ee417-271">Podporované konfigurace popsané dál:</span><span class="sxs-lookup"><span data-stu-id="ee417-271">Supported configurations described next:</span></span>

* <span data-ttu-id="ee417-272">Žádná konfigurace</span><span class="sxs-lookup"><span data-stu-id="ee417-272">No configuration</span></span>
* <span data-ttu-id="ee417-273">Nahradit výchozí certifikát z konfigurace</span><span class="sxs-lookup"><span data-stu-id="ee417-273">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="ee417-274">Změna výchozích hodnot v kódu</span><span class="sxs-lookup"><span data-stu-id="ee417-274">Change the defaults in code</span></span>

<span data-ttu-id="ee417-275">*Žádná konfigurace*</span><span class="sxs-lookup"><span data-stu-id="ee417-275">*No configuration*</span></span>

<span data-ttu-id="ee417-276">Kestrel naslouchá na `http://localhost:5000` a `https://localhost:5001` (Pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="ee417-276">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="ee417-277">*Nahradit výchozí certifikát z konfigurace*</span><span class="sxs-lookup"><span data-stu-id="ee417-277">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="ee417-278">`CreateDefaultBuilder`Při `Configure(context.Configuration.GetSection("Kestrel"))` načítání konfigurace Kestrel ve výchozím nastavení volá.</span><span class="sxs-lookup"><span data-stu-id="ee417-278">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="ee417-279">Pro Kestrel je k dispozici výchozí schéma konfigurace nastavení aplikace HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ee417-279">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="ee417-280">Nakonfigurujte několik koncových bodů, včetně adres URL a certifikátů, které se mají použít, buď ze souboru na disku, nebo z úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="ee417-280">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="ee417-281">V následujícím příkladu *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="ee417-281">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="ee417-282">Nastavte **AllowInvalid** na `true` , aby povolovala použití neplatných certifikátů (například certifikátů podepsaných svým držitelem).</span><span class="sxs-lookup"><span data-stu-id="ee417-282">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="ee417-283">Libovolný koncový bod HTTPS, který neurčuje certifikát (**HttpsDefaultCert** v následujícím příkladu), se vrátí k certifikátu definovanému v části **certifikáty** > **výchozí** nebo vývojový certifikát.</span><span class="sxs-lookup"><span data-stu-id="ee417-283">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="ee417-284">Alternativou k použití **cesty** a **hesla** pro libovolný uzel certifikátu je zadání certifikátu pomocí polí úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="ee417-284">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="ee417-285">Například > **výchozí** certifikát může být zadán jako:</span><span class="sxs-lookup"><span data-stu-id="ee417-285">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="ee417-286">Poznámky ke schématu:</span><span class="sxs-lookup"><span data-stu-id="ee417-286">Schema notes:</span></span>

* <span data-ttu-id="ee417-287">V názvech koncových bodů se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="ee417-287">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="ee417-288">Například `HTTPS` a`Https` jsou platné.</span><span class="sxs-lookup"><span data-stu-id="ee417-288">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="ee417-289">U každého koncového bodu je vyžadován parametr.`Url`</span><span class="sxs-lookup"><span data-stu-id="ee417-289">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="ee417-290">Formát pro tento parametr je stejný jako konfigurační parametr nejvyšší úrovně `Urls` s tím rozdílem, že je omezen na jedinou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="ee417-290">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="ee417-291">Tyto koncové body nahrazují hodnoty definované v konfiguraci nejvyšší úrovně `Urls` místo jejich přidání do těchto koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="ee417-291">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="ee417-292">Koncové body definované v kódu `Listen` prostřednictvím jsou kumulativní pomocí koncových bodů definovaných v konfiguračním oddílu.</span><span class="sxs-lookup"><span data-stu-id="ee417-292">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="ee417-293">`Certificate` Oddíl je nepovinný.</span><span class="sxs-lookup"><span data-stu-id="ee417-293">The `Certificate` section is optional.</span></span> <span data-ttu-id="ee417-294">Pokud není `Certificate` oddíl zadaný, použijí se výchozí hodnoty definované v dřívějších scénářích.</span><span class="sxs-lookup"><span data-stu-id="ee417-294">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="ee417-295">Pokud nejsou k dispozici žádné výchozí hodnoty, Server vyvolá výjimku a nemůže se spustit.</span><span class="sxs-lookup"><span data-stu-id="ee417-295">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="ee417-296">&ndash;&ndash; Oddíl podporuje jak heslo cesty, tak certifikáty úložiště subjektu. `Certificate`</span><span class="sxs-lookup"><span data-stu-id="ee417-296">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="ee417-297">V takovém případě může být definován libovolný počet koncových bodů, pokud to nezpůsobí konflikty portů.</span><span class="sxs-lookup"><span data-stu-id="ee417-297">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="ee417-298">`options.Configure(context.Configuration.GetSection("{SECTION}"))``KestrelConfigurationLoader` vrátí metodu`.Endpoint(string name, listenOptions => { })` s metodou, kterou lze použít k doplnění nastavení nakonfigurovaného koncového bodu:</span><span class="sxs-lookup"><span data-stu-id="ee417-298">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.UseKestrel((context, serverOptions) =>
            {
                serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                    .Endpoint("HTTPS", listenOptions =>
                    {
                        listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                    });
            });
        });
```

<span data-ttu-id="ee417-299">`KestrelServerOptions.ConfigurationLoader`k pokračování v iteraci u stávajícího zavaděče se dá získat přímý odkaz, jako je třeba <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>ten, který poskytuje.</span><span class="sxs-lookup"><span data-stu-id="ee417-299">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="ee417-300">Konfigurační oddíl pro každý koncový bod je k dispozici na možnostech v `Endpoint` metodě, aby bylo možné číst vlastní nastavení.</span><span class="sxs-lookup"><span data-stu-id="ee417-300">The configuration section for each endpoint is a available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="ee417-301">Více konfigurací může být načteno voláním `options.Configure(context.Configuration.GetSection("{SECTION}"))` znovu s jiným oddílem.</span><span class="sxs-lookup"><span data-stu-id="ee417-301">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="ee417-302">Je použita pouze poslední konfigurace, pokud `Load` není explicitně volána při předchozích instancích.</span><span class="sxs-lookup"><span data-stu-id="ee417-302">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="ee417-303">Metapackage nevolá `Load` , aby se jeho výchozí konfigurační oddíl mohl nahradit.</span><span class="sxs-lookup"><span data-stu-id="ee417-303">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="ee417-304">`KestrelConfigurationLoader`zrcadlí `KestrelServerOptions` `Endpoint` rodinu rozhraní API z as přetížení, takže koncové body kódu a konfigurace lze konfigurovat na stejném místě. `Listen`</span><span class="sxs-lookup"><span data-stu-id="ee417-304">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="ee417-305">Tato přetížení nepoužívají názvy a využívají pouze výchozí nastavení z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="ee417-305">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="ee417-306">*Změna výchozích hodnot v kódu*</span><span class="sxs-lookup"><span data-stu-id="ee417-306">*Change the defaults in code*</span></span>

<span data-ttu-id="ee417-307">`ConfigureEndpointDefaults`a `ConfigureHttpsDefaults` dá se použít ke změně výchozího nastavení pro `ListenOptions` a `HttpsConnectionAdapterOptions`, včetně přepsání výchozího certifikátu zadaného v předchozím scénáři.</span><span class="sxs-lookup"><span data-stu-id="ee417-307">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="ee417-308">`ConfigureEndpointDefaults`a `ConfigureHttpsDefaults` měla by být volána před konfigurací koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="ee417-308">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(serverOptions =>
            {
                serverOptions.ConfigureEndpointDefaults(listenOptions =>
                {
                    // Configure endpoint defaults
                });

                serverOptions.ConfigureHttpsDefaults(listenOptions =>
                {
                    listenOptions.SslProtocols = SslProtocols.Tls12;
                });
            });
        });
```

<span data-ttu-id="ee417-309">*Podpora Kestrel pro SNI*</span><span class="sxs-lookup"><span data-stu-id="ee417-309">*Kestrel support for SNI*</span></span>

<span data-ttu-id="ee417-310">[Indikace názvu serveru (SNI)](https://tools.ietf.org/html/rfc6066#section-3) lze použít k hostování více domén na stejné IP adrese a portu.</span><span class="sxs-lookup"><span data-stu-id="ee417-310">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="ee417-311">Aby SNI fungovalo, klient pošle název hostitele pro zabezpečenou relaci serveru během metody handshake TLS, aby server mohl poskytovat správný certifikát.</span><span class="sxs-lookup"><span data-stu-id="ee417-311">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="ee417-312">Klient používá poskytnutý certifikát pro šifrovanou komunikaci se serverem během zabezpečené relace, která následuje za metodou handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="ee417-312">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="ee417-313">Kestrel podporuje sni prostřednictvím `ServerCertificateSelector` zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="ee417-313">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="ee417-314">Zpětné volání je vyvoláno jednou za připojení, které aplikaci umožní zkontrolovat název hostitele a vybrat příslušný certifikát.</span><span class="sxs-lookup"><span data-stu-id="ee417-314">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="ee417-315">Podpora SNI vyžaduje:</span><span class="sxs-lookup"><span data-stu-id="ee417-315">SNI support requires:</span></span>

* <span data-ttu-id="ee417-316">Běží v cílovém rozhraní `netcoreapp2.1` nebo novějším.</span><span class="sxs-lookup"><span data-stu-id="ee417-316">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="ee417-317">V `net461` systému nebo novějším je zpětné volání vyvoláno, `null` `name` ale je vždy.</span><span class="sxs-lookup"><span data-stu-id="ee417-317">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="ee417-318">`name` Je také`null` v případě, že klient v TLS handshake neposkytne parametr názvu hostitele.</span><span class="sxs-lookup"><span data-stu-id="ee417-318">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="ee417-319">Všechny weby běží na stejné instanci Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ee417-319">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="ee417-320">Kestrel nepodporuje sdílení IP adresy a portu napříč několika instancemi bez reverzního proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="ee417-320">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(serverOptions =>
            {
                serverOptions.ListenAnyIP(5005, listenOptions =>
                {
                    listenOptions.UseHttps(httpsOptions =>
                    {
                        var localhostCert = CertificateLoader.LoadFromStoreCert(
                            "localhost", "My", StoreLocation.CurrentUser,
                            allowInvalid: true);
                        var exampleCert = CertificateLoader.LoadFromStoreCert(
                            "example.com", "My", StoreLocation.CurrentUser,
                            allowInvalid: true);
                        var subExampleCert = CertificateLoader.LoadFromStoreCert(
                            "sub.example.com", "My", StoreLocation.CurrentUser,
                            allowInvalid: true);
                        var certs = new Dictionary<string, X509Certificate2>(
                            StringComparer.OrdinalIgnoreCase);
                        certs["localhost"] = localhostCert;
                        certs["example.com"] = exampleCert;
                        certs["sub.example.com"] = subExampleCert;
    
                        httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                        {
                            if (name != null && certs.TryGetValue(name, out var cert))
                            {
                                return cert;
                            }
    
                            return exampleCert;
                        };
                    });
                });
            })
            .UseStartup<Startup>();
        });
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="ee417-321">Vytvoření vazby na soket TCP</span><span class="sxs-lookup"><span data-stu-id="ee417-321">Bind to a TCP socket</span></span>

<span data-ttu-id="ee417-322"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> Metoda se váže k soketu TCP a pomocí lambda možností povolíte konfiguraci certifikátu X. 509:</span><span class="sxs-lookup"><span data-stu-id="ee417-322">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="ee417-323">Příklad nakonfiguruje HTTPS pro koncový bod pomocí <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="ee417-323">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="ee417-324">Ke konfiguraci dalších nastavení Kestrel pro konkrétní koncové body použijte stejné rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="ee417-324">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="ee417-325">Vytvoření vazby na soket UNIX</span><span class="sxs-lookup"><span data-stu-id="ee417-325">Bind to a Unix socket</span></span>

<span data-ttu-id="ee417-326">Naslouchat na soketu <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> se systémem UNIX, aby se zlepšil výkon pomocí Nginx, jak je znázorněno v tomto příkladu:</span><span class="sxs-lookup"><span data-stu-id="ee417-326">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

### <a name="port-0"></a><span data-ttu-id="ee417-327">Port 0</span><span class="sxs-lookup"><span data-stu-id="ee417-327">Port 0</span></span>

<span data-ttu-id="ee417-328">Po zadání čísla `0` portu se Kestrel dynamicky váže k dostupnému portu.</span><span class="sxs-lookup"><span data-stu-id="ee417-328">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="ee417-329">Následující příklad ukazuje, jak určit, který port Kestrel je skutečně vázaný za běhu:</span><span class="sxs-lookup"><span data-stu-id="ee417-329">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="ee417-330">Když se aplikace spustí, výstup okna konzoly indikuje dynamický port, na který se aplikace dá získat:</span><span class="sxs-lookup"><span data-stu-id="ee417-330">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="ee417-331">Omezení</span><span class="sxs-lookup"><span data-stu-id="ee417-331">Limitations</span></span>

<span data-ttu-id="ee417-332">Konfigurace koncových bodů pomocí následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="ee417-332">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="ee417-333">`--urls`argument příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="ee417-333">`--urls` command-line argument</span></span>
* <span data-ttu-id="ee417-334">`urls`konfigurační klíč hostitele</span><span class="sxs-lookup"><span data-stu-id="ee417-334">`urls` host configuration key</span></span>
* <span data-ttu-id="ee417-335">`ASPNETCORE_URLS`Proměnná prostředí</span><span class="sxs-lookup"><span data-stu-id="ee417-335">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="ee417-336">Tyto metody jsou užitečné, pokud chcete, aby kód pracoval na jiných serverech než Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ee417-336">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="ee417-337">Mějte ale na paměti následující omezení:</span><span class="sxs-lookup"><span data-stu-id="ee417-337">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="ee417-338">Protokol HTTPS nelze s těmito přístupy použít, pokud není k dispozici výchozí certifikát v konfiguraci koncového bodu HTTPS (například `KestrelServerOptions` pomocí konfigurace nebo konfiguračního souboru, jak je uvedeno výše v tomto tématu).</span><span class="sxs-lookup"><span data-stu-id="ee417-338">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="ee417-339">Při současném `Listen` `UseUrls` použitíobou`UseUrls` přístupů a koncovýchbodůsekoncovébodypřepisují.`Listen`</span><span class="sxs-lookup"><span data-stu-id="ee417-339">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="ee417-340">Konfigurace koncového bodu služby IIS</span><span class="sxs-lookup"><span data-stu-id="ee417-340">IIS endpoint configuration</span></span>

<span data-ttu-id="ee417-341">Při použití služby IIS jsou vazby adresy URL pro přepsání vazby služby IIS nastaveny buď `Listen` nebo `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="ee417-341">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="ee417-342">Další informace najdete v tématu [ASP.NET Core modulu](xref:host-and-deploy/aspnet-core-module) .</span><span class="sxs-lookup"><span data-stu-id="ee417-342">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="ee417-343">ListenOptions. Protocols</span><span class="sxs-lookup"><span data-stu-id="ee417-343">ListenOptions.Protocols</span></span>

<span data-ttu-id="ee417-344">Vlastnost určuje protokoly HTTP (`HttpProtocols`) povolené pro koncový bod připojení nebo pro server. `Protocols`</span><span class="sxs-lookup"><span data-stu-id="ee417-344">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="ee417-345">Přiřaďte hodnotu `Protocols` vlastnosti `HttpProtocols` z výčtu.</span><span class="sxs-lookup"><span data-stu-id="ee417-345">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="ee417-346">`HttpProtocols`hodnota výčtu</span><span class="sxs-lookup"><span data-stu-id="ee417-346">`HttpProtocols` enum value</span></span> | <span data-ttu-id="ee417-347">Povolený protokol připojení</span><span class="sxs-lookup"><span data-stu-id="ee417-347">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="ee417-348">Pouze HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="ee417-348">HTTP/1.1 only.</span></span> <span data-ttu-id="ee417-349">Dá se použít s TLS nebo bez něj.</span><span class="sxs-lookup"><span data-stu-id="ee417-349">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="ee417-350">Pouze HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="ee417-350">HTTP/2 only.</span></span> <span data-ttu-id="ee417-351">Dá se použít bez TLS jenom v případě, že klient podporuje [předchozí režim znalostní báze](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="ee417-351">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="ee417-352">HTTP/1.1 a HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="ee417-352">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="ee417-353">HTTP/2 vyžaduje, aby klient v rámci metody handshake protokolu TLS [(ALPN)](https://tools.ietf.org/html/rfc7301#section-3) vybral http/2. v opačném případě se připojení nastaví jako výchozí HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="ee417-353">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="ee417-354">Výchozí `ListenOptions.Protocols` hodnota pro libovolný koncový bod je `HttpProtocols.Http1AndHttp2`.</span><span class="sxs-lookup"><span data-stu-id="ee417-354">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="ee417-355">Omezení TLS pro HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="ee417-355">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="ee417-356">TLS verze 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="ee417-356">TLS version 1.2 or later</span></span>
* <span data-ttu-id="ee417-357">Opětovné vyjednávání je zakázané.</span><span class="sxs-lookup"><span data-stu-id="ee417-357">Renegotiation disabled</span></span>
* <span data-ttu-id="ee417-358">Komprese vypnuta</span><span class="sxs-lookup"><span data-stu-id="ee417-358">Compression disabled</span></span>
* <span data-ttu-id="ee417-359">Minimální velikosti dočasného výměny klíčů:</span><span class="sxs-lookup"><span data-stu-id="ee417-359">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="ee417-360">Eliptická křivka Diffie-Hellman (ECDH &lbrack;) [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; &ndash; 224 bity minima</span><span class="sxs-lookup"><span data-stu-id="ee417-360">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum</span></span>
  * <span data-ttu-id="ee417-361">Omezené pole Diffie- &lbrack; Hellman (DHE) `TLS12` &rbrack; &ndash; 2048 bity minima</span><span class="sxs-lookup"><span data-stu-id="ee417-361">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bits minimum</span></span>
* <span data-ttu-id="ee417-362">Šifrovací sada není zakázaná.</span><span class="sxs-lookup"><span data-stu-id="ee417-362">Cipher suite not blacklisted</span></span>

<span data-ttu-id="ee417-363">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack; `FIPS186` &rbrack; ve výchozím nastavení je podporována eliptická křivka P-256. &lbrack; `TLS-ECDHE` &rbrack;</span><span class="sxs-lookup"><span data-stu-id="ee417-363">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="ee417-364">Následující příklad povoluje připojení HTTP/1.1 a HTTP/2 na portu 8000.</span><span class="sxs-lookup"><span data-stu-id="ee417-364">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="ee417-365">Připojení TLS zabezpečuje pomocí zadaného certifikátu:</span><span class="sxs-lookup"><span data-stu-id="ee417-365">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="ee417-366">Volitelně můžete pomocí middleware připojení filtrovat handshake TLS na jednotlivých připojeních pro konkrétní šifry:</span><span class="sxs-lookup"><span data-stu-id="ee417-366">Optionally use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

```csharp
// using System.Net;
// using Microsoft.AspNetCore.Connections;

.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.UseConnectionHandler<TlsFilterConnectionHandler>();
    });
});
```

```csharp
using System;
using System.Buffers;
using System.Security.Authentication;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Connections;
using Microsoft.AspNetCore.Connections.Features;

public class TlsFilterConnectionHandler : ConnectionHandler
{
    public override async Task OnConnectedAsync(ConnectionContext connection)
    {
        var tlsFeature = connection.Features.Get<ITlsHandshakeFeature>();

        // Throw NotSupportedException for any cipher algorithm that the app doesn't
        // wish to support. Alternatively, define and compare
        // ITlsHandshakeFeature.CipherAlgorithm to a list of acceptable cipher
        // suites.
        //
        // A ITlsHandshakeFeature.CipherAlgorithm of CipherAlgorithmType.Null
        // indicates that no cipher algorithm supported by Kestrel matches the
        // requested algorithm(s).
        if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
        {
            throw new NotSupportedException("Prohibited cipher: " + tlsFeature.CipherAlgorithm);
        }

        while (true)
        {
            var result = await connection.Transport.Input.ReadAsync();
            var buffer = result.Buffer;

            if (!buffer.IsEmpty)
            {
                await connection.Transport.Output.WriteAsync(buffer.ToArray());
            }
            else if (result.IsCompleted)
            {
                break;
            }

            connection.Transport.Input.AdvanceTo(buffer.End);
        }
    }
}
```

<span data-ttu-id="ee417-367">*Nastavit protokol z konfigurace*</span><span class="sxs-lookup"><span data-stu-id="ee417-367">*Set the protocol from configuration*</span></span>

<span data-ttu-id="ee417-368">`CreateDefaultBuilder`Při `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` načítání konfigurace Kestrel ve výchozím nastavení volá.</span><span class="sxs-lookup"><span data-stu-id="ee417-368">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="ee417-369">Následující příklad *appSettings. JSON* vytvoří HTTP/1.1 jako výchozí protokol připojení pro všechny koncové body:</span><span class="sxs-lookup"><span data-stu-id="ee417-369">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="ee417-370">Následující příklad *appSettings. JSON* vytvoří protokol připojení HTTP/1.1 pro konkrétní koncový bod:</span><span class="sxs-lookup"><span data-stu-id="ee417-370">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1"
      }
    }
  }
}
```

<span data-ttu-id="ee417-371">Protokoly zadané v hodnotách přepisu kódu nastavených podle konfigurace</span><span class="sxs-lookup"><span data-stu-id="ee417-371">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="ee417-372">Konfigurace přenosu</span><span class="sxs-lookup"><span data-stu-id="ee417-372">Transport configuration</span></span>

<span data-ttu-id="ee417-373">Pro projekty, které vyžadují použití Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span><span class="sxs-lookup"><span data-stu-id="ee417-373">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span></span>

* <span data-ttu-id="ee417-374">Přidejte do souboru projektu aplikace závislost pro balíček [Microsoft. AspNetCore. Server. Kestrel. Transport. Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) :</span><span class="sxs-lookup"><span data-stu-id="ee417-374">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* <span data-ttu-id="ee417-375"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> Zavolat`IWebHostBuilder`na:</span><span class="sxs-lookup"><span data-stu-id="ee417-375">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> on the `IWebHostBuilder`:</span></span>

   ```csharp
   public class Program
   {
       public static void Main(string[] args)
       {
           CreateHostBuilder(args).Build().Run();
       }

       public static IHostBuilder CreateHostBuilder(string[] args) =>
           Host.CreateDefaultBuilder(args)
               .ConfigureWebHostDefaults(webBuilder =>
               {
                   webBuilder.UseLibuv();
                   webBuilder.UseStartup<Startup>();
               });
   }
   ```

### <a name="url-prefixes"></a><span data-ttu-id="ee417-376">Předpony adresy URL</span><span class="sxs-lookup"><span data-stu-id="ee417-376">URL prefixes</span></span>

<span data-ttu-id="ee417-377">Při použití `UseUrls`, `--urls` argumentu příkazového řádku `urls` , konfiguračního klíče hostitele nebo `ASPNETCORE_URLS` proměnné prostředí můžou být předpony adresy URL v některém z následujících formátů.</span><span class="sxs-lookup"><span data-stu-id="ee417-377">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="ee417-378">Platné jsou pouze předpony adresy URL protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="ee417-378">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="ee417-379">Kestrel nepodporuje protokol HTTPS při konfiguraci vazeb URL pomocí `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="ee417-379">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="ee417-380">Adresa IPv4 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="ee417-380">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="ee417-381">`0.0.0.0`je zvláštní případ, který se váže ke všem adresám IPv4.</span><span class="sxs-lookup"><span data-stu-id="ee417-381">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="ee417-382">Adresa IPv6 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="ee417-382">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="ee417-383">`[::]`je ekvivalentem protokolu IPv4 `0.0.0.0`pro protokol IPv6.</span><span class="sxs-lookup"><span data-stu-id="ee417-383">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="ee417-384">Název hostitele s číslem portu</span><span class="sxs-lookup"><span data-stu-id="ee417-384">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="ee417-385">Názvy `*`hostitelů, a `+`nejsou speciální.</span><span class="sxs-lookup"><span data-stu-id="ee417-385">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="ee417-386">Cokoli se nerozpoznalo jako platná IP adresa `localhost` nebo se váže ke všem IP adresám IPv4 a IPv6.</span><span class="sxs-lookup"><span data-stu-id="ee417-386">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="ee417-387">Pokud chcete navazovat různé názvy hostitelů na různé ASP.NET Core aplikace na stejném portu, použijte [http. sys](xref:fundamentals/servers/httpsys) nebo reverzní proxy server, třeba IIS, Nginx nebo Apache.</span><span class="sxs-lookup"><span data-stu-id="ee417-387">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="ee417-388">Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="ee417-388">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="ee417-389">Název `localhost` hostitele s číslem portu nebo IP adresou zpětné smyčky s číslem portu</span><span class="sxs-lookup"><span data-stu-id="ee417-389">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="ee417-390">Když `localhost` je zadaný, Kestrel se pokusí vytvořit propojení s rozhraními zpětné smyčky IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="ee417-390">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="ee417-391">Pokud je požadovaný port používán jinou službou na rozhraní zpětné smyčky, spuštění Kestrel se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="ee417-391">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="ee417-392">Pokud je rozhraní zpětné smyčky v žádném jiném případě nedostupné (většinou kvůli tomu, že protokol IPv6 není podporovaný), Kestrel zaznamená upozornění.</span><span class="sxs-lookup"><span data-stu-id="ee417-392">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="ee417-393">Filtrování hostitele</span><span class="sxs-lookup"><span data-stu-id="ee417-393">Host filtering</span></span>

<span data-ttu-id="ee417-394">I když Kestrel podporuje konfiguraci na základě prefixů `http://example.com:5000`, jako je, Kestrel převážně ignoruje název hostitele.</span><span class="sxs-lookup"><span data-stu-id="ee417-394">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="ee417-395">Hostitel `localhost` je zvláštní případ, který se používá pro vazbu na adresy zpětné smyčky.</span><span class="sxs-lookup"><span data-stu-id="ee417-395">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="ee417-396">Každý hostitel, který je jiný než explicitní IP adresa, se váže na všechny veřejné IP adresy.</span><span class="sxs-lookup"><span data-stu-id="ee417-396">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="ee417-397">`Host`hlavičky nejsou ověřené.</span><span class="sxs-lookup"><span data-stu-id="ee417-397">`Host` headers aren't validated.</span></span>

<span data-ttu-id="ee417-398">Jako alternativní řešení použijte middleware pro filtrování hostitele.</span><span class="sxs-lookup"><span data-stu-id="ee417-398">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="ee417-399">Middleware pro filtrování hostitele poskytuje balíček [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , který je implicitně k dispozici pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="ee417-399">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="ee417-400">Middleware je přidána pomocí <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, který volá <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="ee417-400">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="ee417-401">Middleware pro filtrování hostitele je ve výchozím nastavení zakázáno.</span><span class="sxs-lookup"><span data-stu-id="ee417-401">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="ee417-402">Chcete-li povolit middleware, `AllowedHosts` Definujte klíč v souboru *appSettings. JSON*/ *\< appSettings. Environment >. JSON*.</span><span class="sxs-lookup"><span data-stu-id="ee417-402">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="ee417-403">Hodnota je seznam názvů hostitelů oddělených středníkem bez čísel portů:</span><span class="sxs-lookup"><span data-stu-id="ee417-403">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="ee417-404">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="ee417-404">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="ee417-405">[Middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer) má <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> také možnost.</span><span class="sxs-lookup"><span data-stu-id="ee417-405">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="ee417-406">Middleware předávaných hlaviček a middleware pro filtrování hostitele mají podobné funkce pro různé scénáře.</span><span class="sxs-lookup"><span data-stu-id="ee417-406">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="ee417-407">Nastavení `AllowedHosts` pomocí předávaných middlewarových hlaviček je vhodné `Host` v případě, že hlavička není zachována při předávání požadavků pomocí reverzního proxy server nebo nástroje pro vyrovnávání zatížení.</span><span class="sxs-lookup"><span data-stu-id="ee417-407">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="ee417-408">Nastavení `AllowedHosts` pomocí middlewaru pro filtrování hostitele je vhodné, když se Kestrel používá jako veřejný hraniční Server nebo `Host` když se hlavička přímo přepošle.</span><span class="sxs-lookup"><span data-stu-id="ee417-408">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="ee417-409">Další informace o middlewaru předávaných hlaviček najdete <xref:host-and-deploy/proxy-load-balancer>v tématu.</span><span class="sxs-lookup"><span data-stu-id="ee417-409">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="ee417-410">Kestrel je [webový server pro různé platformy pro ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="ee417-410">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="ee417-411">Kestrel je webový server, který je ve výchozím nastavení součástí ASP.NET Core šablon projektů.</span><span class="sxs-lookup"><span data-stu-id="ee417-411">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="ee417-412">Kestrel podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="ee417-412">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="ee417-413">HTTPS</span><span class="sxs-lookup"><span data-stu-id="ee417-413">HTTPS</span></span>
* <span data-ttu-id="ee417-414">Neprůhledný upgrade používaný k povolení [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="ee417-414">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="ee417-415">Technologie UNIX Sockets s vysokým výkonem za Nginx</span><span class="sxs-lookup"><span data-stu-id="ee417-415">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="ee417-416">HTTP/2 (kromě macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="ee417-416">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="ee417-417">&dagger;HTTP/2 se v budoucí verzi podporuje v macOS.</span><span class="sxs-lookup"><span data-stu-id="ee417-417">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="ee417-418">Kestrel se podporuje na všech platformách a verzích, které podporuje .NET Core.</span><span class="sxs-lookup"><span data-stu-id="ee417-418">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="ee417-419">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ee417-419">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="ee417-420">Podpora HTTP/2</span><span class="sxs-lookup"><span data-stu-id="ee417-420">HTTP/2 support</span></span>

<span data-ttu-id="ee417-421">[Http/2](https://httpwg.org/specs/rfc7540.html) je k dispozici pro ASP.NET Core aplikace, pokud jsou splněné následující základní požadavky:</span><span class="sxs-lookup"><span data-stu-id="ee417-421">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="ee417-422">Operační systém&dagger;</span><span class="sxs-lookup"><span data-stu-id="ee417-422">Operating system&dagger;</span></span>
  * <span data-ttu-id="ee417-423">Windows Server 2016/Windows 10 nebo novější&Dagger;</span><span class="sxs-lookup"><span data-stu-id="ee417-423">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="ee417-424">Linux s OpenSSL 1.0.2 nebo novějším (například Ubuntu 16,04 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="ee417-424">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="ee417-425">Cílová architektura: .NET Core 2,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="ee417-425">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="ee417-426">Připojení [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="ee417-426">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="ee417-427">Protokol TLS 1.2 nebo vyšší připojení</span><span class="sxs-lookup"><span data-stu-id="ee417-427">TLS 1.2 or later connection</span></span>

<span data-ttu-id="ee417-428">&dagger;HTTP/2 se v budoucí verzi podporuje v macOS.</span><span class="sxs-lookup"><span data-stu-id="ee417-428">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="ee417-429">&Dagger;Kestrel má omezená podpora HTTP/2 na Windows Serveru 2012 R2 a Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="ee417-429">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="ee417-430">Podpora je omezená, protože seznam podporovaných šifrovacích sad TLS, které jsou k dispozici v těchto operačních systémech, je omezený.</span><span class="sxs-lookup"><span data-stu-id="ee417-430">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="ee417-431">Pro zabezpečení připojení TLS může být vyžadován certifikát vygenerovaný pomocí algoritmu ECDSA (s připojením typu eliptická křivka).</span><span class="sxs-lookup"><span data-stu-id="ee417-431">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="ee417-432">Pokud se připojení HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) sestavy `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="ee417-432">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="ee417-433">HTTP/2 je ve výchozím nastavení zakázané.</span><span class="sxs-lookup"><span data-stu-id="ee417-433">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="ee417-434">Další informace o konfiguraci najdete v částech [Možnosti Kestrel](#kestrel-options) a [ListenOptions. Protocols](#listenoptionsprotocols) .</span><span class="sxs-lookup"><span data-stu-id="ee417-434">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="ee417-435">Kdy použít Kestrel s reverzním proxy serverem</span><span class="sxs-lookup"><span data-stu-id="ee417-435">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="ee417-436">Kestrel může používat sám sebe nebo s *reverzními proxy server*, jako je [Internetová informační služba (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="ee417-436">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="ee417-437">Reverzní proxy server přijímá požadavky HTTP ze sítě a přesměruje je na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ee417-437">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="ee417-438">Kestrel použitý jako hraniční webový server (internetový):</span><span class="sxs-lookup"><span data-stu-id="ee417-438">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel komunikuje přímo s internetem bez zpětného proxy server](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="ee417-440">Kestrel se používá v konfiguraci reverzního proxy serveru:</span><span class="sxs-lookup"><span data-stu-id="ee417-440">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel nepřímo komunikuje s internetem prostřednictvím reverzní proxy server, jako je IIS, Nginx nebo Apache.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="ee417-442">Buď konfigurace, s zpětným proxy server nebo bez něj, je podporovaná konfigurace hostování.</span><span class="sxs-lookup"><span data-stu-id="ee417-442">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="ee417-443">Kestrel používané jako hraniční Server bez reverzních proxy server nepodporuje sdílení stejné IP adresy a portu mezi více procesy.</span><span class="sxs-lookup"><span data-stu-id="ee417-443">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="ee417-444">Když je Kestrel nakonfigurovaný k naslouchání na portu, Kestrel zpracovává veškerý provoz tohoto portu bez ohledu na `Host` hlavičky požadavků.</span><span class="sxs-lookup"><span data-stu-id="ee417-444">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="ee417-445">Reverzní proxy server, který může sdílet porty, má schopnost přesměrovat požadavky na Kestrel na jedinečné IP adresy a porty.</span><span class="sxs-lookup"><span data-stu-id="ee417-445">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="ee417-446">I v případě, že se zpětný proxy server nepožaduje, může být použití reverzní proxy server vhodnou volbou.</span><span class="sxs-lookup"><span data-stu-id="ee417-446">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="ee417-447">Reverzní proxy:</span><span class="sxs-lookup"><span data-stu-id="ee417-447">A reverse proxy:</span></span>

* <span data-ttu-id="ee417-448">Může omezit vystavenou veřejnou plochu v aplikacích, které hostuje.</span><span class="sxs-lookup"><span data-stu-id="ee417-448">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="ee417-449">Poskytněte další vrstvu konfigurace a obrany.</span><span class="sxs-lookup"><span data-stu-id="ee417-449">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="ee417-450">Může se lépe integrovat se stávající infrastrukturou.</span><span class="sxs-lookup"><span data-stu-id="ee417-450">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="ee417-451">Zjednodušte vyrovnávání zatížení a konfiguraci zabezpečené komunikace (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="ee417-451">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="ee417-452">Pouze reverzní proxy server vyžaduje certifikát X. 509 a tento server může komunikovat se servery aplikace v interní síti pomocí prostého protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="ee417-452">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="ee417-453">Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="ee417-453">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="ee417-454">Použití Kestrel v aplikacích ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ee417-454">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="ee417-455">Balíček [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) je součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="ee417-455">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="ee417-456">ASP.NET Core šablony projektu ve výchozím nastavení používají Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ee417-456">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="ee417-457">V *program.cs*kód šablony volá <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, který volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> na pozadí.</span><span class="sxs-lookup"><span data-stu-id="ee417-457">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="ee417-458">Chcete-li po volání `CreateDefaultBuilder`poskytnout další konfiguraci, použijte: `ConfigureKestrel`</span><span class="sxs-lookup"><span data-stu-id="ee417-458">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="ee417-459">`CreateDefaultBuilder` Pokud aplikace nevolá pro nastavení hostitele, <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> zavolejte **před** voláním `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="ee417-459">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

```csharp
public static void Main(string[] args)
{
    var host = new WebHostBuilder()
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseKestrel()
        .UseIISIntegration()
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        })
        .Build();

    host.Run();
}
```

## <a name="kestrel-options"></a><span data-ttu-id="ee417-460">Kestrel možnosti</span><span class="sxs-lookup"><span data-stu-id="ee417-460">Kestrel options</span></span>

<span data-ttu-id="ee417-461">Webový server Kestrel má možnosti konfigurace omezení, které jsou zvláště užitečné v internetových nasazeních.</span><span class="sxs-lookup"><span data-stu-id="ee417-461">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="ee417-462">Nastavte omezení pro <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> vlastnost <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> třídy.</span><span class="sxs-lookup"><span data-stu-id="ee417-462">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="ee417-463">Vlastnost obsahuje instanci <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>třídy. `Limits`</span><span class="sxs-lookup"><span data-stu-id="ee417-463">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="ee417-464">V následujících příkladech se <xref:Microsoft.AspNetCore.Server.Kestrel.Core> používá obor názvů:</span><span class="sxs-lookup"><span data-stu-id="ee417-464">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

### <a name="keep-alive-timeout"></a><span data-ttu-id="ee417-465">Časový limit Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="ee417-465">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="ee417-466">Získá nebo nastaví [časový limit zachování](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="ee417-466">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="ee417-467">Výchozí hodnota je 2 minuty.</span><span class="sxs-lookup"><span data-stu-id="ee417-467">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="ee417-468">Maximální počet připojení klientů</span><span class="sxs-lookup"><span data-stu-id="ee417-468">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="ee417-469">Maximální počet souběžných otevřených připojení TCP lze pro celou aplikaci nastavit pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="ee417-469">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="ee417-470">Existuje zvláštní omezení pro připojení, která byla upgradována z protokolu HTTP nebo HTTPS na jiný protokol (například na žádost WebSockets).</span><span class="sxs-lookup"><span data-stu-id="ee417-470">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="ee417-471">Po upgradu připojení se nepočítá na `MaxConcurrentConnections` základě limitu.</span><span class="sxs-lookup"><span data-stu-id="ee417-471">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="ee417-472">Ve výchozím nastavení je maximální počet připojení neomezené (null).</span><span class="sxs-lookup"><span data-stu-id="ee417-472">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="ee417-473">Maximální velikost textu požadavku</span><span class="sxs-lookup"><span data-stu-id="ee417-473">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="ee417-474">Výchozí maximální velikost textu požadavku je 30 000 000 bajtů, což je přibližně 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="ee417-474">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="ee417-475">Doporučený postup pro přepsání limitu v aplikaci ASP.NET Core MVC je použití <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> atributu pro metodu akce:</span><span class="sxs-lookup"><span data-stu-id="ee417-475">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="ee417-476">Tady je příklad, který ukazuje, jak nakonfigurovat omezení pro aplikaci na každém požadavku:</span><span class="sxs-lookup"><span data-stu-id="ee417-476">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="ee417-477">Přepsat nastavení u konkrétního požadavku v middleware:</span><span class="sxs-lookup"><span data-stu-id="ee417-477">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="ee417-478">Výjimka je vyvolána, pokud aplikace po zahájení aplikace nakonfiguruje limit na žádost.</span><span class="sxs-lookup"><span data-stu-id="ee417-478">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="ee417-479">Existuje vlastnost, která určuje, `MaxRequestBodySize` zda je vlastnost ve stavu jen pro čtení, což znamená, že je příliš pozdě pro konfiguraci limitu. `IsReadOnly`</span><span class="sxs-lookup"><span data-stu-id="ee417-479">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="ee417-480">Když je aplikace spouštěna [mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za [ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module), je omezení velikosti textu požadavku Kestrel zakázané, protože služba IIS tento limit již nastavuje.</span><span class="sxs-lookup"><span data-stu-id="ee417-480">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="ee417-481">Minimální rychlost těla zprávy požadavku</span><span class="sxs-lookup"><span data-stu-id="ee417-481">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="ee417-482">Kestrel kontroluje každou sekundu, pokud data přicházejí za zadanou rychlost v bajtech za sekundu.</span><span class="sxs-lookup"><span data-stu-id="ee417-482">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="ee417-483">Pokud frekvence klesne pod minimální hodnotu, vypršel časový limit připojení. Doba odkladu je doba, kterou Kestrel umožňuje klientovi zvýšit rychlost odesílání až na minimum; frekvence není během této doby kontrolována.</span><span class="sxs-lookup"><span data-stu-id="ee417-483">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="ee417-484">Doba odkladu pomáhá vyhnout se vyřazení připojení, která při počátečním posílání dat zpomalují rychlost, kvůli pomalému spuštění protokolu TCP.</span><span class="sxs-lookup"><span data-stu-id="ee417-484">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="ee417-485">Výchozí minimální sazba je 240 bajtů za sekundu s obdobím 5 sekund odkladu.</span><span class="sxs-lookup"><span data-stu-id="ee417-485">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="ee417-486">Pro odpověď platí také minimální sazba.</span><span class="sxs-lookup"><span data-stu-id="ee417-486">A minimum rate also applies to the response.</span></span> <span data-ttu-id="ee417-487">Kód pro nastavení limitu požadavku a limit odezvy je stejný, s výjimkou `RequestBody` , že existují nebo `Response` v názvech vlastností a rozhraní.</span><span class="sxs-lookup"><span data-stu-id="ee417-487">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="ee417-488">Tady je příklad, který ukazuje, jak nakonfigurovat minimální datové tarify v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="ee417-488">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="ee417-489">Přepsat minimální limity přenosové rychlosti na žádost v middlewaru:</span><span class="sxs-lookup"><span data-stu-id="ee417-489">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="ee417-490">Pro požadavky HTTP/2 se v předchozí ukázce neodkazuje žádná míra funkce, `HttpContext.Features` protože Změna omezení přenosové rychlosti na základě jednotlivých požadavků není podporovaná pro HTTP/2 kvůli podpoře protokolu pro multiplexování požadavků.</span><span class="sxs-lookup"><span data-stu-id="ee417-490">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="ee417-491">Omezení přenosové rychlosti pro všechny servery `KestrelServerOptions.Limits` jsou nakonfigurovaná přes protokol HTTP/1. x i pro připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="ee417-491">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="ee417-492">Časový limit hlaviček žádostí</span><span class="sxs-lookup"><span data-stu-id="ee417-492">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="ee417-493">Získá nebo nastaví maximální dobu, po kterou server stráví příjem hlaviček požadavků.</span><span class="sxs-lookup"><span data-stu-id="ee417-493">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="ee417-494">Výchozí hodnota je 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="ee417-494">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="ee417-495">Maximální počet proudů na připojení</span><span class="sxs-lookup"><span data-stu-id="ee417-495">Maximum streams per connection</span></span>

<span data-ttu-id="ee417-496">`Http2.MaxStreamsPerConnection`omezuje počet souběžných datových proudů požadavků na připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="ee417-496">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="ee417-497">Nadbytečné proudy jsou odmítnuty.</span><span class="sxs-lookup"><span data-stu-id="ee417-497">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="ee417-498">Výchozí hodnota je 100.</span><span class="sxs-lookup"><span data-stu-id="ee417-498">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="ee417-499">Velikost tabulky hlaviček</span><span class="sxs-lookup"><span data-stu-id="ee417-499">Header table size</span></span>

<span data-ttu-id="ee417-500">Dekodér HPACK dekomprimuje hlavičky HTTP pro připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="ee417-500">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="ee417-501">`Http2.HeaderTableSize`omezuje velikost kompresní tabulky hlaviček, kterou používá dekodér HPACK.</span><span class="sxs-lookup"><span data-stu-id="ee417-501">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="ee417-502">Hodnota je uvedena v oktetech a musí být větší než nula (0).</span><span class="sxs-lookup"><span data-stu-id="ee417-502">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="ee417-503">Výchozí hodnota je 4096.</span><span class="sxs-lookup"><span data-stu-id="ee417-503">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="ee417-504">Maximální velikost rámce</span><span class="sxs-lookup"><span data-stu-id="ee417-504">Maximum frame size</span></span>

<span data-ttu-id="ee417-505">`Http2.MaxFrameSize`Určuje maximální velikost datové části rámce připojení HTTP/2, která se má přijmout.</span><span class="sxs-lookup"><span data-stu-id="ee417-505">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="ee417-506">Hodnota je uvedena v oktetech a musí být v rozmezí 2 ^ 14 (16 384) až 2 ^ 24-1 (16 777 215).</span><span class="sxs-lookup"><span data-stu-id="ee417-506">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="ee417-507">Výchozí hodnota je 2 ^ 14 (16 384).</span><span class="sxs-lookup"><span data-stu-id="ee417-507">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="ee417-508">Maximální velikost hlavičky požadavku</span><span class="sxs-lookup"><span data-stu-id="ee417-508">Maximum request header size</span></span>

<span data-ttu-id="ee417-509">`Http2.MaxRequestHeaderFieldSize`Určuje maximální povolenou velikost v oktetech hodnot hlaviček požadavku.</span><span class="sxs-lookup"><span data-stu-id="ee417-509">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="ee417-510">Toto omezení platí pro názvy i hodnoty společně v komprimovaných a nekomprimovaných reprezentujcích.</span><span class="sxs-lookup"><span data-stu-id="ee417-510">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="ee417-511">Hodnota musí být větší než nula (0).</span><span class="sxs-lookup"><span data-stu-id="ee417-511">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="ee417-512">Výchozí hodnota je 8 192.</span><span class="sxs-lookup"><span data-stu-id="ee417-512">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="ee417-513">Velikost okna počátečního připojení</span><span class="sxs-lookup"><span data-stu-id="ee417-513">Initial connection window size</span></span>

<span data-ttu-id="ee417-514">`Http2.InitialConnectionWindowSize`Určuje maximální velikost textu požadavku v bajtech, které jsou v jednom okamžiku agregovány v rámci všech požadavků (datových proudů) na připojení.</span><span class="sxs-lookup"><span data-stu-id="ee417-514">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="ee417-515">Žádosti jsou také omezené pomocí `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="ee417-515">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="ee417-516">Hodnota musí být větší než nebo rovna 65 535 a menší než 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="ee417-516">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="ee417-517">Výchozí hodnota je 128 KB (131 072).</span><span class="sxs-lookup"><span data-stu-id="ee417-517">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="ee417-518">Počáteční velikost okna datového proudu</span><span class="sxs-lookup"><span data-stu-id="ee417-518">Initial stream window size</span></span>

<span data-ttu-id="ee417-519">`Http2.InitialStreamWindowSize`Určuje maximální velikost textu požadavku v bajtech na jednu žádost (Stream) na jednom místě.</span><span class="sxs-lookup"><span data-stu-id="ee417-519">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="ee417-520">Žádosti jsou také omezené pomocí `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="ee417-520">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="ee417-521">Hodnota musí být větší než nebo rovna 65 535 a menší než 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="ee417-521">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="ee417-522">Výchozí hodnota je 96 KB (98 304).</span><span class="sxs-lookup"><span data-stu-id="ee417-522">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="ee417-523">Synchronní v/v</span><span class="sxs-lookup"><span data-stu-id="ee417-523">Synchronous IO</span></span>

<span data-ttu-id="ee417-524"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>Určuje, zda je pro požadavek a odpověď povolena synchronní v/v.</span><span class="sxs-lookup"><span data-stu-id="ee417-524"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="ee417-525">Výchozí hodnota je `true`.</span><span class="sxs-lookup"><span data-stu-id="ee417-525">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="ee417-526">Velký počet blokování synchronních vstupně-výstupních operací může vést ke vyčerpání fondu vláken, což aplikaci nereaguje.</span><span class="sxs-lookup"><span data-stu-id="ee417-526">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="ee417-527">Povolit `AllowSynchronousIO` pouze při použití knihovny, která nepodporuje asynchronní v/v.</span><span class="sxs-lookup"><span data-stu-id="ee417-527">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="ee417-528">Následující příklad povoluje synchronní vstupně-výstupní operace:</span><span class="sxs-lookup"><span data-stu-id="ee417-528">The following example enables synchronous IO:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="ee417-529">Informace o dalších možnostech a omezeních Kestrel najdete v tématech:</span><span class="sxs-lookup"><span data-stu-id="ee417-529">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="ee417-530">Konfigurace koncového bodu</span><span class="sxs-lookup"><span data-stu-id="ee417-530">Endpoint configuration</span></span>

<span data-ttu-id="ee417-531">Ve výchozím nastavení ASP.NET Core váže k:</span><span class="sxs-lookup"><span data-stu-id="ee417-531">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="ee417-532">`https://localhost:5001`(Pokud je k dispozici místní vývojový certifikát)</span><span class="sxs-lookup"><span data-stu-id="ee417-532">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="ee417-533">Zadejte adresy URL pomocí:</span><span class="sxs-lookup"><span data-stu-id="ee417-533">Specify URLs using the:</span></span>

* <span data-ttu-id="ee417-534">`ASPNETCORE_URLS` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="ee417-534">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="ee417-535">`--urls`argument příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="ee417-535">`--urls` command-line argument.</span></span>
* <span data-ttu-id="ee417-536">`urls`konfigurační klíč hostitele.</span><span class="sxs-lookup"><span data-stu-id="ee417-536">`urls` host configuration key.</span></span>
* <span data-ttu-id="ee417-537">`UseUrls`Metoda rozšíření</span><span class="sxs-lookup"><span data-stu-id="ee417-537">`UseUrls` extension method.</span></span>

<span data-ttu-id="ee417-538">Hodnota zadaná pomocí těchto přístupů může být jeden nebo více koncových bodů HTTP a HTTPS (HTTPS, pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="ee417-538">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="ee417-539">Nakonfigurujte hodnotu jako seznam oddělený středníkem (například `"Urls": "http://localhost:8000; http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="ee417-539">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="ee417-540">Další informace o těchto přístupůch najdete v tématu [adresy URL serveru](xref:fundamentals/host/web-host#server-urls) a [potlačení konfigurace](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="ee417-540">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="ee417-541">Vytvoří se vývojový certifikát:</span><span class="sxs-lookup"><span data-stu-id="ee417-541">A development certificate is created:</span></span>

* <span data-ttu-id="ee417-542">Po instalaci [.NET Core SDK](/dotnet/core/sdk) .</span><span class="sxs-lookup"><span data-stu-id="ee417-542">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="ee417-543">[Nástroj pro vývoj a certifikáty](xref:aspnetcore-2.1#https) slouží k vytvoření certifikátu.</span><span class="sxs-lookup"><span data-stu-id="ee417-543">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="ee417-544">Některé prohlížeče vyžadují udělení explicitního oprávnění pro důvěřování místního vývojového certifikátu.</span><span class="sxs-lookup"><span data-stu-id="ee417-544">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="ee417-545">Šablony projektů konfigurují aplikace tak, aby běžely ve výchozím nastavení HTTPS a zahrnovaly [přesměrování https a podporu HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="ee417-545">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="ee417-546">Volání <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> nebo <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> metody<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> pro konfiguraci předpon adres URL a portů pro Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ee417-546">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="ee417-547">`UseUrls`, argument `urls` `ASPNETCORE_URLS` příkazového řádku, konfigurační klíč hostitele a proměnná prostředí fungují také, ale mají omezení zaznamenaná později v této části (pro koncový bod HTTPS musí být dostupný výchozí certifikát.) `--urls` konfigurace).</span><span class="sxs-lookup"><span data-stu-id="ee417-547">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="ee417-548">`KestrelServerOptions`rozšířeného</span><span class="sxs-lookup"><span data-stu-id="ee417-548">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="ee417-549">ConfigureEndpointDefaults (akce\<ListenOptions >)</span><span class="sxs-lookup"><span data-stu-id="ee417-549">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="ee417-550">Určuje konfiguraci `Action` , která se spustí pro každý zadaný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="ee417-550">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="ee417-551">Volání `ConfigureEndpointDefaults` několikrát nahrazuje předchozí `Action` `Action` zadanou parametr s.</span><span class="sxs-lookup"><span data-stu-id="ee417-551">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="ee417-552">ConfigureHttpsDefaults (akce\<HttpsConnectionAdapterOptions >)</span><span class="sxs-lookup"><span data-stu-id="ee417-552">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="ee417-553">Určuje konfiguraci `Action` , která se má spustit pro každý koncový bod HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ee417-553">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="ee417-554">Volání `ConfigureHttpsDefaults` několikrát nahrazuje předchozí `Action` `Action` zadanou parametr s.</span><span class="sxs-lookup"><span data-stu-id="ee417-554">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

### <a name="configureiconfiguration"></a><span data-ttu-id="ee417-555">Konfigurace (IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="ee417-555">Configure(IConfiguration)</span></span>

<span data-ttu-id="ee417-556">Vytvoří zavaděč konfigurace pro nastavení Kestrel, který přebírá <xref:Microsoft.Extensions.Configuration.IConfiguration> jako vstup.</span><span class="sxs-lookup"><span data-stu-id="ee417-556">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="ee417-557">Konfigurace musí být vymezená na konfigurační oddíl pro Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ee417-557">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="ee417-558">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="ee417-558">ListenOptions.UseHttps</span></span>

<span data-ttu-id="ee417-559">Nakonfigurujte Kestrel tak, aby používal protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ee417-559">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="ee417-560">`ListenOptions.UseHttps`SND</span><span class="sxs-lookup"><span data-stu-id="ee417-560">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="ee417-561">`UseHttps`&ndash; Nakonfigurujte Kestrel tak, aby používal HTTPS s výchozím certifikátem.</span><span class="sxs-lookup"><span data-stu-id="ee417-561">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="ee417-562">Vyvolá výjimku, pokud není nakonfigurován žádný výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="ee417-562">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="ee417-563">`ListenOptions.UseHttps`ukazatelů</span><span class="sxs-lookup"><span data-stu-id="ee417-563">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="ee417-564">`filename`je cesta a název souboru certifikátu vzhledem k adresáři, který obsahuje soubory obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="ee417-564">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="ee417-565">`password`je vyžadováno heslo pro přístup k datům certifikátu X. 509.</span><span class="sxs-lookup"><span data-stu-id="ee417-565">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="ee417-566">`configureOptions``Action` je ke`HttpsConnectionAdapterOptions`konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="ee417-566">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="ee417-567">`ListenOptions`Vrátí.</span><span class="sxs-lookup"><span data-stu-id="ee417-567">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="ee417-568">`storeName`je úložiště certifikátů, ze kterého se má certifikát načíst.</span><span class="sxs-lookup"><span data-stu-id="ee417-568">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="ee417-569">`subject`je název subjektu certifikátu.</span><span class="sxs-lookup"><span data-stu-id="ee417-569">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="ee417-570">`allowInvalid`Určuje, zda by měly být zváženy neplatné certifikáty, například certifikáty podepsané svým držitelem.</span><span class="sxs-lookup"><span data-stu-id="ee417-570">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="ee417-571">`location`je umístěním úložiště, ze kterého se má certifikát načíst.</span><span class="sxs-lookup"><span data-stu-id="ee417-571">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="ee417-572">`serverCertificate`je certifikát X. 509.</span><span class="sxs-lookup"><span data-stu-id="ee417-572">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="ee417-573">V produkčním prostředí musí být HTTPS explicitně nakonfigurovaný.</span><span class="sxs-lookup"><span data-stu-id="ee417-573">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="ee417-574">Musí být zadán minimálně výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="ee417-574">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="ee417-575">Podporované konfigurace popsané dál:</span><span class="sxs-lookup"><span data-stu-id="ee417-575">Supported configurations described next:</span></span>

* <span data-ttu-id="ee417-576">Žádná konfigurace</span><span class="sxs-lookup"><span data-stu-id="ee417-576">No configuration</span></span>
* <span data-ttu-id="ee417-577">Nahradit výchozí certifikát z konfigurace</span><span class="sxs-lookup"><span data-stu-id="ee417-577">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="ee417-578">Změna výchozích hodnot v kódu</span><span class="sxs-lookup"><span data-stu-id="ee417-578">Change the defaults in code</span></span>

<span data-ttu-id="ee417-579">*Žádná konfigurace*</span><span class="sxs-lookup"><span data-stu-id="ee417-579">*No configuration*</span></span>

<span data-ttu-id="ee417-580">Kestrel naslouchá na `http://localhost:5000` a `https://localhost:5001` (Pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="ee417-580">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="ee417-581">*Nahradit výchozí certifikát z konfigurace*</span><span class="sxs-lookup"><span data-stu-id="ee417-581">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="ee417-582">`CreateDefaultBuilder`Při `Configure(context.Configuration.GetSection("Kestrel"))` načítání konfigurace Kestrel ve výchozím nastavení volá.</span><span class="sxs-lookup"><span data-stu-id="ee417-582">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="ee417-583">Pro Kestrel je k dispozici výchozí schéma konfigurace nastavení aplikace HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ee417-583">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="ee417-584">Nakonfigurujte několik koncových bodů, včetně adres URL a certifikátů, které se mají použít, buď ze souboru na disku, nebo z úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="ee417-584">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="ee417-585">V následujícím příkladu *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="ee417-585">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="ee417-586">Nastavte **AllowInvalid** na `true` , aby povolovala použití neplatných certifikátů (například certifikátů podepsaných svým držitelem).</span><span class="sxs-lookup"><span data-stu-id="ee417-586">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="ee417-587">Libovolný koncový bod HTTPS, který neurčuje certifikát (**HttpsDefaultCert** v následujícím příkladu), se vrátí k certifikátu definovanému v části **certifikáty** > **výchozí** nebo vývojový certifikát.</span><span class="sxs-lookup"><span data-stu-id="ee417-587">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="ee417-588">Alternativou k použití **cesty** a **hesla** pro libovolný uzel certifikátu je zadání certifikátu pomocí polí úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="ee417-588">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="ee417-589">Například > **výchozí** certifikát může být zadán jako:</span><span class="sxs-lookup"><span data-stu-id="ee417-589">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="ee417-590">Poznámky ke schématu:</span><span class="sxs-lookup"><span data-stu-id="ee417-590">Schema notes:</span></span>

* <span data-ttu-id="ee417-591">V názvech koncových bodů se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="ee417-591">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="ee417-592">Například `HTTPS` a`Https` jsou platné.</span><span class="sxs-lookup"><span data-stu-id="ee417-592">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="ee417-593">U každého koncového bodu je vyžadován parametr.`Url`</span><span class="sxs-lookup"><span data-stu-id="ee417-593">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="ee417-594">Formát pro tento parametr je stejný jako konfigurační parametr nejvyšší úrovně `Urls` s tím rozdílem, že je omezen na jedinou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="ee417-594">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="ee417-595">Tyto koncové body nahrazují hodnoty definované v konfiguraci nejvyšší úrovně `Urls` místo jejich přidání do těchto koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="ee417-595">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="ee417-596">Koncové body definované v kódu `Listen` prostřednictvím jsou kumulativní pomocí koncových bodů definovaných v konfiguračním oddílu.</span><span class="sxs-lookup"><span data-stu-id="ee417-596">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="ee417-597">`Certificate` Oddíl je nepovinný.</span><span class="sxs-lookup"><span data-stu-id="ee417-597">The `Certificate` section is optional.</span></span> <span data-ttu-id="ee417-598">Pokud není `Certificate` oddíl zadaný, použijí se výchozí hodnoty definované v dřívějších scénářích.</span><span class="sxs-lookup"><span data-stu-id="ee417-598">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="ee417-599">Pokud nejsou k dispozici žádné výchozí hodnoty, Server vyvolá výjimku a nemůže se spustit.</span><span class="sxs-lookup"><span data-stu-id="ee417-599">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="ee417-600">&ndash;&ndash; Oddíl podporuje jak heslo cesty, tak certifikáty úložiště subjektu. `Certificate`</span><span class="sxs-lookup"><span data-stu-id="ee417-600">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="ee417-601">V takovém případě může být definován libovolný počet koncových bodů, pokud to nezpůsobí konflikty portů.</span><span class="sxs-lookup"><span data-stu-id="ee417-601">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="ee417-602">`options.Configure(context.Configuration.GetSection("{SECTION}"))``KestrelConfigurationLoader` vrátí metodu`.Endpoint(string name, listenOptions => { })` s metodou, kterou lze použít k doplnění nastavení nakonfigurovaného koncového bodu:</span><span class="sxs-lookup"><span data-stu-id="ee417-602">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="ee417-603">`KestrelServerOptions.ConfigurationLoader`k pokračování v iteraci u stávajícího zavaděče se dá získat přímý odkaz, jako je třeba <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>ten, který poskytuje.</span><span class="sxs-lookup"><span data-stu-id="ee417-603">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="ee417-604">Konfigurační oddíl pro každý koncový bod je k dispozici na možnostech v `Endpoint` metodě, aby bylo možné číst vlastní nastavení.</span><span class="sxs-lookup"><span data-stu-id="ee417-604">The configuration section for each endpoint is a available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="ee417-605">Více konfigurací může být načteno voláním `options.Configure(context.Configuration.GetSection("{SECTION}"))` znovu s jiným oddílem.</span><span class="sxs-lookup"><span data-stu-id="ee417-605">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="ee417-606">Je použita pouze poslední konfigurace, pokud `Load` není explicitně volána při předchozích instancích.</span><span class="sxs-lookup"><span data-stu-id="ee417-606">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="ee417-607">Metapackage nevolá `Load` , aby se jeho výchozí konfigurační oddíl mohl nahradit.</span><span class="sxs-lookup"><span data-stu-id="ee417-607">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="ee417-608">`KestrelConfigurationLoader`zrcadlí `KestrelServerOptions` `Endpoint` rodinu rozhraní API z as přetížení, takže koncové body kódu a konfigurace lze konfigurovat na stejném místě. `Listen`</span><span class="sxs-lookup"><span data-stu-id="ee417-608">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="ee417-609">Tato přetížení nepoužívají názvy a využívají pouze výchozí nastavení z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="ee417-609">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="ee417-610">*Změna výchozích hodnot v kódu*</span><span class="sxs-lookup"><span data-stu-id="ee417-610">*Change the defaults in code*</span></span>

<span data-ttu-id="ee417-611">`ConfigureEndpointDefaults`a `ConfigureHttpsDefaults` dá se použít ke změně výchozího nastavení pro `ListenOptions` a `HttpsConnectionAdapterOptions`, včetně přepsání výchozího certifikátu zadaného v předchozím scénáři.</span><span class="sxs-lookup"><span data-stu-id="ee417-611">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="ee417-612">`ConfigureEndpointDefaults`a `ConfigureHttpsDefaults` měla by být volána před konfigurací koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="ee417-612">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="ee417-613">*Podpora Kestrel pro SNI*</span><span class="sxs-lookup"><span data-stu-id="ee417-613">*Kestrel support for SNI*</span></span>

<span data-ttu-id="ee417-614">[Indikace názvu serveru (SNI)](https://tools.ietf.org/html/rfc6066#section-3) lze použít k hostování více domén na stejné IP adrese a portu.</span><span class="sxs-lookup"><span data-stu-id="ee417-614">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="ee417-615">Aby SNI fungovalo, klient pošle název hostitele pro zabezpečenou relaci serveru během metody handshake TLS, aby server mohl poskytovat správný certifikát.</span><span class="sxs-lookup"><span data-stu-id="ee417-615">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="ee417-616">Klient používá poskytnutý certifikát pro šifrovanou komunikaci se serverem během zabezpečené relace, která následuje za metodou handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="ee417-616">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="ee417-617">Kestrel podporuje sni prostřednictvím `ServerCertificateSelector` zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="ee417-617">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="ee417-618">Zpětné volání je vyvoláno jednou za připojení, které aplikaci umožní zkontrolovat název hostitele a vybrat příslušný certifikát.</span><span class="sxs-lookup"><span data-stu-id="ee417-618">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="ee417-619">Podpora SNI vyžaduje:</span><span class="sxs-lookup"><span data-stu-id="ee417-619">SNI support requires:</span></span>

* <span data-ttu-id="ee417-620">Běží v cílovém rozhraní `netcoreapp2.1` nebo novějším.</span><span class="sxs-lookup"><span data-stu-id="ee417-620">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="ee417-621">V `net461` systému nebo novějším je zpětné volání vyvoláno, `null` `name` ale je vždy.</span><span class="sxs-lookup"><span data-stu-id="ee417-621">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="ee417-622">`name` Je také`null` v případě, že klient v TLS handshake neposkytne parametr názvu hostitele.</span><span class="sxs-lookup"><span data-stu-id="ee417-622">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="ee417-623">Všechny weby běží na stejné instanci Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ee417-623">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="ee417-624">Kestrel nepodporuje sdílení IP adresy a portu napříč několika instancemi bez reverzního proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="ee417-624">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        });
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="ee417-625">Vytvoření vazby na soket TCP</span><span class="sxs-lookup"><span data-stu-id="ee417-625">Bind to a TCP socket</span></span>

<span data-ttu-id="ee417-626"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> Metoda se váže k soketu TCP a pomocí lambda možností povolíte konfiguraci certifikátu X. 509:</span><span class="sxs-lookup"><span data-stu-id="ee417-626">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="ee417-627">Příklad nakonfiguruje HTTPS pro koncový bod pomocí <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="ee417-627">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="ee417-628">Ke konfiguraci dalších nastavení Kestrel pro konkrétní koncové body použijte stejné rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="ee417-628">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="ee417-629">Vytvoření vazby na soket UNIX</span><span class="sxs-lookup"><span data-stu-id="ee417-629">Bind to a Unix socket</span></span>

<span data-ttu-id="ee417-630">Naslouchat na soketu <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> se systémem UNIX, aby se zlepšil výkon pomocí Nginx, jak je znázorněno v tomto příkladu:</span><span class="sxs-lookup"><span data-stu-id="ee417-630">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

### <a name="port-0"></a><span data-ttu-id="ee417-631">Port 0</span><span class="sxs-lookup"><span data-stu-id="ee417-631">Port 0</span></span>

<span data-ttu-id="ee417-632">Po zadání čísla `0` portu se Kestrel dynamicky váže k dostupnému portu.</span><span class="sxs-lookup"><span data-stu-id="ee417-632">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="ee417-633">Následující příklad ukazuje, jak určit, který port Kestrel je skutečně vázaný za běhu:</span><span class="sxs-lookup"><span data-stu-id="ee417-633">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="ee417-634">Když se aplikace spustí, výstup okna konzoly indikuje dynamický port, na který se aplikace dá získat:</span><span class="sxs-lookup"><span data-stu-id="ee417-634">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="ee417-635">Omezení</span><span class="sxs-lookup"><span data-stu-id="ee417-635">Limitations</span></span>

<span data-ttu-id="ee417-636">Konfigurace koncových bodů pomocí následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="ee417-636">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="ee417-637">`--urls`argument příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="ee417-637">`--urls` command-line argument</span></span>
* <span data-ttu-id="ee417-638">`urls`konfigurační klíč hostitele</span><span class="sxs-lookup"><span data-stu-id="ee417-638">`urls` host configuration key</span></span>
* <span data-ttu-id="ee417-639">`ASPNETCORE_URLS`Proměnná prostředí</span><span class="sxs-lookup"><span data-stu-id="ee417-639">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="ee417-640">Tyto metody jsou užitečné, pokud chcete, aby kód pracoval na jiných serverech než Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ee417-640">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="ee417-641">Mějte ale na paměti následující omezení:</span><span class="sxs-lookup"><span data-stu-id="ee417-641">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="ee417-642">Protokol HTTPS nelze s těmito přístupy použít, pokud není k dispozici výchozí certifikát v konfiguraci koncového bodu HTTPS (například `KestrelServerOptions` pomocí konfigurace nebo konfiguračního souboru, jak je uvedeno výše v tomto tématu).</span><span class="sxs-lookup"><span data-stu-id="ee417-642">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="ee417-643">Při současném `Listen` `UseUrls` použitíobou`UseUrls` přístupů a koncovýchbodůsekoncovébodypřepisují.`Listen`</span><span class="sxs-lookup"><span data-stu-id="ee417-643">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="ee417-644">Konfigurace koncového bodu služby IIS</span><span class="sxs-lookup"><span data-stu-id="ee417-644">IIS endpoint configuration</span></span>

<span data-ttu-id="ee417-645">Při použití služby IIS jsou vazby adresy URL pro přepsání vazby služby IIS nastaveny buď `Listen` nebo `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="ee417-645">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="ee417-646">Další informace najdete v tématu [ASP.NET Core modulu](xref:host-and-deploy/aspnet-core-module) .</span><span class="sxs-lookup"><span data-stu-id="ee417-646">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="ee417-647">ListenOptions. Protocols</span><span class="sxs-lookup"><span data-stu-id="ee417-647">ListenOptions.Protocols</span></span>

<span data-ttu-id="ee417-648">Vlastnost určuje protokoly HTTP (`HttpProtocols`) povolené pro koncový bod připojení nebo pro server. `Protocols`</span><span class="sxs-lookup"><span data-stu-id="ee417-648">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="ee417-649">Přiřaďte hodnotu `Protocols` vlastnosti `HttpProtocols` z výčtu.</span><span class="sxs-lookup"><span data-stu-id="ee417-649">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="ee417-650">`HttpProtocols`hodnota výčtu</span><span class="sxs-lookup"><span data-stu-id="ee417-650">`HttpProtocols` enum value</span></span> | <span data-ttu-id="ee417-651">Povolený protokol připojení</span><span class="sxs-lookup"><span data-stu-id="ee417-651">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="ee417-652">Pouze HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="ee417-652">HTTP/1.1 only.</span></span> <span data-ttu-id="ee417-653">Dá se použít s TLS nebo bez něj.</span><span class="sxs-lookup"><span data-stu-id="ee417-653">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="ee417-654">Pouze HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="ee417-654">HTTP/2 only.</span></span> <span data-ttu-id="ee417-655">Dá se použít bez TLS jenom v případě, že klient podporuje [předchozí režim znalostní báze](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="ee417-655">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="ee417-656">HTTP/1.1 a HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="ee417-656">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="ee417-657">HTTP/2 vyžaduje připojení TLS a [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) ; v opačném případě se připojení nastaví jako výchozí HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="ee417-657">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="ee417-658">Výchozí protokol HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="ee417-658">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="ee417-659">Omezení TLS pro HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="ee417-659">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="ee417-660">TLS verze 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="ee417-660">TLS version 1.2 or later</span></span>
* <span data-ttu-id="ee417-661">Opětovné vyjednávání je zakázané.</span><span class="sxs-lookup"><span data-stu-id="ee417-661">Renegotiation disabled</span></span>
* <span data-ttu-id="ee417-662">Komprese vypnuta</span><span class="sxs-lookup"><span data-stu-id="ee417-662">Compression disabled</span></span>
* <span data-ttu-id="ee417-663">Minimální velikosti dočasného výměny klíčů:</span><span class="sxs-lookup"><span data-stu-id="ee417-663">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="ee417-664">Eliptická křivka Diffie-Hellman (ECDH &lbrack;) [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; &ndash; 224 bity minima</span><span class="sxs-lookup"><span data-stu-id="ee417-664">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum</span></span>
  * <span data-ttu-id="ee417-665">Omezené pole Diffie- &lbrack; Hellman (DHE) `TLS12` &rbrack; &ndash; 2048 bity minima</span><span class="sxs-lookup"><span data-stu-id="ee417-665">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bits minimum</span></span>
* <span data-ttu-id="ee417-666">Šifrovací sada není zakázaná.</span><span class="sxs-lookup"><span data-stu-id="ee417-666">Cipher suite not blacklisted</span></span>

<span data-ttu-id="ee417-667">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack; `FIPS186` &rbrack; ve výchozím nastavení je podporována eliptická křivka P-256. &lbrack; `TLS-ECDHE` &rbrack;</span><span class="sxs-lookup"><span data-stu-id="ee417-667">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="ee417-668">Následující příklad povoluje připojení HTTP/1.1 a HTTP/2 na portu 8000.</span><span class="sxs-lookup"><span data-stu-id="ee417-668">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="ee417-669">Připojení TLS zabezpečuje pomocí zadaného certifikátu:</span><span class="sxs-lookup"><span data-stu-id="ee417-669">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="ee417-670">Volitelně můžete vytvořit `IConnectionAdapter` implementaci pro filtrování ověřování TLS metodou handshake na základě jednotlivých připojení pro konkrétní šifry:</span><span class="sxs-lookup"><span data-stu-id="ee417-670">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

```csharp
.ConfigureKestrel((context, serverOptions) =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.ConnectionAdapters.Add(new TlsFilterAdapter());
    });
});
```

```csharp
private class TlsFilterAdapter : IConnectionAdapter
{
    public bool IsHttps => false;

    public Task<IAdaptedConnection> OnConnectionAsync(ConnectionAdapterContext context)
    {
        var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

        // Throw NotSupportedException for any cipher algorithm that the app doesn't
        // wish to support. Alternatively, define and compare
        // ITlsHandshakeFeature.CipherAlgorithm to a list of acceptable cipher
        // suites.
        //
        // A ITlsHandshakeFeature.CipherAlgorithm of CipherAlgorithmType.Null
        // indicates that no cipher algorithm supported by Kestrel matches the
        // requested algorithm(s).
        if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
        {
            throw new NotSupportedException("Prohibited cipher: " + tlsFeature.CipherAlgorithm);
        }

        return Task.FromResult<IAdaptedConnection>(new AdaptedConnection(context.ConnectionStream));
    }

    private class AdaptedConnection : IAdaptedConnection
    {
        public AdaptedConnection(Stream adaptedStream)
        {
            ConnectionStream = adaptedStream;
        }

        public Stream ConnectionStream { get; }

        public void Dispose()
        {
        }
    }
}
```

<span data-ttu-id="ee417-671">*Nastavit protokol z konfigurace*</span><span class="sxs-lookup"><span data-stu-id="ee417-671">*Set the protocol from configuration*</span></span>

<span data-ttu-id="ee417-672"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>Při `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` načítání konfigurace Kestrel ve výchozím nastavení volá.</span><span class="sxs-lookup"><span data-stu-id="ee417-672"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="ee417-673">V následujícím příkladu *appSettings. JSON* se pro všechny koncové body Kestrel vytvoří výchozí protokol připojení (http/1.1 a HTTP/2):</span><span class="sxs-lookup"><span data-stu-id="ee417-673">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="ee417-674">Následující příklad konfiguračního souboru vytvoří protokol připojení pro konkrétní koncový bod:</span><span class="sxs-lookup"><span data-stu-id="ee417-674">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http1AndHttp2"
      }
    }
  }
}
```

<span data-ttu-id="ee417-675">Protokoly zadané v hodnotách přepisu kódu nastavených podle konfigurace</span><span class="sxs-lookup"><span data-stu-id="ee417-675">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="ee417-676">Konfigurace přenosu</span><span class="sxs-lookup"><span data-stu-id="ee417-676">Transport configuration</span></span>

<span data-ttu-id="ee417-677">S vydáním ASP.NET Core 2,1 není výchozí přenos Kestrel založen na Libuv, ale na základě spravovaných soketů.</span><span class="sxs-lookup"><span data-stu-id="ee417-677">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="ee417-678">Jedná se o zásadní změnu pro aplikace ASP.NET Core 2,0 upgradované na 2,1, <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> které volá a závisí na jednom z následujících balíčků:</span><span class="sxs-lookup"><span data-stu-id="ee417-678">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="ee417-679">[Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (přímý odkaz na balíček)</span><span class="sxs-lookup"><span data-stu-id="ee417-679">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="ee417-680">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="ee417-680">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="ee417-681">Pro projekty, které vyžadují použití Libuv:</span><span class="sxs-lookup"><span data-stu-id="ee417-681">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="ee417-682">Přidejte do souboru projektu aplikace závislost pro balíček [Microsoft. AspNetCore. Server. Kestrel. Transport. Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) :</span><span class="sxs-lookup"><span data-stu-id="ee417-682">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="ee417-683">Volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span><span class="sxs-lookup"><span data-stu-id="ee417-683">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a><span data-ttu-id="ee417-684">Předpony adresy URL</span><span class="sxs-lookup"><span data-stu-id="ee417-684">URL prefixes</span></span>

<span data-ttu-id="ee417-685">Při použití `UseUrls`, `--urls` argumentu příkazového řádku `urls` , konfiguračního klíče hostitele nebo `ASPNETCORE_URLS` proměnné prostředí můžou být předpony adresy URL v některém z následujících formátů.</span><span class="sxs-lookup"><span data-stu-id="ee417-685">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="ee417-686">Platné jsou pouze předpony adresy URL protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="ee417-686">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="ee417-687">Kestrel nepodporuje protokol HTTPS při konfiguraci vazeb URL pomocí `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="ee417-687">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="ee417-688">Adresa IPv4 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="ee417-688">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="ee417-689">`0.0.0.0`je zvláštní případ, který se váže ke všem adresám IPv4.</span><span class="sxs-lookup"><span data-stu-id="ee417-689">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="ee417-690">Adresa IPv6 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="ee417-690">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="ee417-691">`[::]`je ekvivalentem protokolu IPv4 `0.0.0.0`pro protokol IPv6.</span><span class="sxs-lookup"><span data-stu-id="ee417-691">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="ee417-692">Název hostitele s číslem portu</span><span class="sxs-lookup"><span data-stu-id="ee417-692">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="ee417-693">Názvy `*`hostitelů, a `+`nejsou speciální.</span><span class="sxs-lookup"><span data-stu-id="ee417-693">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="ee417-694">Cokoli se nerozpoznalo jako platná IP adresa `localhost` nebo se váže ke všem IP adresám IPv4 a IPv6.</span><span class="sxs-lookup"><span data-stu-id="ee417-694">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="ee417-695">Pokud chcete navazovat různé názvy hostitelů na různé ASP.NET Core aplikace na stejném portu, použijte [http. sys](xref:fundamentals/servers/httpsys) nebo reverzní proxy server, třeba IIS, Nginx nebo Apache.</span><span class="sxs-lookup"><span data-stu-id="ee417-695">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="ee417-696">Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="ee417-696">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="ee417-697">Název `localhost` hostitele s číslem portu nebo IP adresou zpětné smyčky s číslem portu</span><span class="sxs-lookup"><span data-stu-id="ee417-697">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="ee417-698">Když `localhost` je zadaný, Kestrel se pokusí vytvořit propojení s rozhraními zpětné smyčky IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="ee417-698">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="ee417-699">Pokud je požadovaný port používán jinou službou na rozhraní zpětné smyčky, spuštění Kestrel se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="ee417-699">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="ee417-700">Pokud je rozhraní zpětné smyčky v žádném jiném případě nedostupné (většinou kvůli tomu, že protokol IPv6 není podporovaný), Kestrel zaznamená upozornění.</span><span class="sxs-lookup"><span data-stu-id="ee417-700">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="ee417-701">Filtrování hostitele</span><span class="sxs-lookup"><span data-stu-id="ee417-701">Host filtering</span></span>

<span data-ttu-id="ee417-702">I když Kestrel podporuje konfiguraci na základě prefixů `http://example.com:5000`, jako je, Kestrel převážně ignoruje název hostitele.</span><span class="sxs-lookup"><span data-stu-id="ee417-702">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="ee417-703">Hostitel `localhost` je zvláštní případ, který se používá pro vazbu na adresy zpětné smyčky.</span><span class="sxs-lookup"><span data-stu-id="ee417-703">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="ee417-704">Každý hostitel, který je jiný než explicitní IP adresa, se váže na všechny veřejné IP adresy.</span><span class="sxs-lookup"><span data-stu-id="ee417-704">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="ee417-705">`Host`hlavičky nejsou ověřené.</span><span class="sxs-lookup"><span data-stu-id="ee417-705">`Host` headers aren't validated.</span></span>

<span data-ttu-id="ee417-706">Jako alternativní řešení použijte middleware pro filtrování hostitele.</span><span class="sxs-lookup"><span data-stu-id="ee417-706">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="ee417-707">Middleware pro filtrování hostitele poskytuje balíček [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , který je součástí [Microsoft. AspNetCore. App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 nebo 2,2).</span><span class="sxs-lookup"><span data-stu-id="ee417-707">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="ee417-708">Middleware je přidána pomocí <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, který volá <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="ee417-708">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="ee417-709">Middleware pro filtrování hostitele je ve výchozím nastavení zakázáno.</span><span class="sxs-lookup"><span data-stu-id="ee417-709">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="ee417-710">Chcete-li povolit middleware, `AllowedHosts` Definujte klíč v souboru *appSettings. JSON*/ *\< appSettings. Environment >. JSON*.</span><span class="sxs-lookup"><span data-stu-id="ee417-710">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="ee417-711">Hodnota je seznam názvů hostitelů oddělených středníkem bez čísel portů:</span><span class="sxs-lookup"><span data-stu-id="ee417-711">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="ee417-712">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="ee417-712">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="ee417-713">[Middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer) má <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> také možnost.</span><span class="sxs-lookup"><span data-stu-id="ee417-713">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="ee417-714">Middleware předávaných hlaviček a middleware pro filtrování hostitele mají podobné funkce pro různé scénáře.</span><span class="sxs-lookup"><span data-stu-id="ee417-714">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="ee417-715">Nastavení `AllowedHosts` pomocí předávaných middlewarových hlaviček je vhodné `Host` v případě, že hlavička není zachována při předávání požadavků pomocí reverzního proxy server nebo nástroje pro vyrovnávání zatížení.</span><span class="sxs-lookup"><span data-stu-id="ee417-715">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="ee417-716">Nastavení `AllowedHosts` pomocí middlewaru pro filtrování hostitele je vhodné, když se Kestrel používá jako veřejný hraniční Server nebo `Host` když se hlavička přímo přepošle.</span><span class="sxs-lookup"><span data-stu-id="ee417-716">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="ee417-717">Další informace o middlewaru předávaných hlaviček najdete <xref:host-and-deploy/proxy-load-balancer>v tématu.</span><span class="sxs-lookup"><span data-stu-id="ee417-717">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="ee417-718">Kestrel je [webový server pro různé platformy pro ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="ee417-718">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="ee417-719">Kestrel je webový server, který je ve výchozím nastavení součástí ASP.NET Core šablon projektů.</span><span class="sxs-lookup"><span data-stu-id="ee417-719">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="ee417-720">Kestrel podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="ee417-720">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="ee417-721">HTTPS</span><span class="sxs-lookup"><span data-stu-id="ee417-721">HTTPS</span></span>
* <span data-ttu-id="ee417-722">Neprůhledný upgrade používaný k povolení [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="ee417-722">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="ee417-723">Technologie UNIX Sockets s vysokým výkonem za Nginx</span><span class="sxs-lookup"><span data-stu-id="ee417-723">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="ee417-724">Kestrel se podporuje na všech platformách a verzích, které podporuje .NET Core.</span><span class="sxs-lookup"><span data-stu-id="ee417-724">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="ee417-725">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="ee417-725">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="ee417-726">Kdy použít Kestrel s reverzním proxy serverem</span><span class="sxs-lookup"><span data-stu-id="ee417-726">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="ee417-727">Kestrel může používat sám sebe nebo s *reverzními proxy server*, jako je [Internetová informační služba (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="ee417-727">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="ee417-728">Reverzní proxy server přijímá požadavky HTTP ze sítě a přesměruje je na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ee417-728">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="ee417-729">Kestrel použitý jako hraniční webový server (internetový):</span><span class="sxs-lookup"><span data-stu-id="ee417-729">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel komunikuje přímo s internetem bez zpětného proxy server](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="ee417-731">Kestrel se používá v konfiguraci reverzního proxy serveru:</span><span class="sxs-lookup"><span data-stu-id="ee417-731">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel nepřímo komunikuje s internetem prostřednictvím reverzní proxy server, jako je IIS, Nginx nebo Apache.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="ee417-733">Buď konfigurace, s zpětným proxy server nebo bez něj, je podporovaná konfigurace hostování.</span><span class="sxs-lookup"><span data-stu-id="ee417-733">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="ee417-734">Kestrel používané jako hraniční Server bez reverzních proxy server nepodporuje sdílení stejné IP adresy a portu mezi více procesy.</span><span class="sxs-lookup"><span data-stu-id="ee417-734">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="ee417-735">Když je Kestrel nakonfigurovaný k naslouchání na portu, Kestrel zpracovává veškerý provoz tohoto portu bez ohledu na `Host` hlavičky požadavků.</span><span class="sxs-lookup"><span data-stu-id="ee417-735">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="ee417-736">Reverzní proxy server, který může sdílet porty, má schopnost přesměrovat požadavky na Kestrel na jedinečné IP adresy a porty.</span><span class="sxs-lookup"><span data-stu-id="ee417-736">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="ee417-737">I v případě, že se zpětný proxy server nepožaduje, může být použití reverzní proxy server vhodnou volbou.</span><span class="sxs-lookup"><span data-stu-id="ee417-737">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="ee417-738">Reverzní proxy:</span><span class="sxs-lookup"><span data-stu-id="ee417-738">A reverse proxy:</span></span>

* <span data-ttu-id="ee417-739">Může omezit vystavenou veřejnou plochu v aplikacích, které hostuje.</span><span class="sxs-lookup"><span data-stu-id="ee417-739">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="ee417-740">Poskytněte další vrstvu konfigurace a obrany.</span><span class="sxs-lookup"><span data-stu-id="ee417-740">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="ee417-741">Může se lépe integrovat se stávající infrastrukturou.</span><span class="sxs-lookup"><span data-stu-id="ee417-741">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="ee417-742">Zjednodušte vyrovnávání zatížení a konfiguraci zabezpečené komunikace (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="ee417-742">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="ee417-743">Pouze reverzní proxy server vyžaduje certifikát X. 509 a tento server může komunikovat se servery aplikace v interní síti pomocí prostého protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="ee417-743">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="ee417-744">Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="ee417-744">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="ee417-745">Použití Kestrel v aplikacích ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="ee417-745">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="ee417-746">Balíček [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) je součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="ee417-746">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="ee417-747">ASP.NET Core šablony projektu ve výchozím nastavení používají Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ee417-747">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="ee417-748">V *program.cs*kód šablony volá <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, který volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> na pozadí.</span><span class="sxs-lookup"><span data-stu-id="ee417-748">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="ee417-749">Chcete-li po volání `CreateDefaultBuilder`poskytnout další konfiguraci, zavolejte: <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*></span><span class="sxs-lookup"><span data-stu-id="ee417-749">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

## <a name="kestrel-options"></a><span data-ttu-id="ee417-750">Kestrel možnosti</span><span class="sxs-lookup"><span data-stu-id="ee417-750">Kestrel options</span></span>

<span data-ttu-id="ee417-751">Webový server Kestrel má možnosti konfigurace omezení, které jsou zvláště užitečné v internetových nasazeních.</span><span class="sxs-lookup"><span data-stu-id="ee417-751">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="ee417-752">Nastavte omezení pro <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> vlastnost <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> třídy.</span><span class="sxs-lookup"><span data-stu-id="ee417-752">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="ee417-753">Vlastnost obsahuje instanci <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>třídy. `Limits`</span><span class="sxs-lookup"><span data-stu-id="ee417-753">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="ee417-754">V následujících příkladech se <xref:Microsoft.AspNetCore.Server.Kestrel.Core> používá obor názvů:</span><span class="sxs-lookup"><span data-stu-id="ee417-754">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

### <a name="keep-alive-timeout"></a><span data-ttu-id="ee417-755">Časový limit Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="ee417-755">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="ee417-756">Získá nebo nastaví [časový limit zachování](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="ee417-756">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="ee417-757">Výchozí hodnota je 2 minuty.</span><span class="sxs-lookup"><span data-stu-id="ee417-757">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="ee417-758">Maximální počet připojení klientů</span><span class="sxs-lookup"><span data-stu-id="ee417-758">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="ee417-759">Maximální počet souběžných otevřených připojení TCP lze pro celou aplikaci nastavit pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="ee417-759">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="ee417-760">Existuje zvláštní omezení pro připojení, která byla upgradována z protokolu HTTP nebo HTTPS na jiný protokol (například na žádost WebSockets).</span><span class="sxs-lookup"><span data-stu-id="ee417-760">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="ee417-761">Po upgradu připojení se nepočítá na `MaxConcurrentConnections` základě limitu.</span><span class="sxs-lookup"><span data-stu-id="ee417-761">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="ee417-762">Ve výchozím nastavení je maximální počet připojení neomezené (null).</span><span class="sxs-lookup"><span data-stu-id="ee417-762">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="ee417-763">Maximální velikost textu požadavku</span><span class="sxs-lookup"><span data-stu-id="ee417-763">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="ee417-764">Výchozí maximální velikost textu požadavku je 30 000 000 bajtů, což je přibližně 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="ee417-764">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="ee417-765">Doporučený postup pro přepsání limitu v aplikaci ASP.NET Core MVC je použití <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> atributu pro metodu akce:</span><span class="sxs-lookup"><span data-stu-id="ee417-765">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="ee417-766">Tady je příklad, který ukazuje, jak nakonfigurovat omezení pro aplikaci na každém požadavku:</span><span class="sxs-lookup"><span data-stu-id="ee417-766">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="ee417-767">Přepsat nastavení u konkrétního požadavku v middleware:</span><span class="sxs-lookup"><span data-stu-id="ee417-767">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="ee417-768">Výjimka je vyvolána, pokud aplikace po zahájení aplikace nakonfiguruje limit na žádost.</span><span class="sxs-lookup"><span data-stu-id="ee417-768">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="ee417-769">Existuje vlastnost, která určuje, `MaxRequestBodySize` zda je vlastnost ve stavu jen pro čtení, což znamená, že je příliš pozdě pro konfiguraci limitu. `IsReadOnly`</span><span class="sxs-lookup"><span data-stu-id="ee417-769">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="ee417-770">Když je aplikace spouštěna [mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za [ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module), je omezení velikosti textu požadavku Kestrel zakázané, protože služba IIS tento limit již nastavuje.</span><span class="sxs-lookup"><span data-stu-id="ee417-770">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="ee417-771">Minimální rychlost těla zprávy požadavku</span><span class="sxs-lookup"><span data-stu-id="ee417-771">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="ee417-772">Kestrel kontroluje každou sekundu, pokud data přicházejí za zadanou rychlost v bajtech za sekundu.</span><span class="sxs-lookup"><span data-stu-id="ee417-772">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="ee417-773">Pokud frekvence klesne pod minimální hodnotu, vypršel časový limit připojení. Doba odkladu je doba, kterou Kestrel umožňuje klientovi zvýšit rychlost odesílání až na minimum; frekvence není během této doby kontrolována.</span><span class="sxs-lookup"><span data-stu-id="ee417-773">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="ee417-774">Doba odkladu pomáhá vyhnout se vyřazení připojení, která při počátečním posílání dat zpomalují rychlost, kvůli pomalému spuštění protokolu TCP.</span><span class="sxs-lookup"><span data-stu-id="ee417-774">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="ee417-775">Výchozí minimální sazba je 240 bajtů za sekundu s obdobím 5 sekund odkladu.</span><span class="sxs-lookup"><span data-stu-id="ee417-775">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="ee417-776">Pro odpověď platí také minimální sazba.</span><span class="sxs-lookup"><span data-stu-id="ee417-776">A minimum rate also applies to the response.</span></span> <span data-ttu-id="ee417-777">Kód pro nastavení limitu požadavku a limit odezvy je stejný, s výjimkou `RequestBody` , že existují nebo `Response` v názvech vlastností a rozhraní.</span><span class="sxs-lookup"><span data-stu-id="ee417-777">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="ee417-778">Tady je příklad, který ukazuje, jak nakonfigurovat minimální datové tarify v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="ee417-778">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MinRequestBodyDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
            serverOptions.Limits.MinResponseDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
        });
```

### <a name="request-headers-timeout"></a><span data-ttu-id="ee417-779">Časový limit hlaviček žádostí</span><span class="sxs-lookup"><span data-stu-id="ee417-779">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="ee417-780">Získá nebo nastaví maximální dobu, po kterou server stráví příjem hlaviček požadavků.</span><span class="sxs-lookup"><span data-stu-id="ee417-780">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="ee417-781">Výchozí hodnota je 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="ee417-781">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="ee417-782">Synchronní v/v</span><span class="sxs-lookup"><span data-stu-id="ee417-782">Synchronous IO</span></span>

<span data-ttu-id="ee417-783"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>Určuje, zda je pro požadavek a odpověď povolena synchronní v/v.</span><span class="sxs-lookup"><span data-stu-id="ee417-783"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="ee417-784">Výchozí hodnota je `true`.</span><span class="sxs-lookup"><span data-stu-id="ee417-784">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="ee417-785">Velký počet blokování synchronních vstupně-výstupních operací může vést ke vyčerpání fondu vláken, což aplikaci nereaguje.</span><span class="sxs-lookup"><span data-stu-id="ee417-785">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="ee417-786">Povolit `AllowSynchronousIO` pouze při použití knihovny, která nepodporuje asynchronní v/v.</span><span class="sxs-lookup"><span data-stu-id="ee417-786">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="ee417-787">Následující příklad zakáže synchronní v/v:</span><span class="sxs-lookup"><span data-stu-id="ee417-787">The following example disables synchronous IO:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="ee417-788">Informace o dalších možnostech a omezeních Kestrel najdete v tématech:</span><span class="sxs-lookup"><span data-stu-id="ee417-788">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="ee417-789">Konfigurace koncového bodu</span><span class="sxs-lookup"><span data-stu-id="ee417-789">Endpoint configuration</span></span>

<span data-ttu-id="ee417-790">Ve výchozím nastavení ASP.NET Core váže k:</span><span class="sxs-lookup"><span data-stu-id="ee417-790">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="ee417-791">`https://localhost:5001`(Pokud je k dispozici místní vývojový certifikát)</span><span class="sxs-lookup"><span data-stu-id="ee417-791">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="ee417-792">Zadejte adresy URL pomocí:</span><span class="sxs-lookup"><span data-stu-id="ee417-792">Specify URLs using the:</span></span>

* <span data-ttu-id="ee417-793">`ASPNETCORE_URLS` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="ee417-793">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="ee417-794">`--urls`argument příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="ee417-794">`--urls` command-line argument.</span></span>
* <span data-ttu-id="ee417-795">`urls`konfigurační klíč hostitele.</span><span class="sxs-lookup"><span data-stu-id="ee417-795">`urls` host configuration key.</span></span>
* <span data-ttu-id="ee417-796">`UseUrls`Metoda rozšíření</span><span class="sxs-lookup"><span data-stu-id="ee417-796">`UseUrls` extension method.</span></span>

<span data-ttu-id="ee417-797">Hodnota zadaná pomocí těchto přístupů může být jeden nebo více koncových bodů HTTP a HTTPS (HTTPS, pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="ee417-797">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="ee417-798">Nakonfigurujte hodnotu jako seznam oddělený středníkem (například `"Urls": "http://localhost:8000; http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="ee417-798">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="ee417-799">Další informace o těchto přístupůch najdete v tématu [adresy URL serveru](xref:fundamentals/host/web-host#server-urls) a [potlačení konfigurace](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="ee417-799">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="ee417-800">Vytvoří se vývojový certifikát:</span><span class="sxs-lookup"><span data-stu-id="ee417-800">A development certificate is created:</span></span>

* <span data-ttu-id="ee417-801">Po instalaci [.NET Core SDK](/dotnet/core/sdk) .</span><span class="sxs-lookup"><span data-stu-id="ee417-801">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="ee417-802">[Nástroj pro vývoj a certifikáty](xref:aspnetcore-2.1#https) slouží k vytvoření certifikátu.</span><span class="sxs-lookup"><span data-stu-id="ee417-802">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="ee417-803">Některé prohlížeče vyžadují udělení explicitního oprávnění pro důvěřování místního vývojového certifikátu.</span><span class="sxs-lookup"><span data-stu-id="ee417-803">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="ee417-804">Šablony projektů konfigurují aplikace tak, aby běžely ve výchozím nastavení HTTPS a zahrnovaly [přesměrování https a podporu HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="ee417-804">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="ee417-805">Volání <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> nebo <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> metody<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> pro konfiguraci předpon adres URL a portů pro Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ee417-805">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="ee417-806">`UseUrls`, argument `urls` `ASPNETCORE_URLS` příkazového řádku, konfigurační klíč hostitele a proměnná prostředí fungují také, ale mají omezení zaznamenaná později v této části (pro koncový bod HTTPS musí být dostupný výchozí certifikát.) `--urls` konfigurace).</span><span class="sxs-lookup"><span data-stu-id="ee417-806">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="ee417-807">`KestrelServerOptions`rozšířeného</span><span class="sxs-lookup"><span data-stu-id="ee417-807">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="ee417-808">ConfigureEndpointDefaults (akce\<ListenOptions >)</span><span class="sxs-lookup"><span data-stu-id="ee417-808">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="ee417-809">Určuje konfiguraci `Action` , která se spustí pro každý zadaný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="ee417-809">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="ee417-810">Volání `ConfigureEndpointDefaults` několikrát nahrazuje předchozí `Action` `Action` zadanou parametr s.</span><span class="sxs-lookup"><span data-stu-id="ee417-810">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
        });
```

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="ee417-811">ConfigureHttpsDefaults (akce\<HttpsConnectionAdapterOptions >)</span><span class="sxs-lookup"><span data-stu-id="ee417-811">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="ee417-812">Určuje konfiguraci `Action` , která se má spustit pro každý koncový bod HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ee417-812">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="ee417-813">Volání `ConfigureHttpsDefaults` několikrát nahrazuje předchozí `Action` `Action` zadanou parametr s.</span><span class="sxs-lookup"><span data-stu-id="ee417-813">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                // certificate is an X509Certificate2
                listenOptions.ServerCertificate = certificate;
            });
        });
```

### <a name="configureiconfiguration"></a><span data-ttu-id="ee417-814">Konfigurace (IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="ee417-814">Configure(IConfiguration)</span></span>

<span data-ttu-id="ee417-815">Vytvoří zavaděč konfigurace pro nastavení Kestrel, který přebírá <xref:Microsoft.Extensions.Configuration.IConfiguration> jako vstup.</span><span class="sxs-lookup"><span data-stu-id="ee417-815">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="ee417-816">Konfigurace musí být vymezená na konfigurační oddíl pro Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ee417-816">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="ee417-817">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="ee417-817">ListenOptions.UseHttps</span></span>

<span data-ttu-id="ee417-818">Nakonfigurujte Kestrel tak, aby používal protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ee417-818">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="ee417-819">`ListenOptions.UseHttps`SND</span><span class="sxs-lookup"><span data-stu-id="ee417-819">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="ee417-820">`UseHttps`&ndash; Nakonfigurujte Kestrel tak, aby používal HTTPS s výchozím certifikátem.</span><span class="sxs-lookup"><span data-stu-id="ee417-820">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="ee417-821">Vyvolá výjimku, pokud není nakonfigurován žádný výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="ee417-821">Throws an exception if no default certificate is configured.</span></span>
* `UseHttps(string fileName)`
* `UseHttps(string fileName, string password)`
* `UseHttps(string fileName, string password, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(StoreName storeName, string subject)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location)`
* `UseHttps(StoreName storeName, string subject, bool allowInvalid, StoreLocation location, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(X509Certificate2 serverCertificate)`
* `UseHttps(X509Certificate2 serverCertificate, Action<HttpsConnectionAdapterOptions> configureOptions)`
* `UseHttps(Action<HttpsConnectionAdapterOptions> configureOptions)`

<span data-ttu-id="ee417-822">`ListenOptions.UseHttps`ukazatelů</span><span class="sxs-lookup"><span data-stu-id="ee417-822">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="ee417-823">`filename`je cesta a název souboru certifikátu vzhledem k adresáři, který obsahuje soubory obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="ee417-823">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="ee417-824">`password`je vyžadováno heslo pro přístup k datům certifikátu X. 509.</span><span class="sxs-lookup"><span data-stu-id="ee417-824">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="ee417-825">`configureOptions``Action` je ke`HttpsConnectionAdapterOptions`konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="ee417-825">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="ee417-826">`ListenOptions`Vrátí.</span><span class="sxs-lookup"><span data-stu-id="ee417-826">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="ee417-827">`storeName`je úložiště certifikátů, ze kterého se má certifikát načíst.</span><span class="sxs-lookup"><span data-stu-id="ee417-827">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="ee417-828">`subject`je název subjektu certifikátu.</span><span class="sxs-lookup"><span data-stu-id="ee417-828">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="ee417-829">`allowInvalid`Určuje, zda by měly být zváženy neplatné certifikáty, například certifikáty podepsané svým držitelem.</span><span class="sxs-lookup"><span data-stu-id="ee417-829">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="ee417-830">`location`je umístěním úložiště, ze kterého se má certifikát načíst.</span><span class="sxs-lookup"><span data-stu-id="ee417-830">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="ee417-831">`serverCertificate`je certifikát X. 509.</span><span class="sxs-lookup"><span data-stu-id="ee417-831">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="ee417-832">V produkčním prostředí musí být HTTPS explicitně nakonfigurovaný.</span><span class="sxs-lookup"><span data-stu-id="ee417-832">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="ee417-833">Musí být zadán minimálně výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="ee417-833">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="ee417-834">Podporované konfigurace popsané dál:</span><span class="sxs-lookup"><span data-stu-id="ee417-834">Supported configurations described next:</span></span>

* <span data-ttu-id="ee417-835">Žádná konfigurace</span><span class="sxs-lookup"><span data-stu-id="ee417-835">No configuration</span></span>
* <span data-ttu-id="ee417-836">Nahradit výchozí certifikát z konfigurace</span><span class="sxs-lookup"><span data-stu-id="ee417-836">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="ee417-837">Změna výchozích hodnot v kódu</span><span class="sxs-lookup"><span data-stu-id="ee417-837">Change the defaults in code</span></span>

<span data-ttu-id="ee417-838">*Žádná konfigurace*</span><span class="sxs-lookup"><span data-stu-id="ee417-838">*No configuration*</span></span>

<span data-ttu-id="ee417-839">Kestrel naslouchá na `http://localhost:5000` a `https://localhost:5001` (Pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="ee417-839">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="ee417-840">*Nahradit výchozí certifikát z konfigurace*</span><span class="sxs-lookup"><span data-stu-id="ee417-840">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="ee417-841">`CreateDefaultBuilder`Při `Configure(context.Configuration.GetSection("Kestrel"))` načítání konfigurace Kestrel ve výchozím nastavení volá.</span><span class="sxs-lookup"><span data-stu-id="ee417-841">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="ee417-842">Pro Kestrel je k dispozici výchozí schéma konfigurace nastavení aplikace HTTPS.</span><span class="sxs-lookup"><span data-stu-id="ee417-842">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="ee417-843">Nakonfigurujte několik koncových bodů, včetně adres URL a certifikátů, které se mají použít, buď ze souboru na disku, nebo z úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="ee417-843">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="ee417-844">V následujícím příkladu *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="ee417-844">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="ee417-845">Nastavte **AllowInvalid** na `true` , aby povolovala použití neplatných certifikátů (například certifikátů podepsaných svým držitelem).</span><span class="sxs-lookup"><span data-stu-id="ee417-845">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="ee417-846">Libovolný koncový bod HTTPS, který neurčuje certifikát (**HttpsDefaultCert** v následujícím příkladu), se vrátí k certifikátu definovanému v části **certifikáty** > **výchozí** nebo vývojový certifikát.</span><span class="sxs-lookup"><span data-stu-id="ee417-846">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },

      "HttpsInlineCertFile": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      },

      "HttpsInlineCertStore": {
        "Url": "https://localhost:5002",
        "Certificate": {
          "Subject": "<subject; required>",
          "Store": "<certificate store; required>",
          "Location": "<location; defaults to CurrentUser>",
          "AllowInvalid": "<true or false; defaults to false>"
        }
      },

      "HttpsDefaultCert": {
        "Url": "https://localhost:5003"
      },

      "Https": {
        "Url": "https://*:5004",
        "Certificate": {
          "Path": "<path to .pfx file>",
          "Password": "<certificate password>"
        }
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="ee417-847">Alternativou k použití **cesty** a **hesla** pro libovolný uzel certifikátu je zadání certifikátu pomocí polí úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="ee417-847">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="ee417-848">Například > **výchozí** certifikát může být zadán jako:</span><span class="sxs-lookup"><span data-stu-id="ee417-848">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="ee417-849">Poznámky ke schématu:</span><span class="sxs-lookup"><span data-stu-id="ee417-849">Schema notes:</span></span>

* <span data-ttu-id="ee417-850">V názvech koncových bodů se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="ee417-850">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="ee417-851">Například `HTTPS` a`Https` jsou platné.</span><span class="sxs-lookup"><span data-stu-id="ee417-851">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="ee417-852">U každého koncového bodu je vyžadován parametr.`Url`</span><span class="sxs-lookup"><span data-stu-id="ee417-852">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="ee417-853">Formát pro tento parametr je stejný jako konfigurační parametr nejvyšší úrovně `Urls` s tím rozdílem, že je omezen na jedinou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="ee417-853">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="ee417-854">Tyto koncové body nahrazují hodnoty definované v konfiguraci nejvyšší úrovně `Urls` místo jejich přidání do těchto koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="ee417-854">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="ee417-855">Koncové body definované v kódu `Listen` prostřednictvím jsou kumulativní pomocí koncových bodů definovaných v konfiguračním oddílu.</span><span class="sxs-lookup"><span data-stu-id="ee417-855">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="ee417-856">`Certificate` Oddíl je nepovinný.</span><span class="sxs-lookup"><span data-stu-id="ee417-856">The `Certificate` section is optional.</span></span> <span data-ttu-id="ee417-857">Pokud není `Certificate` oddíl zadaný, použijí se výchozí hodnoty definované v dřívějších scénářích.</span><span class="sxs-lookup"><span data-stu-id="ee417-857">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="ee417-858">Pokud nejsou k dispozici žádné výchozí hodnoty, Server vyvolá výjimku a nemůže se spustit.</span><span class="sxs-lookup"><span data-stu-id="ee417-858">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="ee417-859">&ndash;&ndash; Oddíl podporuje jak heslo cesty, tak certifikáty úložiště subjektu. `Certificate`</span><span class="sxs-lookup"><span data-stu-id="ee417-859">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="ee417-860">V takovém případě může být definován libovolný počet koncových bodů, pokud to nezpůsobí konflikty portů.</span><span class="sxs-lookup"><span data-stu-id="ee417-860">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="ee417-861">`options.Configure(context.Configuration.GetSection("{SECTION}"))``KestrelConfigurationLoader` vrátí metodu`.Endpoint(string name, listenOptions => { })` s metodou, kterou lze použít k doplnění nastavení nakonfigurovaného koncového bodu:</span><span class="sxs-lookup"><span data-stu-id="ee417-861">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
                .Endpoint("HTTPS", listenOptions =>
                {
                    listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
                });
        });
```

<span data-ttu-id="ee417-862">`KestrelServerOptions.ConfigurationLoader`k pokračování v iteraci u stávajícího zavaděče se dá získat přímý odkaz, jako je třeba <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>ten, který poskytuje.</span><span class="sxs-lookup"><span data-stu-id="ee417-862">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="ee417-863">Konfigurační oddíl pro každý koncový bod je k dispozici na možnostech v `Endpoint` metodě, aby bylo možné číst vlastní nastavení.</span><span class="sxs-lookup"><span data-stu-id="ee417-863">The configuration section for each endpoint is a available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="ee417-864">Více konfigurací může být načteno voláním `options.Configure(context.Configuration.GetSection("{SECTION}"))` znovu s jiným oddílem.</span><span class="sxs-lookup"><span data-stu-id="ee417-864">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="ee417-865">Je použita pouze poslední konfigurace, pokud `Load` není explicitně volána při předchozích instancích.</span><span class="sxs-lookup"><span data-stu-id="ee417-865">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="ee417-866">Metapackage nevolá `Load` , aby se jeho výchozí konfigurační oddíl mohl nahradit.</span><span class="sxs-lookup"><span data-stu-id="ee417-866">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="ee417-867">`KestrelConfigurationLoader`zrcadlí `KestrelServerOptions` `Endpoint` rodinu rozhraní API z as přetížení, takže koncové body kódu a konfigurace lze konfigurovat na stejném místě. `Listen`</span><span class="sxs-lookup"><span data-stu-id="ee417-867">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="ee417-868">Tato přetížení nepoužívají názvy a využívají pouze výchozí nastavení z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="ee417-868">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="ee417-869">*Změna výchozích hodnot v kódu*</span><span class="sxs-lookup"><span data-stu-id="ee417-869">*Change the defaults in code*</span></span>

<span data-ttu-id="ee417-870">`ConfigureEndpointDefaults`a `ConfigureHttpsDefaults` dá se použít ke změně výchozího nastavení pro `ListenOptions` a `HttpsConnectionAdapterOptions`, včetně přepsání výchozího certifikátu zadaného v předchozím scénáři.</span><span class="sxs-lookup"><span data-stu-id="ee417-870">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="ee417-871">`ConfigureEndpointDefaults`a `ConfigureHttpsDefaults` měla by být volána před konfigurací koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="ee417-871">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ConfigureEndpointDefaults(listenOptions =>
            {
                // Configure endpoint defaults
            });
            
            serverOptions.ConfigureHttpsDefaults(listenOptions =>
            {
                listenOptions.SslProtocols = SslProtocols.Tls12;
            });
        });
```

<span data-ttu-id="ee417-872">*Podpora Kestrel pro SNI*</span><span class="sxs-lookup"><span data-stu-id="ee417-872">*Kestrel support for SNI*</span></span>

<span data-ttu-id="ee417-873">[Indikace názvu serveru (SNI)](https://tools.ietf.org/html/rfc6066#section-3) lze použít k hostování více domén na stejné IP adrese a portu.</span><span class="sxs-lookup"><span data-stu-id="ee417-873">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="ee417-874">Aby SNI fungovalo, klient pošle název hostitele pro zabezpečenou relaci serveru během metody handshake TLS, aby server mohl poskytovat správný certifikát.</span><span class="sxs-lookup"><span data-stu-id="ee417-874">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="ee417-875">Klient používá poskytnutý certifikát pro šifrovanou komunikaci se serverem během zabezpečené relace, která následuje za metodou handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="ee417-875">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="ee417-876">Kestrel podporuje sni prostřednictvím `ServerCertificateSelector` zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="ee417-876">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="ee417-877">Zpětné volání je vyvoláno jednou za připojení, které aplikaci umožní zkontrolovat název hostitele a vybrat příslušný certifikát.</span><span class="sxs-lookup"><span data-stu-id="ee417-877">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="ee417-878">Podpora SNI vyžaduje:</span><span class="sxs-lookup"><span data-stu-id="ee417-878">SNI support requires:</span></span>

* <span data-ttu-id="ee417-879">Běží v cílovém rozhraní `netcoreapp2.1` nebo novějším.</span><span class="sxs-lookup"><span data-stu-id="ee417-879">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="ee417-880">V `net461` systému nebo novějším je zpětné volání vyvoláno, `null` `name` ale je vždy.</span><span class="sxs-lookup"><span data-stu-id="ee417-880">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="ee417-881">`name` Je také`null` v případě, že klient v TLS handshake neposkytne parametr názvu hostitele.</span><span class="sxs-lookup"><span data-stu-id="ee417-881">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="ee417-882">Všechny weby běží na stejné instanci Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ee417-882">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="ee417-883">Kestrel nepodporuje sdílení IP adresy a portu napříč několika instancemi bez reverzního proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="ee417-883">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, serverOptions) =>
        {
            serverOptions.ListenAnyIP(5005, listenOptions =>
            {
                listenOptions.UseHttps(httpsOptions =>
                {
                    var localhostCert = CertificateLoader.LoadFromStoreCert(
                        "localhost", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var exampleCert = CertificateLoader.LoadFromStoreCert(
                        "example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var subExampleCert = CertificateLoader.LoadFromStoreCert(
                        "sub.example.com", "My", StoreLocation.CurrentUser,
                        allowInvalid: true);
                    var certs = new Dictionary<string, X509Certificate2>(
                        StringComparer.OrdinalIgnoreCase);
                    certs["localhost"] = localhostCert;
                    certs["example.com"] = exampleCert;
                    certs["sub.example.com"] = subExampleCert;

                    httpsOptions.ServerCertificateSelector = (connectionContext, name) =>
                    {
                        if (name != null && certs.TryGetValue(name, out var cert))
                        {
                            return cert;
                        }

                        return exampleCert;
                    };
                });
            });
        })
        .Build();
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="ee417-884">Vytvoření vazby na soket TCP</span><span class="sxs-lookup"><span data-stu-id="ee417-884">Bind to a TCP socket</span></span>

<span data-ttu-id="ee417-885"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> Metoda se váže k soketu TCP a pomocí lambda možností povolíte konfiguraci certifikátu X. 509:</span><span class="sxs-lookup"><span data-stu-id="ee417-885">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Listen(IPAddress.Loopback, 5000);
            serverOptions.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

<span data-ttu-id="ee417-886">Příklad nakonfiguruje HTTPS pro koncový bod pomocí <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="ee417-886">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="ee417-887">Ke konfiguraci dalších nastavení Kestrel pro konkrétní koncové body použijte stejné rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="ee417-887">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="ee417-888">Vytvoření vazby na soket UNIX</span><span class="sxs-lookup"><span data-stu-id="ee417-888">Bind to a Unix socket</span></span>

<span data-ttu-id="ee417-889">Naslouchat na soketu <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> se systémem UNIX, aby se zlepšil výkon pomocí Nginx, jak je znázorněno v tomto příkladu:</span><span class="sxs-lookup"><span data-stu-id="ee417-889">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock");
            serverOptions.ListenUnixSocket("/tmp/kestrel-test.sock", listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testpassword");
            });
        });
```

### <a name="port-0"></a><span data-ttu-id="ee417-890">Port 0</span><span class="sxs-lookup"><span data-stu-id="ee417-890">Port 0</span></span>

<span data-ttu-id="ee417-891">Po zadání čísla `0` portu se Kestrel dynamicky váže k dostupnému portu.</span><span class="sxs-lookup"><span data-stu-id="ee417-891">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="ee417-892">Následující příklad ukazuje, jak určit, který port Kestrel je skutečně vázaný za běhu:</span><span class="sxs-lookup"><span data-stu-id="ee417-892">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="ee417-893">Když se aplikace spustí, výstup okna konzoly indikuje dynamický port, na který se aplikace dá získat:</span><span class="sxs-lookup"><span data-stu-id="ee417-893">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="ee417-894">Omezení</span><span class="sxs-lookup"><span data-stu-id="ee417-894">Limitations</span></span>

<span data-ttu-id="ee417-895">Konfigurace koncových bodů pomocí následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="ee417-895">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="ee417-896">`--urls`argument příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="ee417-896">`--urls` command-line argument</span></span>
* <span data-ttu-id="ee417-897">`urls`konfigurační klíč hostitele</span><span class="sxs-lookup"><span data-stu-id="ee417-897">`urls` host configuration key</span></span>
* <span data-ttu-id="ee417-898">`ASPNETCORE_URLS`Proměnná prostředí</span><span class="sxs-lookup"><span data-stu-id="ee417-898">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="ee417-899">Tyto metody jsou užitečné, pokud chcete, aby kód pracoval na jiných serverech než Kestrel.</span><span class="sxs-lookup"><span data-stu-id="ee417-899">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="ee417-900">Mějte ale na paměti následující omezení:</span><span class="sxs-lookup"><span data-stu-id="ee417-900">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="ee417-901">Protokol HTTPS nelze s těmito přístupy použít, pokud není k dispozici výchozí certifikát v konfiguraci koncového bodu HTTPS (například `KestrelServerOptions` pomocí konfigurace nebo konfiguračního souboru, jak je uvedeno výše v tomto tématu).</span><span class="sxs-lookup"><span data-stu-id="ee417-901">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="ee417-902">Při současném `Listen` `UseUrls` použitíobou`UseUrls` přístupů a koncovýchbodůsekoncovébodypřepisují.`Listen`</span><span class="sxs-lookup"><span data-stu-id="ee417-902">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="ee417-903">Konfigurace koncového bodu služby IIS</span><span class="sxs-lookup"><span data-stu-id="ee417-903">IIS endpoint configuration</span></span>

<span data-ttu-id="ee417-904">Při použití služby IIS jsou vazby adresy URL pro přepsání vazby služby IIS nastaveny buď `Listen` nebo `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="ee417-904">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="ee417-905">Další informace najdete v tématu [ASP.NET Core modulu](xref:host-and-deploy/aspnet-core-module) .</span><span class="sxs-lookup"><span data-stu-id="ee417-905">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="ee417-906">Konfigurace přenosu</span><span class="sxs-lookup"><span data-stu-id="ee417-906">Transport configuration</span></span>

<span data-ttu-id="ee417-907">S vydáním ASP.NET Core 2,1 není výchozí přenos Kestrel založen na Libuv, ale na základě spravovaných soketů.</span><span class="sxs-lookup"><span data-stu-id="ee417-907">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="ee417-908">Jedná se o zásadní změnu pro aplikace ASP.NET Core 2,0 upgradované na 2,1, <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> které volá a závisí na jednom z následujících balíčků:</span><span class="sxs-lookup"><span data-stu-id="ee417-908">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="ee417-909">[Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (přímý odkaz na balíček)</span><span class="sxs-lookup"><span data-stu-id="ee417-909">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="ee417-910">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="ee417-910">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="ee417-911">Pro projekty, které vyžadují použití Libuv:</span><span class="sxs-lookup"><span data-stu-id="ee417-911">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="ee417-912">Přidejte do souboru projektu aplikace závislost pro balíček [Microsoft. AspNetCore. Server. Kestrel. Transport. Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) :</span><span class="sxs-lookup"><span data-stu-id="ee417-912">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="ee417-913">Volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span><span class="sxs-lookup"><span data-stu-id="ee417-913">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

  ```csharp
  public class Program
  {
      public static void Main(string[] args)
      {
          CreateWebHostBuilder(args).Build().Run();
      }

      public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
          WebHost.CreateDefaultBuilder(args)
              .UseLibuv()
              .UseStartup<Startup>();
  }
  ```

### <a name="url-prefixes"></a><span data-ttu-id="ee417-914">Předpony adresy URL</span><span class="sxs-lookup"><span data-stu-id="ee417-914">URL prefixes</span></span>

<span data-ttu-id="ee417-915">Při použití `UseUrls`, `--urls` argumentu příkazového řádku `urls` , konfiguračního klíče hostitele nebo `ASPNETCORE_URLS` proměnné prostředí můžou být předpony adresy URL v některém z následujících formátů.</span><span class="sxs-lookup"><span data-stu-id="ee417-915">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="ee417-916">Platné jsou pouze předpony adresy URL protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="ee417-916">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="ee417-917">Kestrel nepodporuje protokol HTTPS při konfiguraci vazeb URL pomocí `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="ee417-917">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="ee417-918">Adresa IPv4 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="ee417-918">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="ee417-919">`0.0.0.0`je zvláštní případ, který se váže ke všem adresám IPv4.</span><span class="sxs-lookup"><span data-stu-id="ee417-919">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="ee417-920">Adresa IPv6 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="ee417-920">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="ee417-921">`[::]`je ekvivalentem protokolu IPv4 `0.0.0.0`pro protokol IPv6.</span><span class="sxs-lookup"><span data-stu-id="ee417-921">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="ee417-922">Název hostitele s číslem portu</span><span class="sxs-lookup"><span data-stu-id="ee417-922">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="ee417-923">Názvy `*`hostitelů, a `+`nejsou speciální.</span><span class="sxs-lookup"><span data-stu-id="ee417-923">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="ee417-924">Cokoli se nerozpoznalo jako platná IP adresa `localhost` nebo se váže ke všem IP adresám IPv4 a IPv6.</span><span class="sxs-lookup"><span data-stu-id="ee417-924">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="ee417-925">Pokud chcete navazovat různé názvy hostitelů na různé ASP.NET Core aplikace na stejném portu, použijte [http. sys](xref:fundamentals/servers/httpsys) nebo reverzní proxy server, třeba IIS, Nginx nebo Apache.</span><span class="sxs-lookup"><span data-stu-id="ee417-925">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="ee417-926">Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="ee417-926">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="ee417-927">Název `localhost` hostitele s číslem portu nebo IP adresou zpětné smyčky s číslem portu</span><span class="sxs-lookup"><span data-stu-id="ee417-927">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="ee417-928">Když `localhost` je zadaný, Kestrel se pokusí vytvořit propojení s rozhraními zpětné smyčky IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="ee417-928">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="ee417-929">Pokud je požadovaný port používán jinou službou na rozhraní zpětné smyčky, spuštění Kestrel se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="ee417-929">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="ee417-930">Pokud je rozhraní zpětné smyčky v žádném jiném případě nedostupné (většinou kvůli tomu, že protokol IPv6 není podporovaný), Kestrel zaznamená upozornění.</span><span class="sxs-lookup"><span data-stu-id="ee417-930">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="ee417-931">Filtrování hostitele</span><span class="sxs-lookup"><span data-stu-id="ee417-931">Host filtering</span></span>

<span data-ttu-id="ee417-932">I když Kestrel podporuje konfiguraci na základě prefixů `http://example.com:5000`, jako je, Kestrel převážně ignoruje název hostitele.</span><span class="sxs-lookup"><span data-stu-id="ee417-932">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="ee417-933">Hostitel `localhost` je zvláštní případ, který se používá pro vazbu na adresy zpětné smyčky.</span><span class="sxs-lookup"><span data-stu-id="ee417-933">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="ee417-934">Každý hostitel, který je jiný než explicitní IP adresa, se váže na všechny veřejné IP adresy.</span><span class="sxs-lookup"><span data-stu-id="ee417-934">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="ee417-935">`Host`hlavičky nejsou ověřené.</span><span class="sxs-lookup"><span data-stu-id="ee417-935">`Host` headers aren't validated.</span></span>

<span data-ttu-id="ee417-936">Jako alternativní řešení použijte middleware pro filtrování hostitele.</span><span class="sxs-lookup"><span data-stu-id="ee417-936">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="ee417-937">Middleware pro filtrování hostitele poskytuje balíček [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , který je součástí [Microsoft. AspNetCore. App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 nebo 2,2).</span><span class="sxs-lookup"><span data-stu-id="ee417-937">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="ee417-938">Middleware je přidána pomocí <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, který volá <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="ee417-938">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="ee417-939">Middleware pro filtrování hostitele je ve výchozím nastavení zakázáno.</span><span class="sxs-lookup"><span data-stu-id="ee417-939">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="ee417-940">Chcete-li povolit middleware, `AllowedHosts` Definujte klíč v souboru *appSettings. JSON*/ *\< appSettings. Environment >. JSON*.</span><span class="sxs-lookup"><span data-stu-id="ee417-940">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="ee417-941">Hodnota je seznam názvů hostitelů oddělených středníkem bez čísel portů:</span><span class="sxs-lookup"><span data-stu-id="ee417-941">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="ee417-942">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="ee417-942">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="ee417-943">[Middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer) má <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> také možnost.</span><span class="sxs-lookup"><span data-stu-id="ee417-943">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="ee417-944">Middleware předávaných hlaviček a middleware pro filtrování hostitele mají podobné funkce pro různé scénáře.</span><span class="sxs-lookup"><span data-stu-id="ee417-944">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="ee417-945">Nastavení `AllowedHosts` pomocí předávaných middlewarových hlaviček je vhodné `Host` v případě, že hlavička není zachována při předávání požadavků pomocí reverzního proxy server nebo nástroje pro vyrovnávání zatížení.</span><span class="sxs-lookup"><span data-stu-id="ee417-945">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="ee417-946">Nastavení `AllowedHosts` pomocí middlewaru pro filtrování hostitele je vhodné, když se Kestrel používá jako veřejný hraniční Server nebo `Host` když se hlavička přímo přepošle.</span><span class="sxs-lookup"><span data-stu-id="ee417-946">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="ee417-947">Další informace o middlewaru předávaných hlaviček najdete <xref:host-and-deploy/proxy-load-balancer>v tématu.</span><span class="sxs-lookup"><span data-stu-id="ee417-947">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="ee417-948">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="ee417-948">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="ee417-949">RFC 7230: Syntaxe zprávy a směrování (oddíl 5,4: Provoz</span><span class="sxs-lookup"><span data-stu-id="ee417-949">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
