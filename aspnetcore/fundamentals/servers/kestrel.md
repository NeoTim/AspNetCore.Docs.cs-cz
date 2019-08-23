---
title: Implementace webového serveru Kestrel v ASP.NET Core
author: guardrex
description: Přečtěte si o Kestrel, webovém serveru pro různé platformy pro ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/24/2019
uid: fundamentals/servers/kestrel
ms.openlocfilehash: 763f65ea26367e56c2ff1392eea51e62fc663ee6
ms.sourcegitcommit: 8835b6777682da6fb3becf9f9121c03f89dc7614
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69975529"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="42256-103">Implementace webového serveru Kestrel v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="42256-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="42256-104">Díky [Dykstra](https://github.com/tdykstra), [Novák Rossův](https://github.com/Tratcher)a zablokování [Stephen](https://twitter.com/halter73)</span><span class="sxs-lookup"><span data-stu-id="42256-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

<span data-ttu-id="42256-105">Kestrel je [webový server pro různé platformy pro ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="42256-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="42256-106">Kestrel je webový server, který je ve výchozím nastavení součástí ASP.NET Core šablon projektů.</span><span class="sxs-lookup"><span data-stu-id="42256-106">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="42256-107">Kestrel podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="42256-107">Kestrel supports the following scenarios:</span></span>

::: moniker range=">= aspnetcore-2.2"

* <span data-ttu-id="42256-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="42256-108">HTTPS</span></span>
* <span data-ttu-id="42256-109">Neprůhledný upgrade používaný [](https://github.com/aspnet/websockets) k povolení WebSockets</span><span class="sxs-lookup"><span data-stu-id="42256-109">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="42256-110">Technologie UNIX Sockets s vysokým výkonem za Nginx</span><span class="sxs-lookup"><span data-stu-id="42256-110">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="42256-111">HTTP/2 (kromě macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="42256-111">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="42256-112">&dagger;HTTP/2 se v budoucí verzi podporuje v macOS.</span><span class="sxs-lookup"><span data-stu-id="42256-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

* <span data-ttu-id="42256-113">HTTPS</span><span class="sxs-lookup"><span data-stu-id="42256-113">HTTPS</span></span>
* <span data-ttu-id="42256-114">Neprůhledný upgrade používaný [](https://github.com/aspnet/websockets) k povolení WebSockets</span><span class="sxs-lookup"><span data-stu-id="42256-114">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="42256-115">Technologie UNIX Sockets s vysokým výkonem za Nginx</span><span class="sxs-lookup"><span data-stu-id="42256-115">Unix sockets for high performance behind Nginx</span></span>

::: moniker-end

<span data-ttu-id="42256-116">Kestrel se podporuje na všech platformách a verzích, které podporuje .NET Core.</span><span class="sxs-lookup"><span data-stu-id="42256-116">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="42256-117">[Zobrazení nebo stažení ukázkového kódu](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([stažení](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="42256-117">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="http2-support"></a><span data-ttu-id="42256-118">Podpora HTTP/2</span><span class="sxs-lookup"><span data-stu-id="42256-118">HTTP/2 support</span></span>

<span data-ttu-id="42256-119">[Http/2](https://httpwg.org/specs/rfc7540.html) je k dispozici pro ASP.NET Core aplikace, pokud jsou splněné následující základní požadavky:</span><span class="sxs-lookup"><span data-stu-id="42256-119">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="42256-120">Operační systém&dagger;</span><span class="sxs-lookup"><span data-stu-id="42256-120">Operating system&dagger;</span></span>
  * <span data-ttu-id="42256-121">Windows Server 2016/Windows 10 nebo novější&Dagger;</span><span class="sxs-lookup"><span data-stu-id="42256-121">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="42256-122">Linux s OpenSSL 1.0.2 nebo novějším (například Ubuntu 16,04 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="42256-122">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="42256-123">Cílová architektura: .NET Core 2,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="42256-123">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="42256-124">Připojení [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="42256-124">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="42256-125">Protokol TLS 1.2 nebo vyšší připojení</span><span class="sxs-lookup"><span data-stu-id="42256-125">TLS 1.2 or later connection</span></span>

<span data-ttu-id="42256-126">&dagger;HTTP/2 se v budoucí verzi podporuje v macOS.</span><span class="sxs-lookup"><span data-stu-id="42256-126">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="42256-127">&Dagger;Kestrel má omezená podpora HTTP/2 na Windows Serveru 2012 R2 a Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="42256-127">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="42256-128">Podpora je omezená, protože seznam podporovaných šifrovacích sad TLS, které jsou k dispozici v těchto operačních systémech, je omezený.</span><span class="sxs-lookup"><span data-stu-id="42256-128">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="42256-129">Pro zabezpečení připojení TLS může být vyžadován certifikát vygenerovaný pomocí algoritmu ECDSA (s připojením typu eliptická křivka).</span><span class="sxs-lookup"><span data-stu-id="42256-129">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="42256-130">Pokud se připojení HTTP/2, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) sestavy `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="42256-130">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="42256-131">HTTP/2 je ve výchozím nastavení zakázané.</span><span class="sxs-lookup"><span data-stu-id="42256-131">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="42256-132">Další informace o konfiguraci najdete v částech [Možnosti Kestrel](#kestrel-options) a [ListenOptions. Protocols](#listenoptionsprotocols) .</span><span class="sxs-lookup"><span data-stu-id="42256-132">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

::: moniker-end

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="42256-133">Kdy použít Kestrel s reverzním proxy serverem</span><span class="sxs-lookup"><span data-stu-id="42256-133">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="42256-134">Kestrel může používat sám sebe nebo s reverzními *proxy server*, jako je [Internetová informační služba (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="42256-134">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="42256-135">Reverzní proxy server přijímá požadavky HTTP ze sítě a přesměruje je na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="42256-135">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="42256-136">Kestrel použitý jako hraniční webový server (internetový):</span><span class="sxs-lookup"><span data-stu-id="42256-136">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel komunikuje přímo s internetem bez zpětného proxy server](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="42256-138">Kestrel se používá v konfiguraci reverzního proxy serveru:</span><span class="sxs-lookup"><span data-stu-id="42256-138">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel nepřímo komunikuje s internetem prostřednictvím reverzní proxy server, jako je IIS, Nginx nebo Apache.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="42256-140">Buď konfigurace&mdash;s zpětným proxy serverm&mdash;, nebo bez něj, je podporovaná konfigurace hostování pro ASP.NET Core 2,1 nebo novější aplikace, které přijímají požadavky z Internetu.</span><span class="sxs-lookup"><span data-stu-id="42256-140">Either configuration&mdash;with or without a reverse proxy server&mdash;is a supported hosting configuration for ASP.NET Core 2.1 or later apps that receive requests from the Internet.</span></span>

<span data-ttu-id="42256-141">Kestrel používané jako hraniční Server bez reverzních proxy server nepodporuje sdílení stejné IP adresy a portu mezi více procesy.</span><span class="sxs-lookup"><span data-stu-id="42256-141">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="42256-142">Když je Kestrel nakonfigurovaný k naslouchání na portu, Kestrel zpracovává veškerý provoz tohoto portu bez ohledu na `Host` hlavičky požadavků.</span><span class="sxs-lookup"><span data-stu-id="42256-142">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="42256-143">Reverzní proxy server, který může sdílet porty, má schopnost přesměrovat požadavky na Kestrel na jedinečné IP adresy a porty.</span><span class="sxs-lookup"><span data-stu-id="42256-143">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="42256-144">I v případě, že se zpětný proxy server nepožaduje, může být použití reverzní proxy server vhodnou volbou.</span><span class="sxs-lookup"><span data-stu-id="42256-144">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="42256-145">Reverzní proxy:</span><span class="sxs-lookup"><span data-stu-id="42256-145">A reverse proxy:</span></span>

* <span data-ttu-id="42256-146">Může omezit vystavenou veřejnou plochu v aplikacích, které hostuje.</span><span class="sxs-lookup"><span data-stu-id="42256-146">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="42256-147">Poskytněte další vrstvu konfigurace a obrany.</span><span class="sxs-lookup"><span data-stu-id="42256-147">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="42256-148">Může se lépe integrovat se stávající infrastrukturou.</span><span class="sxs-lookup"><span data-stu-id="42256-148">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="42256-149">Zjednodušte vyrovnávání zatížení a konfiguraci zabezpečené komunikace (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="42256-149">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="42256-150">Pouze reverzní proxy server vyžaduje certifikát X. 509 a tento server může komunikovat s aplikačními servery v interní síti pomocí prostého protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="42256-150">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with your app servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="42256-151">Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="42256-151">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="42256-152">Použití Kestrel v aplikacích ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="42256-152">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="42256-153">Balíček [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) je součástí [Microsoft. AspNetCore. App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 nebo novější).</span><span class="sxs-lookup"><span data-stu-id="42256-153">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span>

<span data-ttu-id="42256-154">ASP.NET Core šablony projektu ve výchozím nastavení používají Kestrel.</span><span class="sxs-lookup"><span data-stu-id="42256-154">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="42256-155">V *program.cs*kód šablony volá <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, který volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> na pozadí.</span><span class="sxs-lookup"><span data-stu-id="42256-155">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="42256-156">Chcete-li po volání `CreateDefaultBuilder`poskytnout další konfiguraci, použijte: `ConfigureKestrel`</span><span class="sxs-lookup"><span data-stu-id="42256-156">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            // Set properties and call methods on options
        });
```

<span data-ttu-id="42256-157">`CreateDefaultBuilder` Pokud aplikace nevolá pro nastavení hostitele, <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> zavolejte **před** voláním `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="42256-157">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

```csharp
public static void Main(string[] args)
{
    var host = new WebHostBuilder()
        .UseContentRoot(Directory.GetCurrentDirectory())
        .UseKestrel()
        .UseIISIntegration()
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            // Set properties and call methods on options
        })
        .Build();

    host.Run();
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="42256-158">Chcete-li po volání `CreateDefaultBuilder`poskytnout další konfiguraci, zavolejte: <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*></span><span class="sxs-lookup"><span data-stu-id="42256-158">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            // Set properties and call methods on options
        });
```

::: moniker-end

## <a name="kestrel-options"></a><span data-ttu-id="42256-159">Kestrel možnosti</span><span class="sxs-lookup"><span data-stu-id="42256-159">Kestrel options</span></span>

<span data-ttu-id="42256-160">Webový server Kestrel má možnosti konfigurace omezení, které jsou zvláště užitečné v internetových nasazeních.</span><span class="sxs-lookup"><span data-stu-id="42256-160">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="42256-161">Nastavte omezení pro <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> vlastnost <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> třídy.</span><span class="sxs-lookup"><span data-stu-id="42256-161">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="42256-162">Vlastnost obsahuje instanci <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>třídy. `Limits`</span><span class="sxs-lookup"><span data-stu-id="42256-162">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="42256-163">V následujících příkladech se <xref:Microsoft.AspNetCore.Server.Kestrel.Core> používá obor názvů:</span><span class="sxs-lookup"><span data-stu-id="42256-163">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

### <a name="keep-alive-timeout"></a><span data-ttu-id="42256-164">Časový limit Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="42256-164">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="42256-165">Získá nebo nastaví [časový limit zachování](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="42256-165">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="42256-166">Výchozí hodnota je 2 minuty.</span><span class="sxs-lookup"><span data-stu-id="42256-166">Defaults to 2 minutes.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

::: moniker-end

### <a name="maximum-client-connections"></a><span data-ttu-id="42256-167">Maximální počet připojení klientů</span><span class="sxs-lookup"><span data-stu-id="42256-167">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="42256-168">Maximální počet souběžných otevřených připojení TCP lze pro celou aplikaci nastavit pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="42256-168">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Limits.MaxConcurrentConnections = 100;
        });
```

::: moniker-end

<span data-ttu-id="42256-169">Existuje zvláštní omezení pro připojení, která byla upgradována z protokolu HTTP nebo HTTPS na jiný protokol (například na žádost WebSockets).</span><span class="sxs-lookup"><span data-stu-id="42256-169">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="42256-170">Po upgradu připojení se nepočítá na `MaxConcurrentConnections` základě limitu.</span><span class="sxs-lookup"><span data-stu-id="42256-170">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

::: moniker-end

<span data-ttu-id="42256-171">Ve výchozím nastavení je maximální počet připojení neomezené (null).</span><span class="sxs-lookup"><span data-stu-id="42256-171">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="42256-172">Maximální velikost textu požadavku</span><span class="sxs-lookup"><span data-stu-id="42256-172">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="42256-173">Výchozí maximální velikost textu požadavku je 30 000 000 bajtů, což je přibližně 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="42256-173">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="42256-174">Doporučený postup pro přepsání limitu v aplikaci ASP.NET Core MVC je použití <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> atributu pro metodu akce:</span><span class="sxs-lookup"><span data-stu-id="42256-174">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="42256-175">Tady je příklad, který ukazuje, jak nakonfigurovat omezení pro aplikaci na každém požadavku:</span><span class="sxs-lookup"><span data-stu-id="42256-175">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="42256-176">Nastavení můžete v middlewaru přepsat konkrétním požadavkem:</span><span class="sxs-lookup"><span data-stu-id="42256-176">You can override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

::: moniker-end

<span data-ttu-id="42256-177">Výjimka je vyvolána, pokud se pokusíte nakonfigurovat limit žádosti poté, co aplikace začne číst požadavek.</span><span class="sxs-lookup"><span data-stu-id="42256-177">An exception is thrown if you attempt to configure the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="42256-178">Existuje vlastnost, která určuje, `MaxRequestBodySize` zda je vlastnost ve stavu jen pro čtení, což znamená, že je příliš pozdě pro konfiguraci limitu. `IsReadOnly`</span><span class="sxs-lookup"><span data-stu-id="42256-178">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="42256-179">Když je aplikace spouštěna [mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za [ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module), je omezení velikosti textu požadavku Kestrel zakázané, protože služba IIS tento limit již nastavuje.</span><span class="sxs-lookup"><span data-stu-id="42256-179">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="42256-180">Minimální rychlost těla zprávy požadavku</span><span class="sxs-lookup"><span data-stu-id="42256-180">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="42256-181">Kestrel kontroluje každou sekundu, pokud data přicházejí za zadanou rychlost v bajtech za sekundu.</span><span class="sxs-lookup"><span data-stu-id="42256-181">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="42256-182">Pokud frekvence klesne pod minimální hodnotu, vypršel časový limit připojení. Doba odkladu je doba, kterou Kestrel umožňuje klientovi zvýšit rychlost odesílání až na minimum; frekvence není během této doby kontrolována.</span><span class="sxs-lookup"><span data-stu-id="42256-182">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="42256-183">Doba odkladu pomáhá vyhnout se vyřazení připojení, která při počátečním posílání dat zpomalují rychlost, kvůli pomalému spuštění protokolu TCP.</span><span class="sxs-lookup"><span data-stu-id="42256-183">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="42256-184">Výchozí minimální sazba je 240 bajtů za sekundu s obdobím 5 sekund odkladu.</span><span class="sxs-lookup"><span data-stu-id="42256-184">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="42256-185">Pro odpověď platí také minimální sazba.</span><span class="sxs-lookup"><span data-stu-id="42256-185">A minimum rate also applies to the response.</span></span> <span data-ttu-id="42256-186">Kód pro nastavení limitu požadavku a limit odezvy je stejný, s výjimkou `RequestBody` , že existují nebo `Response` v názvech vlastností a rozhraní.</span><span class="sxs-lookup"><span data-stu-id="42256-186">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="42256-187">Tady je příklad, který ukazuje, jak nakonfigurovat minimální datové tarify v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="42256-187">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Limits.MinRequestBodyDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
            options.Limits.MinResponseDataRate =
                new MinDataRate(bytesPerSecond: 100, gracePeriod: TimeSpan.FromSeconds(10));
        });
```

::: moniker-end

<span data-ttu-id="42256-188">Limity minimální přenosové rychlosti na žádost můžete přepsat v middlewaru:</span><span class="sxs-lookup"><span data-stu-id="42256-188">You can override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="42256-189">Odkazovaná v předchozí ukázce není `HttpContext.Features` k dispozici pro požadavky HTTP/2, protože Změna omezení přenosové rychlosti u jednotlivých požadavků je obecně Nepodporovaná pro HTTP/2 kvůli podpoře protokolu pro multiplexování požadavků. <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature></span><span class="sxs-lookup"><span data-stu-id="42256-189">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="42256-190">`HttpContext.Features` `IHttpMinRequestBodyDataRateFeature.MinDataRate` `null`Propožadavky http/2 sealestálepoužívá,protožeomezeníčetnostičtenímůžebýtpořádzcelazakázanénazákladějednotlivýchpožadavkůnastavenímnaipropožadavekHTTP/2.<xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature></span><span class="sxs-lookup"><span data-stu-id="42256-190">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="42256-191">Když se pokusíte o jeho navýšení `null` `NotSupportedException` nebopokusojehonastavenínajinouhodnotunež,vygenerujesemupožadavek`IHttpMinRequestBodyDataRateFeature.MinDataRate` http/2.</span><span class="sxs-lookup"><span data-stu-id="42256-191">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="42256-192">Omezení přenosové rychlosti pro všechny servery `KestrelServerOptions.Limits` jsou nakonfigurovaná přes protokol HTTP/1. x i pro připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="42256-192">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="42256-193">Pro požadavky HTTP/2 se v předchozí ukázce neodkazuje žádná míra funkce, `HttpContext.Features` protože Změna omezení přenosové rychlosti na základě jednotlivých požadavků není podporovaná pro HTTP/2 kvůli podpoře protokolu pro multiplexování požadavků.</span><span class="sxs-lookup"><span data-stu-id="42256-193">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="42256-194">Omezení přenosové rychlosti pro všechny servery `KestrelServerOptions.Limits` jsou nakonfigurovaná přes protokol HTTP/1. x i pro připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="42256-194">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

::: moniker-end

### <a name="request-headers-timeout"></a><span data-ttu-id="42256-195">Časový limit hlaviček žádostí</span><span class="sxs-lookup"><span data-stu-id="42256-195">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="42256-196">Získá nebo nastaví maximální dobu, po kterou server stráví příjem hlaviček požadavků.</span><span class="sxs-lookup"><span data-stu-id="42256-196">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="42256-197">Výchozí hodnota je 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="42256-197">Defaults to 30 seconds.</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="42256-198">Maximální počet proudů na připojení</span><span class="sxs-lookup"><span data-stu-id="42256-198">Maximum streams per connection</span></span>

<span data-ttu-id="42256-199">`Http2.MaxStreamsPerConnection`omezuje počet souběžných datových proudů požadavků na připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="42256-199">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="42256-200">Nadbytečné proudy jsou odmítnuty.</span><span class="sxs-lookup"><span data-stu-id="42256-200">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="42256-201">Výchozí hodnota je 100.</span><span class="sxs-lookup"><span data-stu-id="42256-201">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="42256-202">Velikost tabulky hlaviček</span><span class="sxs-lookup"><span data-stu-id="42256-202">Header table size</span></span>

<span data-ttu-id="42256-203">Dekodér HPACK dekomprimuje hlavičky HTTP pro připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="42256-203">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="42256-204">`Http2.HeaderTableSize`omezuje velikost kompresní tabulky hlaviček, kterou používá dekodér HPACK.</span><span class="sxs-lookup"><span data-stu-id="42256-204">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="42256-205">Hodnota je uvedena v oktetech a musí být větší než nula (0).</span><span class="sxs-lookup"><span data-stu-id="42256-205">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="42256-206">Výchozí hodnota je 4096.</span><span class="sxs-lookup"><span data-stu-id="42256-206">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="42256-207">Maximální velikost rámce</span><span class="sxs-lookup"><span data-stu-id="42256-207">Maximum frame size</span></span>

<span data-ttu-id="42256-208">`Http2.MaxFrameSize`Určuje maximální velikost datové části rámce připojení HTTP/2, která se má přijmout.</span><span class="sxs-lookup"><span data-stu-id="42256-208">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="42256-209">Hodnota je uvedena v oktetech a musí být v rozmezí 2 ^ 14 (16 384) až 2 ^ 24-1 (16 777 215).</span><span class="sxs-lookup"><span data-stu-id="42256-209">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="42256-210">Výchozí hodnota je 2 ^ 14 (16 384).</span><span class="sxs-lookup"><span data-stu-id="42256-210">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="42256-211">Maximální velikost hlavičky požadavku</span><span class="sxs-lookup"><span data-stu-id="42256-211">Maximum request header size</span></span>

<span data-ttu-id="42256-212">`Http2.MaxRequestHeaderFieldSize`Určuje maximální povolenou velikost v oktetech hodnot hlaviček požadavku.</span><span class="sxs-lookup"><span data-stu-id="42256-212">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="42256-213">Toto omezení platí pro názvy i hodnoty společně v komprimovaných a nekomprimovaných reprezentujcích.</span><span class="sxs-lookup"><span data-stu-id="42256-213">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="42256-214">Hodnota musí být větší než nula (0).</span><span class="sxs-lookup"><span data-stu-id="42256-214">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="42256-215">Výchozí hodnota je 8 192.</span><span class="sxs-lookup"><span data-stu-id="42256-215">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="42256-216">Velikost okna počátečního připojení</span><span class="sxs-lookup"><span data-stu-id="42256-216">Initial connection window size</span></span>

<span data-ttu-id="42256-217">`Http2.InitialConnectionWindowSize`Určuje maximální velikost textu požadavku v bajtech, které jsou v jednom okamžiku agregovány v rámci všech požadavků (datových proudů) na připojení.</span><span class="sxs-lookup"><span data-stu-id="42256-217">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="42256-218">Žádosti jsou také omezené pomocí `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="42256-218">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="42256-219">Hodnota musí být větší než nebo rovna 65 535 a menší než 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="42256-219">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="42256-220">Výchozí hodnota je 128 KB (131 072).</span><span class="sxs-lookup"><span data-stu-id="42256-220">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="42256-221">Počáteční velikost okna datového proudu</span><span class="sxs-lookup"><span data-stu-id="42256-221">Initial stream window size</span></span>

<span data-ttu-id="42256-222">`Http2.InitialStreamWindowSize`Určuje maximální velikost textu požadavku v bajtech na jednu žádost (Stream) na jednom místě.</span><span class="sxs-lookup"><span data-stu-id="42256-222">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="42256-223">Žádosti jsou také omezené pomocí `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="42256-223">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="42256-224">Hodnota musí být větší než nebo rovna 65 535 a menší než 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="42256-224">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="42256-225">Výchozí hodnota je 96 KB (98 304).</span><span class="sxs-lookup"><span data-stu-id="42256-225">The default value is 96 KB (98,304).</span></span>

::: moniker-end

### <a name="synchronous-io"></a><span data-ttu-id="42256-226">Synchronní v/v</span><span class="sxs-lookup"><span data-stu-id="42256-226">Synchronous IO</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="42256-227"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>Určuje, zda je pro požadavek a odpověď povolena synchronní v/v.</span><span class="sxs-lookup"><span data-stu-id="42256-227"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="42256-228">Výchozí hodnota je `false`.</span><span class="sxs-lookup"><span data-stu-id="42256-228">The default value is `false`.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="42256-229"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>Určuje, zda je pro požadavek a odpověď povolena synchronní v/v.</span><span class="sxs-lookup"><span data-stu-id="42256-229"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="42256-230">Výchozí hodnota je `true`.</span><span class="sxs-lookup"><span data-stu-id="42256-230">The  default value is `true`.</span></span>

::: moniker-end

> [!WARNING]
> <span data-ttu-id="42256-231">Velký počet blokování synchronních vstupně-výstupních operací může vést ke vyčerpání fondu vláken, což aplikaci nereaguje.</span><span class="sxs-lookup"><span data-stu-id="42256-231">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="42256-232">Povolit `AllowSynchronousIO` pouze při použití knihovny, která nepodporuje asynchronní v/v.</span><span class="sxs-lookup"><span data-stu-id="42256-232">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="42256-233">Následující příklad povoluje synchronní vstupně-výstupní operace:</span><span class="sxs-lookup"><span data-stu-id="42256-233">The following example enables synchronous IO:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO&highlight=3)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="42256-234">Následující příklad zakáže synchronní v/v:</span><span class="sxs-lookup"><span data-stu-id="42256-234">The following example disables synchronous IO:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.AllowSynchronousIO = false;
        });
```

::: moniker-end

<span data-ttu-id="42256-235">Informace o dalších možnostech a omezeních Kestrel najdete v tématech:</span><span class="sxs-lookup"><span data-stu-id="42256-235">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="42256-236">Konfigurace koncového bodu</span><span class="sxs-lookup"><span data-stu-id="42256-236">Endpoint configuration</span></span>

<span data-ttu-id="42256-237">Ve výchozím nastavení ASP.NET Core váže k:</span><span class="sxs-lookup"><span data-stu-id="42256-237">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="42256-238">`https://localhost:5001`(Pokud je k dispozici místní vývojový certifikát)</span><span class="sxs-lookup"><span data-stu-id="42256-238">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="42256-239">Zadejte adresy URL pomocí:</span><span class="sxs-lookup"><span data-stu-id="42256-239">Specify URLs using the:</span></span>

* <span data-ttu-id="42256-240">`ASPNETCORE_URLS` proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="42256-240">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="42256-241">`--urls`argument příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="42256-241">`--urls` command-line argument.</span></span>
* <span data-ttu-id="42256-242">`urls`konfigurační klíč hostitele.</span><span class="sxs-lookup"><span data-stu-id="42256-242">`urls` host configuration key.</span></span>
* <span data-ttu-id="42256-243">`UseUrls`Metoda rozšíření</span><span class="sxs-lookup"><span data-stu-id="42256-243">`UseUrls` extension method.</span></span>

<span data-ttu-id="42256-244">Hodnota zadaná pomocí těchto přístupů může být jeden nebo více koncových bodů HTTP a HTTPS (HTTPS, pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="42256-244">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="42256-245">Nakonfigurujte hodnotu jako seznam oddělený středníkem (například `"Urls": "http://localhost:8000; http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="42256-245">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="42256-246">Další informace o těchto přístupůch najdete v tématu [adresy URL serveru](xref:fundamentals/host/web-host#server-urls) a potlačení [Konfigurace](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="42256-246">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="42256-247">Vytvoří se vývojový certifikát:</span><span class="sxs-lookup"><span data-stu-id="42256-247">A development certificate is created:</span></span>

* <span data-ttu-id="42256-248">Po instalaci [.NET Core SDK](/dotnet/core/sdk) .</span><span class="sxs-lookup"><span data-stu-id="42256-248">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="42256-249">[Nástroj pro vývoj a certifikáty](xref:aspnetcore-2.1#https) slouží k vytvoření certifikátu.</span><span class="sxs-lookup"><span data-stu-id="42256-249">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="42256-250">Některé prohlížeče vyžadují, abyste v prohlížeči udělili explicitní oprávnění k důvěřování místnímu vývojovému certifikátu.</span><span class="sxs-lookup"><span data-stu-id="42256-250">Some browsers require that you grant explicit permission to the browser to trust the local development certificate.</span></span>

<span data-ttu-id="42256-251">ASP.NET Core 2,1 a novější šablony projektů konfigurují aplikace tak, aby se spouštěly ve výchozím nastavení HTTPS a zahrnovaly [přesměrování https a podporu HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="42256-251">ASP.NET Core 2.1 and later project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="42256-252">Volání <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> nebo <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> metody<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> pro konfiguraci předpon adres URL a portů pro Kestrel.</span><span class="sxs-lookup"><span data-stu-id="42256-252">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="42256-253">`UseUrls`, argument `urls` `ASPNETCORE_URLS` příkazového řádku, konfigurační klíč hostitele a proměnná prostředí fungují také, ale mají omezení zaznamenaná později v této části (pro koncový bod HTTPS musí být dostupný výchozí certifikát.) `--urls` konfigurace).</span><span class="sxs-lookup"><span data-stu-id="42256-253">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="42256-254">Konfigurace ASP.NET Core 2,1 nebo `KestrelServerOptions` novější:</span><span class="sxs-lookup"><span data-stu-id="42256-254">ASP.NET Core 2.1 or later `KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionltlistenoptionsgt"></a><span data-ttu-id="42256-255">ConfigureEndpointDefaults (Action&lt;ListenOptions&gt;)</span><span class="sxs-lookup"><span data-stu-id="42256-255">ConfigureEndpointDefaults(Action&lt;ListenOptions&gt;)</span></span>

<span data-ttu-id="42256-256">Určuje konfiguraci `Action` , která se spustí pro každý zadaný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="42256-256">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="42256-257">Volání `ConfigureEndpointDefaults` několikrát nahrazuje předchozí `Action` `Action` zadanou parametr s.</span><span class="sxs-lookup"><span data-stu-id="42256-257">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.ConfigureKestrel(serverOptions =>
        {
            serverOptions.ConfigureEndpointDefaults(configureOptions =>
            {
                configureOptions.NoDelay = true;
            });
        });
        webBuilder.UseStartup<Startup>();
    });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.ConfigureEndpointDefaults(configureOptions =>
            {
                configureOptions.NoDelay = true;
            });
        });
```

::: moniker-end

### <a name="configurehttpsdefaultsactionlthttpsconnectionadapteroptionsgt"></a><span data-ttu-id="42256-258">ConfigureHttpsDefaults (Action&lt;HttpsConnectionAdapterOptions&gt;)</span><span class="sxs-lookup"><span data-stu-id="42256-258">ConfigureHttpsDefaults(Action&lt;HttpsConnectionAdapterOptions&gt;)</span></span>

<span data-ttu-id="42256-259">Určuje konfiguraci `Action` , která se má spustit pro každý koncový bod HTTPS.</span><span class="sxs-lookup"><span data-stu-id="42256-259">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="42256-260">Volání `ConfigureHttpsDefaults` několikrát nahrazuje předchozí `Action` `Action` zadanou parametr s.</span><span class="sxs-lookup"><span data-stu-id="42256-260">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

::: moniker range=">= aspnetcore-3.0"

```csharp
Host.CreateDefaultBuilder(args)
    .ConfigureWebHostDefaults(webBuilder =>
    {
        webBuilder.ConfigureKestrel(serverOptions =>
        {
            serverOptions.ConfigureHttpsDefaults(options =>
            {
                // certificate is an X509Certificate2
                options.ServerCertificate = certificate;
            });
        });
        webBuilder.UseStartup<Startup>();
    });
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.ConfigureHttpsDefaults(httpsOptions =>
            {
                // certificate is an X509Certificate2
                httpsOptions.ServerCertificate = certificate;
            });
        });
```

::: moniker-end

### <a name="configureiconfiguration"></a><span data-ttu-id="42256-261">Konfigurace (IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="42256-261">Configure(IConfiguration)</span></span>

<span data-ttu-id="42256-262">Vytvoří zavaděč konfigurace pro nastavení Kestrel, který přebírá <xref:Microsoft.Extensions.Configuration.IConfiguration> jako vstup.</span><span class="sxs-lookup"><span data-stu-id="42256-262">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="42256-263">Konfigurace musí být vymezená na konfigurační oddíl pro Kestrel.</span><span class="sxs-lookup"><span data-stu-id="42256-263">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="42256-264">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="42256-264">ListenOptions.UseHttps</span></span>

<span data-ttu-id="42256-265">Nakonfigurujte Kestrel tak, aby používal protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="42256-265">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="42256-266">`ListenOptions.UseHttps`SND</span><span class="sxs-lookup"><span data-stu-id="42256-266">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="42256-267">`UseHttps`&ndash; Nakonfigurujte Kestrel tak, aby používal HTTPS s výchozím certifikátem.</span><span class="sxs-lookup"><span data-stu-id="42256-267">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="42256-268">Vyvolá výjimku, pokud není nakonfigurován žádný výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="42256-268">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="42256-269">`ListenOptions.UseHttps`ukazatelů</span><span class="sxs-lookup"><span data-stu-id="42256-269">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="42256-270">`filename`je cesta a název souboru certifikátu vzhledem k adresáři, který obsahuje soubory obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="42256-270">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="42256-271">`password`je vyžadováno heslo pro přístup k datům certifikátu X. 509.</span><span class="sxs-lookup"><span data-stu-id="42256-271">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="42256-272">`configureOptions``Action` je ke`HttpsConnectionAdapterOptions`konfiguraci.</span><span class="sxs-lookup"><span data-stu-id="42256-272">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="42256-273">`ListenOptions`Vrátí.</span><span class="sxs-lookup"><span data-stu-id="42256-273">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="42256-274">`storeName`je úložiště certifikátů, ze kterého se má certifikát načíst.</span><span class="sxs-lookup"><span data-stu-id="42256-274">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="42256-275">`subject`je název subjektu certifikátu.</span><span class="sxs-lookup"><span data-stu-id="42256-275">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="42256-276">`allowInvalid`Určuje, zda by měly být zváženy neplatné certifikáty, například certifikáty podepsané svým držitelem.</span><span class="sxs-lookup"><span data-stu-id="42256-276">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="42256-277">`location`je umístěním úložiště, ze kterého se má certifikát načíst.</span><span class="sxs-lookup"><span data-stu-id="42256-277">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="42256-278">`serverCertificate`je certifikát X. 509.</span><span class="sxs-lookup"><span data-stu-id="42256-278">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="42256-279">V produkčním prostředí musí být HTTPS explicitně nakonfigurovaný.</span><span class="sxs-lookup"><span data-stu-id="42256-279">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="42256-280">Musí být zadán minimálně výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="42256-280">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="42256-281">Podporované konfigurace popsané dál:</span><span class="sxs-lookup"><span data-stu-id="42256-281">Supported configurations described next:</span></span>

* <span data-ttu-id="42256-282">Žádná konfigurace</span><span class="sxs-lookup"><span data-stu-id="42256-282">No configuration</span></span>
* <span data-ttu-id="42256-283">Nahradit výchozí certifikát z konfigurace</span><span class="sxs-lookup"><span data-stu-id="42256-283">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="42256-284">Změna výchozích hodnot v kódu</span><span class="sxs-lookup"><span data-stu-id="42256-284">Change the defaults in code</span></span>

<span data-ttu-id="42256-285">*Žádná konfigurace*</span><span class="sxs-lookup"><span data-stu-id="42256-285">*No configuration*</span></span>

<span data-ttu-id="42256-286">Kestrel naslouchá na `http://localhost:5000` a `https://localhost:5001` (Pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="42256-286">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="42256-287">*Nahradit výchozí certifikát z konfigurace*</span><span class="sxs-lookup"><span data-stu-id="42256-287">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="42256-288"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>Při `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` načítání konfigurace Kestrel ve výchozím nastavení volá.</span><span class="sxs-lookup"><span data-stu-id="42256-288"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="42256-289">Pro Kestrel je k dispozici výchozí schéma konfigurace nastavení aplikace HTTPS.</span><span class="sxs-lookup"><span data-stu-id="42256-289">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="42256-290">Nakonfigurujte několik koncových bodů, včetně adres URL a certifikátů, které se mají použít, buď ze souboru na disku, nebo z úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="42256-290">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="42256-291">V následujícím příkladu *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="42256-291">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="42256-292">Nastavte **AllowInvalid** na `true` , aby povolovala použití neplatných certifikátů (například certifikátů podepsaných svým držitelem).</span><span class="sxs-lookup"><span data-stu-id="42256-292">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="42256-293">Libovolný koncový bod HTTPS, který nemá určenou certifikát (**HttpsDefaultCert** v následujícím příkladu) spadne zpět na cert definované v části **certifikáty** > **výchozí** nebo certifikát pro vývoj.</span><span class="sxs-lookup"><span data-stu-id="42256-293">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="42256-294">Alternativou k použití **cesty** a **hesla** pro libovolný uzel certifikátu je zadání certifikátu pomocí polí úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="42256-294">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="42256-295">Například > **výchozí** certifikát může být zadán jako:</span><span class="sxs-lookup"><span data-stu-id="42256-295">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="42256-296">Poznámky ke schématu:</span><span class="sxs-lookup"><span data-stu-id="42256-296">Schema notes:</span></span>

* <span data-ttu-id="42256-297">V názvech koncových bodů se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="42256-297">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="42256-298">Například `HTTPS` a`Https` jsou platné.</span><span class="sxs-lookup"><span data-stu-id="42256-298">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="42256-299">U každého koncového bodu je vyžadován parametr.`Url`</span><span class="sxs-lookup"><span data-stu-id="42256-299">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="42256-300">Formát pro tento parametr je stejný jako konfigurační parametr nejvyšší úrovně `Urls` s tím rozdílem, že je omezen na jedinou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="42256-300">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="42256-301">Tyto koncové body nahrazují hodnoty definované v konfiguraci nejvyšší úrovně `Urls` místo jejich přidání do těchto koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="42256-301">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="42256-302">Koncové body definované v kódu `Listen` prostřednictvím jsou kumulativní pomocí koncových bodů definovaných v konfiguračním oddílu.</span><span class="sxs-lookup"><span data-stu-id="42256-302">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="42256-303">`Certificate` Oddíl je nepovinný.</span><span class="sxs-lookup"><span data-stu-id="42256-303">The `Certificate` section is optional.</span></span> <span data-ttu-id="42256-304">Pokud není `Certificate` oddíl zadaný, použijí se výchozí hodnoty definované v dřívějších scénářích.</span><span class="sxs-lookup"><span data-stu-id="42256-304">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="42256-305">Pokud nejsou k dispozici žádné výchozí hodnoty, Server vyvolá výjimku a nemůže se spustit.</span><span class="sxs-lookup"><span data-stu-id="42256-305">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="42256-306">&ndash;&ndash; Oddíl podporuje jak heslo cesty, tak certifikáty úložiště subjektu. `Certificate`</span><span class="sxs-lookup"><span data-stu-id="42256-306">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="42256-307">V takovém případě může být definován libovolný počet koncových bodů, pokud to nezpůsobí konflikty portů.</span><span class="sxs-lookup"><span data-stu-id="42256-307">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="42256-308">`options.Configure(context.Configuration.GetSection("Kestrel"))``KestrelConfigurationLoader` vrátí metodu`.Endpoint(string name, options => { })` s metodou, kterou lze použít k doplnění nastavení nakonfigurovaného koncového bodu:</span><span class="sxs-lookup"><span data-stu-id="42256-308">`options.Configure(context.Configuration.GetSection("Kestrel"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, options => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

  ```csharp
  options.Configure(context.Configuration.GetSection("Kestrel"))
      .Endpoint("HTTPS", opt =>
      {
          opt.HttpsOptions.SslProtocols = SslProtocols.Tls12;
      });
  ```

  <span data-ttu-id="42256-309">Můžete také přímo získat přístup `KestrelServerOptions.ConfigurationLoader` k iteraci stávajícího zavaděče, jako je třeba ten, který <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>poskytuje.</span><span class="sxs-lookup"><span data-stu-id="42256-309">You can also directly access `KestrelServerOptions.ConfigurationLoader` to keep iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="42256-310">Konfigurační oddíl pro každý koncový bod je k dispozici na možnostech v `Endpoint` metodě, aby bylo možné číst vlastní nastavení.</span><span class="sxs-lookup"><span data-stu-id="42256-310">The configuration section for each endpoint is a available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="42256-311">Více konfigurací může být načteno voláním `options.Configure(context.Configuration.GetSection("Kestrel"))` znovu s jiným oddílem.</span><span class="sxs-lookup"><span data-stu-id="42256-311">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("Kestrel"))` again with another section.</span></span> <span data-ttu-id="42256-312">Je použita pouze poslední konfigurace, pokud `Load` není explicitně volána při předchozích instancích.</span><span class="sxs-lookup"><span data-stu-id="42256-312">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="42256-313">Metapackage nevolá `Load` , aby se jeho výchozí konfigurační oddíl mohl nahradit.</span><span class="sxs-lookup"><span data-stu-id="42256-313">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="42256-314">`KestrelConfigurationLoader`zrcadlí `KestrelServerOptions` `Endpoint` rodinu rozhraní API z as přetížení, takže koncové body kódu a konfigurace lze konfigurovat na stejném místě. `Listen`</span><span class="sxs-lookup"><span data-stu-id="42256-314">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="42256-315">Tato přetížení nepoužívají názvy a využívají pouze výchozí nastavení z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="42256-315">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="42256-316">*Změna výchozích hodnot v kódu*</span><span class="sxs-lookup"><span data-stu-id="42256-316">*Change the defaults in code*</span></span>

<span data-ttu-id="42256-317">`ConfigureEndpointDefaults`a `ConfigureHttpsDefaults` dá se použít ke změně výchozího nastavení pro `ListenOptions` a `HttpsConnectionAdapterOptions`, včetně přepsání výchozího certifikátu zadaného v předchozím scénáři.</span><span class="sxs-lookup"><span data-stu-id="42256-317">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="42256-318">`ConfigureEndpointDefaults`a `ConfigureHttpsDefaults` měla by být volána před konfigurací koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="42256-318">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
options.ConfigureEndpointDefaults(opt =>
{
    opt.NoDelay = true;
});

options.ConfigureHttpsDefaults(httpsOptions =>
{
    httpsOptions.SslProtocols = SslProtocols.Tls12;
});
```

<span data-ttu-id="42256-319">*Podpora Kestrel pro SNI*</span><span class="sxs-lookup"><span data-stu-id="42256-319">*Kestrel support for SNI*</span></span>

<span data-ttu-id="42256-320">[Indikace názvu serveru (SNI)](https://tools.ietf.org/html/rfc6066#section-3) lze použít k hostování více domén na stejné IP adrese a portu.</span><span class="sxs-lookup"><span data-stu-id="42256-320">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="42256-321">Aby SNI fungovalo, klient pošle název hostitele pro zabezpečenou relaci serveru během metody handshake TLS, aby server mohl poskytovat správný certifikát.</span><span class="sxs-lookup"><span data-stu-id="42256-321">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="42256-322">Klient používá poskytnutý certifikát pro šifrovanou komunikaci se serverem během zabezpečené relace, která následuje za metodou handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="42256-322">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="42256-323">Kestrel podporuje sni prostřednictvím `ServerCertificateSelector` zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="42256-323">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="42256-324">Zpětné volání je vyvoláno jednou za připojení, které aplikaci umožní zkontrolovat název hostitele a vybrat příslušný certifikát.</span><span class="sxs-lookup"><span data-stu-id="42256-324">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="42256-325">Podpora SNI vyžaduje:</span><span class="sxs-lookup"><span data-stu-id="42256-325">SNI support requires:</span></span>

* <span data-ttu-id="42256-326">Běží v cílové architektuře `netcoreapp2.1`.</span><span class="sxs-lookup"><span data-stu-id="42256-326">Running on target framework `netcoreapp2.1`.</span></span> <span data-ttu-id="42256-327">V `netcoreapp2.0` systémech `net461`a `null` je`name` zpětné volání vyvoláno, ale je vždy.</span><span class="sxs-lookup"><span data-stu-id="42256-327">On `netcoreapp2.0` and `net461`, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="42256-328">`name` Je také`null` v případě, že klient v TLS handshake neposkytne parametr názvu hostitele.</span><span class="sxs-lookup"><span data-stu-id="42256-328">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="42256-329">Všechny weby běží na stejné instanci Kestrel.</span><span class="sxs-lookup"><span data-stu-id="42256-329">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="42256-330">Kestrel nepodporuje sdílení IP adresy a portu napříč několika instancemi bez reverzního proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="42256-330">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

::: moniker range=">= aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.ListenAnyIP(5005, listenOptions =>
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

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel((context, options) =>
        {
            options.ListenAnyIP(5005, listenOptions =>
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

::: moniker-end

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="42256-331">Vytvoření vazby na soket TCP</span><span class="sxs-lookup"><span data-stu-id="42256-331">Bind to a TCP socket</span></span>

<span data-ttu-id="42256-332"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> Metoda se váže k soketu TCP a pomocí lambda možností povolíte konfiguraci certifikátu X. 509:</span><span class="sxs-lookup"><span data-stu-id="42256-332">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static void Main(string[] args)
{
    CreateWebHostBuilder(args).Build().Run();
}

public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.Listen(IPAddress.Loopback, 5000);
            options.Listen(IPAddress.Loopback, 5001, listenOptions =>
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
        .UseKestrel(options =>
        {
            options.Listen(IPAddress.Loopback, 5000);
            options.Listen(IPAddress.Loopback, 5001, listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

::: moniker-end

<span data-ttu-id="42256-333">Příklad nakonfiguruje HTTPS pro koncový bod pomocí <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="42256-333">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="42256-334">Ke konfiguraci dalších nastavení Kestrel pro konkrétní koncové body použijte stejné rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="42256-334">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="42256-335">Vytvoření vazby na soket UNIX</span><span class="sxs-lookup"><span data-stu-id="42256-335">Bind to a Unix socket</span></span>

<span data-ttu-id="42256-336">Naslouchat na soketu <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> se systémem UNIX, aby se zlepšil výkon pomocí Nginx, jak je znázorněno v tomto příkladu:</span><span class="sxs-lookup"><span data-stu-id="42256-336">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

::: moniker range=">= aspnetcore-2.2"

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(options =>
        {
            options.ListenUnixSocket("/tmp/kestrel-test.sock");
            options.ListenUnixSocket("/tmp/kestrel-test.sock", listenOptions =>
            {
                listenOptions.UseHttps("testCert.pfx", "testpassword");
            });
        });
```

::: moniker-end

### <a name="port-0"></a><span data-ttu-id="42256-337">Port 0</span><span class="sxs-lookup"><span data-stu-id="42256-337">Port 0</span></span>

<span data-ttu-id="42256-338">Po zadání čísla `0` portu se Kestrel dynamicky váže k dostupnému portu.</span><span class="sxs-lookup"><span data-stu-id="42256-338">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="42256-339">Následující příklad ukazuje, jak určit, který port Kestrel je skutečně vázaný za běhu:</span><span class="sxs-lookup"><span data-stu-id="42256-339">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="42256-340">Když se aplikace spustí, výstup okna konzoly indikuje dynamický port, na který se aplikace dá získat:</span><span class="sxs-lookup"><span data-stu-id="42256-340">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="42256-341">Omezení</span><span class="sxs-lookup"><span data-stu-id="42256-341">Limitations</span></span>

<span data-ttu-id="42256-342">Konfigurace koncových bodů pomocí následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="42256-342">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="42256-343">`--urls`argument příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="42256-343">`--urls` command-line argument</span></span>
* <span data-ttu-id="42256-344">`urls`konfigurační klíč hostitele</span><span class="sxs-lookup"><span data-stu-id="42256-344">`urls` host configuration key</span></span>
* <span data-ttu-id="42256-345">`ASPNETCORE_URLS`Proměnná prostředí</span><span class="sxs-lookup"><span data-stu-id="42256-345">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="42256-346">Tyto metody jsou užitečné, pokud chcete, aby kód pracoval na jiných serverech než Kestrel.</span><span class="sxs-lookup"><span data-stu-id="42256-346">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="42256-347">Mějte ale na paměti následující omezení:</span><span class="sxs-lookup"><span data-stu-id="42256-347">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="42256-348">Protokol HTTPS nelze s těmito přístupy použít, pokud není k dispozici výchozí certifikát v konfiguraci koncového bodu HTTPS (například `KestrelServerOptions` pomocí konfigurace nebo konfiguračního souboru, jak je uvedeno výše v tomto tématu).</span><span class="sxs-lookup"><span data-stu-id="42256-348">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="42256-349">Při současném `Listen` `UseUrls` použitíobou`UseUrls` přístupů a koncovýchbodůsekoncovébodypřepisují.`Listen`</span><span class="sxs-lookup"><span data-stu-id="42256-349">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="42256-350">Konfigurace koncového bodu služby IIS</span><span class="sxs-lookup"><span data-stu-id="42256-350">IIS endpoint configuration</span></span>

<span data-ttu-id="42256-351">Při použití služby IIS jsou vazby adresy URL pro přepsání vazby služby IIS nastaveny buď `Listen` nebo `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="42256-351">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="42256-352">Další informace najdete v tématu [ASP.NET Core modulu](xref:host-and-deploy/aspnet-core-module) .</span><span class="sxs-lookup"><span data-stu-id="42256-352">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

::: moniker range=">= aspnetcore-2.2"

### <a name="listenoptionsprotocols"></a><span data-ttu-id="42256-353">ListenOptions. Protocols</span><span class="sxs-lookup"><span data-stu-id="42256-353">ListenOptions.Protocols</span></span>

<span data-ttu-id="42256-354">Vlastnost určuje protokoly HTTP (`HttpProtocols`) povolené pro koncový bod připojení nebo pro server. `Protocols`</span><span class="sxs-lookup"><span data-stu-id="42256-354">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="42256-355">Přiřaďte hodnotu `Protocols` vlastnosti `HttpProtocols` z výčtu.</span><span class="sxs-lookup"><span data-stu-id="42256-355">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="42256-356">`HttpProtocols`hodnota výčtu</span><span class="sxs-lookup"><span data-stu-id="42256-356">`HttpProtocols` enum value</span></span> | <span data-ttu-id="42256-357">Povolený protokol připojení</span><span class="sxs-lookup"><span data-stu-id="42256-357">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="42256-358">Pouze HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="42256-358">HTTP/1.1 only.</span></span> <span data-ttu-id="42256-359">Dá se použít s TLS nebo bez něj.</span><span class="sxs-lookup"><span data-stu-id="42256-359">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="42256-360">Pouze HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="42256-360">HTTP/2 only.</span></span> <span data-ttu-id="42256-361">Primárně se používá s protokolem TLS.</span><span class="sxs-lookup"><span data-stu-id="42256-361">Primarily used with TLS.</span></span> <span data-ttu-id="42256-362">Dá se použít bez TLS jenom v případě, že klient podporuje [předchozí režim znalostní báze](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="42256-362">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="42256-363">HTTP/1.1 a HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="42256-363">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="42256-364">Pro vyjednávání HTTP/2 vyžaduje připojení TLS a [protokol ALPN (Application-Layer Protocol)](https://tools.ietf.org/html/rfc7301#section-3) . v opačném případě se připojení nastaví jako výchozí HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="42256-364">Requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection to negotiate HTTP/2; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="42256-365">Výchozí protokol HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="42256-365">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="42256-366">Omezení TLS pro HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="42256-366">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="42256-367">TLS verze 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="42256-367">TLS version 1.2 or later</span></span>
* <span data-ttu-id="42256-368">Opětovné vyjednávání je zakázané.</span><span class="sxs-lookup"><span data-stu-id="42256-368">Renegotiation disabled</span></span>
* <span data-ttu-id="42256-369">Komprese vypnuta</span><span class="sxs-lookup"><span data-stu-id="42256-369">Compression disabled</span></span>
* <span data-ttu-id="42256-370">Minimální velikosti dočasného výměny klíčů:</span><span class="sxs-lookup"><span data-stu-id="42256-370">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="42256-371">Eliptická křivka Diffie-Hellman (ECDH &lbrack;) [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; &ndash; 224 bity minima</span><span class="sxs-lookup"><span data-stu-id="42256-371">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum</span></span>
  * <span data-ttu-id="42256-372">Omezené pole Diffie- &lbrack; Hellman (DHE) `TLS12` &rbrack; &ndash; 2048 bity minima</span><span class="sxs-lookup"><span data-stu-id="42256-372">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bits minimum</span></span>
* <span data-ttu-id="42256-373">Šifrovací sada není zakázaná.</span><span class="sxs-lookup"><span data-stu-id="42256-373">Cipher suite not blacklisted</span></span>

<span data-ttu-id="42256-374">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack; `FIPS186` &rbrack; ve výchozím nastavení je podporována eliptická křivka P-256. &lbrack; `TLS-ECDHE` &rbrack;</span><span class="sxs-lookup"><span data-stu-id="42256-374">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="42256-375">Následující příklad povoluje připojení HTTP/1.1 a HTTP/2 na portu 8000.</span><span class="sxs-lookup"><span data-stu-id="42256-375">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="42256-376">Připojení TLS zabezpečuje pomocí zadaného certifikátu:</span><span class="sxs-lookup"><span data-stu-id="42256-376">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Listen(IPAddress.Any, 8000, listenOptions =>
            {
                listenOptions.Protocols = HttpProtocols.Http1AndHttp2;
                listenOptions.UseHttps("testCert.pfx", "testPassword");
            });
        });
```

<span data-ttu-id="42256-377">Volitelně můžete vytvořit `IConnectionAdapter` implementaci pro filtrování ověřování TLS metodou handshake na základě jednotlivých připojení pro konkrétní šifry:</span><span class="sxs-lookup"><span data-stu-id="42256-377">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, options) =>
        {
            options.Listen(IPAddress.Any, 8000, listenOptions =>
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

        // Throw NotSupportedException for any cipher algorithm that you don't
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

<span data-ttu-id="42256-378">*Nastavit protokol z konfigurace*</span><span class="sxs-lookup"><span data-stu-id="42256-378">*Set the protocol from configuration*</span></span>

<span data-ttu-id="42256-379"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>Při `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` načítání konfigurace Kestrel ve výchozím nastavení volá.</span><span class="sxs-lookup"><span data-stu-id="42256-379"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="42256-380">V následujícím příkladu *appSettings. JSON* se pro všechny koncové body Kestrel vytvoří výchozí protokol připojení (http/1.1 a HTTP/2):</span><span class="sxs-lookup"><span data-stu-id="42256-380">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="42256-381">Následující příklad konfiguračního souboru vytvoří protokol připojení pro konkrétní koncový bod:</span><span class="sxs-lookup"><span data-stu-id="42256-381">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="42256-382">Protokoly zadané v hodnotách přepisu kódu nastavených podle konfigurace</span><span class="sxs-lookup"><span data-stu-id="42256-382">Protocols specified in code override values set by configuration.</span></span>

::: moniker-end

## <a name="transport-configuration"></a><span data-ttu-id="42256-383">Konfigurace přenosu</span><span class="sxs-lookup"><span data-stu-id="42256-383">Transport configuration</span></span>

<span data-ttu-id="42256-384">S vydáním ASP.NET Core 2,1 není výchozí přenos Kestrel založen na Libuv, ale na základě spravovaných soketů.</span><span class="sxs-lookup"><span data-stu-id="42256-384">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="42256-385">Jedná se o zásadní změnu pro aplikace ASP.NET Core 2,0 upgradované na 2,1, <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> které volá a závisí na jednom z následujících balíčků:</span><span class="sxs-lookup"><span data-stu-id="42256-385">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="42256-386">[Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (přímý odkaz na balíček)</span><span class="sxs-lookup"><span data-stu-id="42256-386">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="42256-387">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="42256-387">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="42256-388">Pro projekty ASP.NET Core 2,1 nebo novější, které používají [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app) a vyžadují použití Libuv:</span><span class="sxs-lookup"><span data-stu-id="42256-388">For ASP.NET Core 2.1 or later projects that use the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and require the use of Libuv:</span></span>

* <span data-ttu-id="42256-389">Přidejte do souboru projektu aplikace závislost pro balíček [Microsoft. AspNetCore. Server. Kestrel. Transport. Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) :</span><span class="sxs-lookup"><span data-stu-id="42256-389">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

    ```xml
    <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                      Version="<LATEST_VERSION>" />
    ```

* <span data-ttu-id="42256-390">Volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span><span class="sxs-lookup"><span data-stu-id="42256-390">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="42256-391">Předpony adresy URL</span><span class="sxs-lookup"><span data-stu-id="42256-391">URL prefixes</span></span>

<span data-ttu-id="42256-392">Při použití `UseUrls`, `--urls` argumentu příkazového řádku `urls` , konfiguračního klíče hostitele nebo `ASPNETCORE_URLS` proměnné prostředí můžou být předpony adresy URL v některém z následujících formátů.</span><span class="sxs-lookup"><span data-stu-id="42256-392">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="42256-393">Platné jsou pouze předpony adresy URL protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="42256-393">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="42256-394">Kestrel nepodporuje protokol HTTPS při konfiguraci vazeb URL pomocí `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="42256-394">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="42256-395">Adresa IPv4 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="42256-395">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="42256-396">`0.0.0.0`je zvláštní případ, který se váže ke všem adresám IPv4.</span><span class="sxs-lookup"><span data-stu-id="42256-396">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="42256-397">Adresa IPv6 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="42256-397">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="42256-398">`[::]`je ekvivalentem protokolu IPv4 `0.0.0.0`pro protokol IPv6.</span><span class="sxs-lookup"><span data-stu-id="42256-398">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="42256-399">Název hostitele s číslem portu</span><span class="sxs-lookup"><span data-stu-id="42256-399">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="42256-400">Názvy `*`hostitelů, a `+`nejsou speciální.</span><span class="sxs-lookup"><span data-stu-id="42256-400">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="42256-401">Cokoli se nerozpoznalo jako platná IP adresa `localhost` nebo se váže ke všem IP adresám IPv4 a IPv6.</span><span class="sxs-lookup"><span data-stu-id="42256-401">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="42256-402">Pokud chcete navazovat různé názvy hostitelů na různé ASP.NET Core aplikace na stejném portu, použijte [http. sys](xref:fundamentals/servers/httpsys) nebo reverzní proxy server, třeba IIS, Nginx nebo Apache.</span><span class="sxs-lookup"><span data-stu-id="42256-402">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="42256-403">Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="42256-403">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="42256-404">Název `localhost` hostitele s číslem portu nebo IP adresou zpětné smyčky s číslem portu</span><span class="sxs-lookup"><span data-stu-id="42256-404">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="42256-405">Když `localhost` je zadaný, Kestrel se pokusí vytvořit propojení s rozhraními zpětné smyčky IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="42256-405">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="42256-406">Pokud je požadovaný port používán jinou službou na rozhraní zpětné smyčky, spuštění Kestrel se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="42256-406">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="42256-407">Pokud je rozhraní zpětné smyčky v žádném jiném případě nedostupné (většinou kvůli tomu, že protokol IPv6 není podporovaný), Kestrel zaznamená upozornění.</span><span class="sxs-lookup"><span data-stu-id="42256-407">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="42256-408">Filtrování hostitele</span><span class="sxs-lookup"><span data-stu-id="42256-408">Host filtering</span></span>

<span data-ttu-id="42256-409">I když Kestrel podporuje konfiguraci na základě prefixů `http://example.com:5000`, jako je, Kestrel převážně ignoruje název hostitele.</span><span class="sxs-lookup"><span data-stu-id="42256-409">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="42256-410">Hostitel `localhost` je zvláštní případ, který se používá pro vazbu na adresy zpětné smyčky.</span><span class="sxs-lookup"><span data-stu-id="42256-410">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="42256-411">Každý hostitel, který je jiný než explicitní IP adresa, se váže na všechny veřejné IP adresy.</span><span class="sxs-lookup"><span data-stu-id="42256-411">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="42256-412">`Host`hlavičky nejsou ověřené.</span><span class="sxs-lookup"><span data-stu-id="42256-412">`Host` headers aren't validated.</span></span>

<span data-ttu-id="42256-413">Jako alternativní řešení použijte middleware pro filtrování hostitele.</span><span class="sxs-lookup"><span data-stu-id="42256-413">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="42256-414">Middleware pro filtrování hostitele poskytuje balíček [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , který je součástí [Microsoft. AspNetCore. App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 nebo novější).</span><span class="sxs-lookup"><span data-stu-id="42256-414">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or later).</span></span> <span data-ttu-id="42256-415">Middleware je přidána pomocí <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, který volá <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="42256-415">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="42256-416">Middleware pro filtrování hostitele je ve výchozím nastavení zakázáno.</span><span class="sxs-lookup"><span data-stu-id="42256-416">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="42256-417">Chcete-li povolit middleware, `AllowedHosts` Definujte klíč v souboru *appSettings. JSON*/ *\< appSettings. Environment >. JSON*.</span><span class="sxs-lookup"><span data-stu-id="42256-417">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="42256-418">Hodnota je seznam názvů hostitelů oddělených středníkem bez čísel portů:</span><span class="sxs-lookup"><span data-stu-id="42256-418">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="42256-419">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="42256-419">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="42256-420">[Middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer) má <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> také možnost.</span><span class="sxs-lookup"><span data-stu-id="42256-420">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="42256-421">Middleware předávaných hlaviček a middleware pro filtrování hostitele mají podobné funkce pro různé scénáře.</span><span class="sxs-lookup"><span data-stu-id="42256-421">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="42256-422">Nastavení `AllowedHosts` pomocí předávaných middlewarových hlaviček je vhodné `Host` v případě, že hlavička není zachována při předávání požadavků pomocí reverzního proxy server nebo nástroje pro vyrovnávání zatížení.</span><span class="sxs-lookup"><span data-stu-id="42256-422">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="42256-423">Nastavení `AllowedHosts` pomocí middlewaru pro filtrování hostitele je vhodné, když se Kestrel používá jako veřejný hraniční Server nebo `Host` když se hlavička přímo přepošle.</span><span class="sxs-lookup"><span data-stu-id="42256-423">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="42256-424">Další informace o middlewaru předávaných hlaviček najdete <xref:host-and-deploy/proxy-load-balancer>v tématu.</span><span class="sxs-lookup"><span data-stu-id="42256-424">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="42256-425">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="42256-425">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="42256-426">Zdrojový kód Kestrel</span><span class="sxs-lookup"><span data-stu-id="42256-426">Kestrel source code</span></span>](https://github.com/aspnet/AspNetCore/tree/master/src/Servers/Kestrel)
* [<span data-ttu-id="42256-427">RFC 7230: Syntaxe zprávy a směrování (oddíl 5,4: Provoz</span><span class="sxs-lookup"><span data-stu-id="42256-427">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
