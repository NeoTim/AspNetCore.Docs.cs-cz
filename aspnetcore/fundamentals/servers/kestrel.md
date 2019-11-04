---
title: Implementace webového serveru Kestrel v ASP.NET Core
author: guardrex
description: Přečtěte si o Kestrel, webovém serveru pro různé platformy pro ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/31/2019
uid: fundamentals/servers/kestrel
ms.openlocfilehash: bab751bc1453481a11114a7a8c0787fa5576e500
ms.sourcegitcommit: 77c8be22d5e88dd710f42c739748869f198865dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2019
ms.locfileid: "73427062"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="55a68-103">Implementace webového serveru Kestrel v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="55a68-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="55a68-104">Tím, že [Dykstra](https://github.com/tdykstra), [Chris Rossův](https://github.com/Tratcher), [Stephen zastavili](https://twitter.com/halter73)a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="55a68-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), [Stephen Halter](https://twitter.com/halter73), and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="55a68-105">Kestrel je [webový server pro různé platformy pro ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="55a68-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="55a68-106">Kestrel je webový server, který je ve výchozím nastavení součástí ASP.NET Core šablon projektů.</span><span class="sxs-lookup"><span data-stu-id="55a68-106">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="55a68-107">Kestrel podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="55a68-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="55a68-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="55a68-108">HTTPS</span></span>
* <span data-ttu-id="55a68-109">Neprůhledný upgrade používaný k povolení [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="55a68-109">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="55a68-110">Technologie UNIX Sockets s vysokým výkonem za Nginx</span><span class="sxs-lookup"><span data-stu-id="55a68-110">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="55a68-111">HTTP/2 (kromě macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="55a68-111">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="55a68-112">&dagger;HTTP/2 bude v budoucí verzi podporován v macOS.</span><span class="sxs-lookup"><span data-stu-id="55a68-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="55a68-113">Kestrel se podporuje na všech platformách a verzích, které podporuje .NET Core.</span><span class="sxs-lookup"><span data-stu-id="55a68-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="55a68-114">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="55a68-114">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="55a68-115">Podpora HTTP/2</span><span class="sxs-lookup"><span data-stu-id="55a68-115">HTTP/2 support</span></span>

<span data-ttu-id="55a68-116">[Http/2](https://httpwg.org/specs/rfc7540.html) je k dispozici pro ASP.NET Core aplikace, pokud jsou splněné následující základní požadavky:</span><span class="sxs-lookup"><span data-stu-id="55a68-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="55a68-117">&dagger; operačního systému</span><span class="sxs-lookup"><span data-stu-id="55a68-117">Operating system&dagger;</span></span>
  * <span data-ttu-id="55a68-118">Windows Server 2016/Windows 10 nebo novější&Dagger;</span><span class="sxs-lookup"><span data-stu-id="55a68-118">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="55a68-119">Linux s OpenSSL 1.0.2 nebo novějším (například Ubuntu 16,04 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="55a68-119">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="55a68-120">Cílová architektura: .NET Core 2,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="55a68-120">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="55a68-121">Připojení [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="55a68-121">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="55a68-122">Připojení TLS 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="55a68-122">TLS 1.2 or later connection</span></span>

<span data-ttu-id="55a68-123">&dagger;HTTP/2 bude v budoucí verzi podporován v macOS.</span><span class="sxs-lookup"><span data-stu-id="55a68-123">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="55a68-124">&Dagger;Kestrel má omezená podpora HTTP/2 na Windows Serveru 2012 R2 a Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="55a68-124">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="55a68-125">Podpora je omezená, protože seznam podporovaných šifrovacích sad TLS, které jsou k dispozici v těchto operačních systémech, je omezený.</span><span class="sxs-lookup"><span data-stu-id="55a68-125">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="55a68-126">Pro zabezpečení připojení TLS může být vyžadován certifikát vygenerovaný pomocí algoritmu ECDSA (s připojením typu eliptická křivka).</span><span class="sxs-lookup"><span data-stu-id="55a68-126">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="55a68-127">Pokud je navázáno připojení HTTP/2, sestavy [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="55a68-127">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="55a68-128">HTTP/2 je ve výchozím nastavení zakázané.</span><span class="sxs-lookup"><span data-stu-id="55a68-128">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="55a68-129">Další informace o konfiguraci najdete v částech [Možnosti Kestrel](#kestrel-options) a [ListenOptions. Protocols](#listenoptionsprotocols) .</span><span class="sxs-lookup"><span data-stu-id="55a68-129">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="55a68-130">Kdy použít Kestrel s reverzním proxy serverem</span><span class="sxs-lookup"><span data-stu-id="55a68-130">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="55a68-131">Kestrel může používat sám sebe nebo s *reverzními proxy server*, jako je [Internetová informační služba (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="55a68-131">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="55a68-132">Reverzní proxy server přijímá požadavky HTTP ze sítě a přesměruje je na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="55a68-132">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="55a68-133">Kestrel použitý jako hraniční webový server (internetový):</span><span class="sxs-lookup"><span data-stu-id="55a68-133">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel komunikuje přímo s internetem bez zpětného proxy server](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="55a68-135">Kestrel se používá v konfiguraci reverzního proxy serveru:</span><span class="sxs-lookup"><span data-stu-id="55a68-135">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel nepřímo komunikuje s internetem prostřednictvím reverzní proxy server, jako je IIS, Nginx nebo Apache.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="55a68-137">Buď konfigurace, s zpětným proxy server nebo bez něj, je podporovaná konfigurace hostování.</span><span class="sxs-lookup"><span data-stu-id="55a68-137">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="55a68-138">Kestrel používané jako hraniční Server bez reverzních proxy server nepodporuje sdílení stejné IP adresy a portu mezi více procesy.</span><span class="sxs-lookup"><span data-stu-id="55a68-138">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="55a68-139">Když je Kestrel nakonfigurovaný k naslouchání na portu, Kestrel zpracovává veškerý provoz tohoto portu bez ohledu na hlavičky `Host`.</span><span class="sxs-lookup"><span data-stu-id="55a68-139">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="55a68-140">Reverzní proxy server, který může sdílet porty, má schopnost přesměrovat požadavky na Kestrel na jedinečné IP adresy a porty.</span><span class="sxs-lookup"><span data-stu-id="55a68-140">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="55a68-141">I v případě, že se zpětný proxy server nepožaduje, může být použití reverzní proxy server vhodnou volbou.</span><span class="sxs-lookup"><span data-stu-id="55a68-141">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="55a68-142">Reverzní proxy:</span><span class="sxs-lookup"><span data-stu-id="55a68-142">A reverse proxy:</span></span>

* <span data-ttu-id="55a68-143">Může omezit vystavenou veřejnou plochu v aplikacích, které hostuje.</span><span class="sxs-lookup"><span data-stu-id="55a68-143">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="55a68-144">Poskytněte další vrstvu konfigurace a obrany.</span><span class="sxs-lookup"><span data-stu-id="55a68-144">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="55a68-145">Může se lépe integrovat se stávající infrastrukturou.</span><span class="sxs-lookup"><span data-stu-id="55a68-145">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="55a68-146">Zjednodušte vyrovnávání zatížení a konfiguraci zabezpečené komunikace (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="55a68-146">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="55a68-147">Pouze reverzní proxy server vyžaduje certifikát X. 509 a tento server může komunikovat se servery aplikace v interní síti pomocí prostého protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="55a68-147">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="55a68-148">Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="55a68-148">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="55a68-149">Použití Kestrel v aplikacích ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="55a68-149">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="55a68-150">ASP.NET Core šablony projektu ve výchozím nastavení používají Kestrel.</span><span class="sxs-lookup"><span data-stu-id="55a68-150">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="55a68-151">V *program.cs*aplikace volá `ConfigureWebHostDefaults`, která volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> na pozadí.</span><span class="sxs-lookup"><span data-stu-id="55a68-151">In *Program.cs*, the app calls `ConfigureWebHostDefaults`, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="55a68-152">Další informace o vytváření hostitele naleznete v části nastavení *hostitele* a *výchozí nastavení tvůrce* <xref:fundamentals/host/generic-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="55a68-152">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="55a68-153">K poskytnutí další konfigurace po volání `ConfigureWebHostDefaults`použijte `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="55a68-153">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="55a68-154">Kestrel možnosti</span><span class="sxs-lookup"><span data-stu-id="55a68-154">Kestrel options</span></span>

<span data-ttu-id="55a68-155">Webový server Kestrel má možnosti konfigurace omezení, které jsou zvláště užitečné v internetových nasazeních.</span><span class="sxs-lookup"><span data-stu-id="55a68-155">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="55a68-156">Nastavte omezení pro vlastnost <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> třídy <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="55a68-156">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="55a68-157">Vlastnost `Limits` obsahuje instanci třídy <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="55a68-157">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="55a68-158">V následujících příkladech se používá obor názvů <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:</span><span class="sxs-lookup"><span data-stu-id="55a68-158">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="55a68-159">Možnosti Kestrel, které jsou konfigurovány C# v kódu v následujících příkladech, lze také nastavit pomocí [poskytovatele konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="55a68-159">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="55a68-160">Poskytovatel konfigurace souboru může například načíst konfiguraci Kestrel z objektu *appSettings. JSON* nebo *appSettings. { Soubor Environment}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="55a68-160">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    },
    "DisableStringReuse": true
  }
}
```

<span data-ttu-id="55a68-161">Použijte **jeden** z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="55a68-161">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="55a68-162">Konfigurace Kestrel v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="55a68-162">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="55a68-163">Vloží instanci `IConfiguration` do třídy `Startup`.</span><span class="sxs-lookup"><span data-stu-id="55a68-163">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="55a68-164">Následující příklad předpokládá, že vložená konfigurace je přiřazena vlastnosti `Configuration`.</span><span class="sxs-lookup"><span data-stu-id="55a68-164">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="55a68-165">V `Startup.ConfigureServices` načtěte oddíl `Kestrel` konfigurace do konfigurace Kestrel.</span><span class="sxs-lookup"><span data-stu-id="55a68-165">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration.</span></span>

     ```csharp
     // using Microsoft.Extensions.Configuration

     public void ConfigureServices(IServiceCollection services)
     {
         services.Configure<KestrelServerOptions>(
             Configuration.GetSection("Kestrel"));
     }
     ```

* <span data-ttu-id="55a68-166">Nakonfigurovat Kestrel při sestavování hostitele:</span><span class="sxs-lookup"><span data-stu-id="55a68-166">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="55a68-167">V *program.cs*do konfigurace Kestrel načtěte oddíl `Kestrel` konfigurace:</span><span class="sxs-lookup"><span data-stu-id="55a68-167">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IHostBuilder CreateHostBuilder(string[] args) =>
      Host.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .ConfigureWebHostDefaults(webBuilder =>
          {
              webBuilder.UseStartup<Startup>();
          });
  ```

<span data-ttu-id="55a68-168">Obě předchozí přístupy fungují u libovolného [poskytovatele konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="55a68-168">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="55a68-169">Časový limit Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="55a68-169">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="55a68-170">Získá nebo nastaví [časový limit zachování](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="55a68-170">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="55a68-171">Výchozí hodnota je 2 minuty.</span><span class="sxs-lookup"><span data-stu-id="55a68-171">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="55a68-172">Maximální počet připojení klientů</span><span class="sxs-lookup"><span data-stu-id="55a68-172">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="55a68-173">Maximální počet souběžných otevřených připojení TCP lze pro celou aplikaci nastavit pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="55a68-173">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="55a68-174">Existuje zvláštní omezení pro připojení, která byla upgradována z protokolu HTTP nebo HTTPS na jiný protokol (například na žádost WebSockets).</span><span class="sxs-lookup"><span data-stu-id="55a68-174">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="55a68-175">Po upgradu připojení se nepočítá s limitem `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="55a68-175">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="55a68-176">Ve výchozím nastavení je maximální počet připojení neomezené (null).</span><span class="sxs-lookup"><span data-stu-id="55a68-176">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="55a68-177">Maximální velikost textu požadavku</span><span class="sxs-lookup"><span data-stu-id="55a68-177">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="55a68-178">Výchozí maximální velikost textu požadavku je 30 000 000 bajtů, což je přibližně 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="55a68-178">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="55a68-179">Doporučený postup pro přepsání limitu v aplikaci ASP.NET Core MVC je použití atributu <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> u metody akce:</span><span class="sxs-lookup"><span data-stu-id="55a68-179">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="55a68-180">Tady je příklad, který ukazuje, jak nakonfigurovat omezení pro aplikaci na každém požadavku:</span><span class="sxs-lookup"><span data-stu-id="55a68-180">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="55a68-181">Přepsat nastavení u konkrétního požadavku v middleware:</span><span class="sxs-lookup"><span data-stu-id="55a68-181">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="55a68-182">Výjimka je vyvolána, pokud aplikace po zahájení aplikace nakonfiguruje limit na žádost.</span><span class="sxs-lookup"><span data-stu-id="55a68-182">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="55a68-183">Existuje vlastnost `IsReadOnly`, která určuje, zda je vlastnost `MaxRequestBodySize` ve stavu jen pro čtení, což znamená, že je příliš pozdě pro konfiguraci limitu.</span><span class="sxs-lookup"><span data-stu-id="55a68-183">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="55a68-184">Když je aplikace spouštěna [mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za [ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module), je omezení velikosti textu požadavku Kestrel zakázané, protože služba IIS tento limit již nastavuje.</span><span class="sxs-lookup"><span data-stu-id="55a68-184">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="55a68-185">Minimální rychlost těla zprávy požadavku</span><span class="sxs-lookup"><span data-stu-id="55a68-185">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="55a68-186">Kestrel kontroluje každou sekundu, pokud data přicházejí za zadanou rychlost v bajtech za sekundu.</span><span class="sxs-lookup"><span data-stu-id="55a68-186">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="55a68-187">Pokud frekvence klesne pod minimální hodnotu, vypršel časový limit připojení. Doba odkladu je doba, kterou Kestrel umožňuje klientovi zvýšit rychlost odesílání až na minimum; frekvence není během této doby kontrolována.</span><span class="sxs-lookup"><span data-stu-id="55a68-187">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="55a68-188">Doba odkladu pomáhá vyhnout se vyřazení připojení, která při počátečním posílání dat zpomalují rychlost, kvůli pomalému spuštění protokolu TCP.</span><span class="sxs-lookup"><span data-stu-id="55a68-188">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="55a68-189">Výchozí minimální sazba je 240 bajtů za sekundu s obdobím 5 sekund odkladu.</span><span class="sxs-lookup"><span data-stu-id="55a68-189">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="55a68-190">Pro odpověď platí také minimální sazba.</span><span class="sxs-lookup"><span data-stu-id="55a68-190">A minimum rate also applies to the response.</span></span> <span data-ttu-id="55a68-191">Kód pro nastavení limitu požadavků a limit odpovědi je stejný s výjimkou `RequestBody` nebo `Response` v názvech vlastností a rozhraní.</span><span class="sxs-lookup"><span data-stu-id="55a68-191">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="55a68-192">Tady je příklad, který ukazuje, jak nakonfigurovat minimální datové tarify v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="55a68-192">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="55a68-193">Přepsat minimální limity přenosové rychlosti na žádost v middlewaru:</span><span class="sxs-lookup"><span data-stu-id="55a68-193">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="55a68-194"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature>, na které se odkazuje v předchozí ukázce, není k dispozici v `HttpContext.Features` pro požadavky HTTP/2, protože Změna omezení přenosové rychlosti u jednotlivých požadavků je obecně Nepodporovaná pro HTTP/2 kvůli podpoře protokolu pro multiplexování požadavků.</span><span class="sxs-lookup"><span data-stu-id="55a68-194">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="55a68-195"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> však stále existují `HttpContext.Features` požadavků HTTP/2, protože omezení četnosti čtení je stále možné *úplně zakázat* na základě jednotlivých požadavků nastavením `IHttpMinRequestBodyDataRateFeature.MinDataRate` na `null` i pro požadavek HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="55a68-195">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="55a68-196">Při pokusu o čtení `IHttpMinRequestBodyDataRateFeature.MinDataRate` nebo pokusu o její nastavení na jinou hodnotu než `null` bude výsledkem `NotSupportedException` vyjímka požadavku HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="55a68-196">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="55a68-197">Omezení přenosové rychlosti pro všechny servery nakonfigurovaná přes `KestrelServerOptions.Limits` se stále vztahují na připojení HTTP/1. x i HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="55a68-197">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="55a68-198">Časový limit hlaviček žádostí</span><span class="sxs-lookup"><span data-stu-id="55a68-198">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="55a68-199">Získá nebo nastaví maximální dobu, po kterou server stráví příjem hlaviček požadavků.</span><span class="sxs-lookup"><span data-stu-id="55a68-199">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="55a68-200">Výchozí hodnota je 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="55a68-200">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="55a68-201">Maximální počet proudů na připojení</span><span class="sxs-lookup"><span data-stu-id="55a68-201">Maximum streams per connection</span></span>

<span data-ttu-id="55a68-202">`Http2.MaxStreamsPerConnection` omezuje počet souběžných proudů požadavků na připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="55a68-202">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="55a68-203">Nadbytečné proudy jsou odmítnuty.</span><span class="sxs-lookup"><span data-stu-id="55a68-203">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="55a68-204">Výchozí hodnota je 100.</span><span class="sxs-lookup"><span data-stu-id="55a68-204">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="55a68-205">Velikost tabulky hlaviček</span><span class="sxs-lookup"><span data-stu-id="55a68-205">Header table size</span></span>

<span data-ttu-id="55a68-206">Dekodér HPACK dekomprimuje hlavičky HTTP pro připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="55a68-206">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="55a68-207">`Http2.HeaderTableSize` omezuje velikost kompresní tabulky hlaviček, kterou používá dekodér HPACK.</span><span class="sxs-lookup"><span data-stu-id="55a68-207">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="55a68-208">Hodnota je uvedena v oktetech a musí být větší než nula (0).</span><span class="sxs-lookup"><span data-stu-id="55a68-208">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="55a68-209">Výchozí hodnota je 4096.</span><span class="sxs-lookup"><span data-stu-id="55a68-209">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="55a68-210">Maximální velikost rámce</span><span class="sxs-lookup"><span data-stu-id="55a68-210">Maximum frame size</span></span>

<span data-ttu-id="55a68-211">`Http2.MaxFrameSize` označuje maximální povolenou velikost datové části rámce připojení HTTP/2 přijatého nebo odesílaného serverem.</span><span class="sxs-lookup"><span data-stu-id="55a68-211">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="55a68-212">Hodnota je uvedena v oktetech a musí být v rozmezí 2 ^ 14 (16 384) až 2 ^ 24-1 (16 777 215).</span><span class="sxs-lookup"><span data-stu-id="55a68-212">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="55a68-213">Výchozí hodnota je 2 ^ 14 (16 384).</span><span class="sxs-lookup"><span data-stu-id="55a68-213">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="55a68-214">Maximální velikost hlavičky požadavku</span><span class="sxs-lookup"><span data-stu-id="55a68-214">Maximum request header size</span></span>

<span data-ttu-id="55a68-215">`Http2.MaxRequestHeaderFieldSize` označuje maximální povolenou velikost v oktetech hodnot hlaviček požadavku.</span><span class="sxs-lookup"><span data-stu-id="55a68-215">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="55a68-216">Toto omezení se vztahuje na název i hodnotu v jejich komprimovaných a nekomprimovaných reprezentujcích.</span><span class="sxs-lookup"><span data-stu-id="55a68-216">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="55a68-217">Hodnota musí být větší než nula (0).</span><span class="sxs-lookup"><span data-stu-id="55a68-217">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="55a68-218">Výchozí hodnota je 8 192.</span><span class="sxs-lookup"><span data-stu-id="55a68-218">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="55a68-219">Velikost okna počátečního připojení</span><span class="sxs-lookup"><span data-stu-id="55a68-219">Initial connection window size</span></span>

<span data-ttu-id="55a68-220">`Http2.InitialConnectionWindowSize` označuje maximální velikost textu žádosti v bajtech. vyrovnávací paměti serveru se v jednom okamžiku agreguje napříč všemi požadavky (datovými proudy) na připojení.</span><span class="sxs-lookup"><span data-stu-id="55a68-220">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="55a68-221">Žádosti jsou také omezené pomocí `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="55a68-221">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="55a68-222">Hodnota musí být větší než nebo rovna 65 535 a menší než 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="55a68-222">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="55a68-223">Výchozí hodnota je 128 KB (131 072).</span><span class="sxs-lookup"><span data-stu-id="55a68-223">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="55a68-224">Počáteční velikost okna datového proudu</span><span class="sxs-lookup"><span data-stu-id="55a68-224">Initial stream window size</span></span>

<span data-ttu-id="55a68-225">`Http2.InitialStreamWindowSize` označuje maximální velikost textu požadavku v bajtech na jednu žádost (Stream) na jednom místě.</span><span class="sxs-lookup"><span data-stu-id="55a68-225">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="55a68-226">Žádosti jsou také omezené pomocí `Http2.InitialConnectionWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="55a68-226">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="55a68-227">Hodnota musí být větší než nebo rovna 65 535 a menší než 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="55a68-227">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="55a68-228">Výchozí hodnota je 96 KB (98 304).</span><span class="sxs-lookup"><span data-stu-id="55a68-228">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="55a68-229">Synchronní v/v</span><span class="sxs-lookup"><span data-stu-id="55a68-229">Synchronous IO</span></span>

<span data-ttu-id="55a68-230"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> řídí, zda je pro požadavek a odpověď povolena synchronní v/v.</span><span class="sxs-lookup"><span data-stu-id="55a68-230"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="55a68-231">Výchozí hodnota je `false`.</span><span class="sxs-lookup"><span data-stu-id="55a68-231">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="55a68-232">Velký počet blokování synchronních vstupně-výstupních operací může vést ke vyčerpání fondu vláken, což aplikaci nereaguje.</span><span class="sxs-lookup"><span data-stu-id="55a68-232">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="55a68-233">Při použití knihovny, která nepodporuje asynchronní vstupně-výstupní operace, povolte pouze `AllowSynchronousIO`.</span><span class="sxs-lookup"><span data-stu-id="55a68-233">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="55a68-234">Následující příklad povoluje synchronní vstupně-výstupní operace:</span><span class="sxs-lookup"><span data-stu-id="55a68-234">The following example enables synchronous IO:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="55a68-235">Informace o dalších možnostech a omezeních Kestrel najdete v tématech:</span><span class="sxs-lookup"><span data-stu-id="55a68-235">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="55a68-236">Konfigurace koncového bodu</span><span class="sxs-lookup"><span data-stu-id="55a68-236">Endpoint configuration</span></span>

<span data-ttu-id="55a68-237">Ve výchozím nastavení ASP.NET Core váže k:</span><span class="sxs-lookup"><span data-stu-id="55a68-237">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="55a68-238">`https://localhost:5001` (Pokud je k dispozici místní vývojový certifikát)</span><span class="sxs-lookup"><span data-stu-id="55a68-238">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="55a68-239">Zadejte adresy URL pomocí:</span><span class="sxs-lookup"><span data-stu-id="55a68-239">Specify URLs using the:</span></span>

* <span data-ttu-id="55a68-240">`ASPNETCORE_URLS` proměnnou prostředí.</span><span class="sxs-lookup"><span data-stu-id="55a68-240">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="55a68-241">argument příkazového řádku `--urls`</span><span class="sxs-lookup"><span data-stu-id="55a68-241">`--urls` command-line argument.</span></span>
* <span data-ttu-id="55a68-242">`urls` konfigurační klíč hostitele.</span><span class="sxs-lookup"><span data-stu-id="55a68-242">`urls` host configuration key.</span></span>
* <span data-ttu-id="55a68-243">Metoda rozšíření `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="55a68-243">`UseUrls` extension method.</span></span>

<span data-ttu-id="55a68-244">Hodnota zadaná pomocí těchto přístupů může být jeden nebo více koncových bodů HTTP a HTTPS (HTTPS, pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="55a68-244">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="55a68-245">Nakonfigurujte hodnotu jako seznam oddělený středníkem (například `"Urls": "http://localhost:8000; http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="55a68-245">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="55a68-246">Další informace o těchto přístupůch najdete v tématu [adresy URL serveru](xref:fundamentals/host/web-host#server-urls) a [potlačení konfigurace](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="55a68-246">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="55a68-247">Vytvoří se vývojový certifikát:</span><span class="sxs-lookup"><span data-stu-id="55a68-247">A development certificate is created:</span></span>

* <span data-ttu-id="55a68-248">Po instalaci [.NET Core SDK](/dotnet/core/sdk) .</span><span class="sxs-lookup"><span data-stu-id="55a68-248">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="55a68-249">[Nástroj pro vývoj a certifikáty](xref:aspnetcore-2.1#https) slouží k vytvoření certifikátu.</span><span class="sxs-lookup"><span data-stu-id="55a68-249">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="55a68-250">Některé prohlížeče vyžadují udělení explicitního oprávnění pro důvěřování místního vývojového certifikátu.</span><span class="sxs-lookup"><span data-stu-id="55a68-250">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="55a68-251">Šablony projektů konfigurují aplikace tak, aby běžely ve výchozím nastavení HTTPS a zahrnovaly [přesměrování https a podporu HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="55a68-251">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="55a68-252">Pokud chcete nakonfigurovat předpony a porty adres URL pro Kestrel, volejte <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> nebo <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> na <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="55a68-252">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="55a68-253">`UseUrls`, argument příkazového řádku `--urls`, `urls` konfigurační klíč hostitele a proměnná prostředí `ASPNETCORE_URLS` fungují, ale mají omezení zaznamenaná později v této části (pro konfiguraci koncového bodu HTTPS musí být k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="55a68-253">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="55a68-254">Konfigurace `KestrelServerOptions`:</span><span class="sxs-lookup"><span data-stu-id="55a68-254">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="55a68-255">ConfigureEndpointDefaults (Action\<ListenOptions >)</span><span class="sxs-lookup"><span data-stu-id="55a68-255">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="55a68-256">Určuje konfiguraci `Action`, která se má spustit pro každý zadaný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="55a68-256">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="55a68-257">Volání `ConfigureEndpointDefaults` několikrát nahrazuje předchozí `Action` s určeným posledním `Action`.</span><span class="sxs-lookup"><span data-stu-id="55a68-257">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="55a68-258">ConfigureHttpsDefaults (Action\<HttpsConnectionAdapterOptions >)</span><span class="sxs-lookup"><span data-stu-id="55a68-258">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="55a68-259">Určuje konfiguraci `Action` pro spuštění každého koncového bodu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="55a68-259">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="55a68-260">Volání `ConfigureHttpsDefaults` několikrát nahrazuje předchozí `Action` s určeným posledním `Action`.</span><span class="sxs-lookup"><span data-stu-id="55a68-260">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        // certificate is an X509Certificate2
        listenOptions.ServerCertificate = certificate;
    });
});
```

### <a name="configureiconfiguration"></a><span data-ttu-id="55a68-261">Konfigurace (IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="55a68-261">Configure(IConfiguration)</span></span>

<span data-ttu-id="55a68-262">Vytvoří zavaděč konfigurace pro nastavení Kestrel, který jako vstup převezme <xref:Microsoft.Extensions.Configuration.IConfiguration>.</span><span class="sxs-lookup"><span data-stu-id="55a68-262">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="55a68-263">Konfigurace musí být vymezená na konfigurační oddíl pro Kestrel.</span><span class="sxs-lookup"><span data-stu-id="55a68-263">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="55a68-264">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="55a68-264">ListenOptions.UseHttps</span></span>

<span data-ttu-id="55a68-265">Nakonfigurujte Kestrel tak, aby používal protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="55a68-265">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="55a68-266">rozšíření `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="55a68-266">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="55a68-267">`UseHttps` &ndash; nakonfigurujte Kestrel tak, aby používal HTTPS s výchozím certifikátem.</span><span class="sxs-lookup"><span data-stu-id="55a68-267">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="55a68-268">Vyvolá výjimku, pokud není nakonfigurován žádný výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="55a68-268">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="55a68-269">parametry `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="55a68-269">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="55a68-270">`filename` je cesta a název souboru certifikátu vzhledem k adresáři, který obsahuje soubory obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="55a68-270">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="55a68-271">`password` je heslo vyžadované pro přístup k datům certifikátu X. 509.</span><span class="sxs-lookup"><span data-stu-id="55a68-271">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="55a68-272">`configureOptions` je `Action` ke konfiguraci `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="55a68-272">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="55a68-273">Vrátí `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="55a68-273">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="55a68-274">`storeName` je úložiště certifikátů, ze kterého se má certifikát načíst.</span><span class="sxs-lookup"><span data-stu-id="55a68-274">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="55a68-275">`subject` je název subjektu certifikátu.</span><span class="sxs-lookup"><span data-stu-id="55a68-275">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="55a68-276">`allowInvalid` označuje, zda by měly být zváženy neplatné certifikáty, například certifikáty podepsané svým držitelem.</span><span class="sxs-lookup"><span data-stu-id="55a68-276">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="55a68-277">`location` je umístění úložiště, ze kterého se má načíst certifikát.</span><span class="sxs-lookup"><span data-stu-id="55a68-277">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="55a68-278">`serverCertificate` je certifikát X. 509.</span><span class="sxs-lookup"><span data-stu-id="55a68-278">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="55a68-279">V produkčním prostředí musí být HTTPS explicitně nakonfigurovaný.</span><span class="sxs-lookup"><span data-stu-id="55a68-279">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="55a68-280">Musí být zadán minimálně výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="55a68-280">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="55a68-281">Podporované konfigurace popsané dál:</span><span class="sxs-lookup"><span data-stu-id="55a68-281">Supported configurations described next:</span></span>

* <span data-ttu-id="55a68-282">Žádná konfigurace</span><span class="sxs-lookup"><span data-stu-id="55a68-282">No configuration</span></span>
* <span data-ttu-id="55a68-283">Nahradit výchozí certifikát z konfigurace</span><span class="sxs-lookup"><span data-stu-id="55a68-283">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="55a68-284">Změna výchozích hodnot v kódu</span><span class="sxs-lookup"><span data-stu-id="55a68-284">Change the defaults in code</span></span>

<span data-ttu-id="55a68-285">*Žádná konfigurace*</span><span class="sxs-lookup"><span data-stu-id="55a68-285">*No configuration*</span></span>

<span data-ttu-id="55a68-286">Kestrel naslouchá na `http://localhost:5000` a `https://localhost:5001` (Pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="55a68-286">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="55a68-287">*Nahradit výchozí certifikát z konfigurace*</span><span class="sxs-lookup"><span data-stu-id="55a68-287">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="55a68-288">`CreateDefaultBuilder` volá ve výchozím nastavení `Configure(context.Configuration.GetSection("Kestrel"))`, aby se načetla konfigurace Kestrel.</span><span class="sxs-lookup"><span data-stu-id="55a68-288">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="55a68-289">Pro Kestrel je k dispozici výchozí schéma konfigurace nastavení aplikace HTTPS.</span><span class="sxs-lookup"><span data-stu-id="55a68-289">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="55a68-290">Nakonfigurujte několik koncových bodů, včetně adres URL a certifikátů, které se mají použít, buď ze souboru na disku, nebo z úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="55a68-290">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="55a68-291">V následujícím příkladu *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="55a68-291">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="55a68-292">Nastavte **AllowInvalid** na `true`, pokud chcete povolit použití neplatných certifikátů (například certifikátů podepsaných svým držitelem).</span><span class="sxs-lookup"><span data-stu-id="55a68-292">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="55a68-293">Libovolný koncový bod HTTPS, který neurčuje certifikát (**HttpsDefaultCert** v následujícím příkladu), se vrátí k certifikátu definovanému v části **certifikáty** > **výchozí** nebo vývojový certifikát.</span><span class="sxs-lookup"><span data-stu-id="55a68-293">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="55a68-294">Alternativou k použití **cesty** a **hesla** pro libovolný uzel certifikátu je zadání certifikátu pomocí polí úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="55a68-294">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="55a68-295">Například **certifikáty** > **výchozí** certifikát lze zadat jako:</span><span class="sxs-lookup"><span data-stu-id="55a68-295">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="55a68-296">Poznámky ke schématu:</span><span class="sxs-lookup"><span data-stu-id="55a68-296">Schema notes:</span></span>

* <span data-ttu-id="55a68-297">V názvech koncových bodů se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="55a68-297">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="55a68-298">Například `HTTPS` a `Https` jsou platné.</span><span class="sxs-lookup"><span data-stu-id="55a68-298">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="55a68-299">U každého koncového bodu je vyžadován parametr `Url`.</span><span class="sxs-lookup"><span data-stu-id="55a68-299">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="55a68-300">Formát tohoto parametru je stejný jako konfigurační parametr `Urls` nejvyšší úrovně s tím rozdílem, že je omezen na jedinou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="55a68-300">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="55a68-301">Tyto koncové body nahrazují hodnoty definované v konfiguraci `Urls` na nejvyšší úrovni místo jejich přidávání do těchto koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="55a68-301">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="55a68-302">Koncové body definované v kódu prostřednictvím `Listen` jsou kumulativní s koncovými body definovanými v konfiguračním oddílu.</span><span class="sxs-lookup"><span data-stu-id="55a68-302">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="55a68-303">Oddíl `Certificate` je nepovinný.</span><span class="sxs-lookup"><span data-stu-id="55a68-303">The `Certificate` section is optional.</span></span> <span data-ttu-id="55a68-304">Pokud se nezadá oddíl `Certificate`, použijí se výchozí hodnoty definované v dřívějších scénářích.</span><span class="sxs-lookup"><span data-stu-id="55a68-304">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="55a68-305">Pokud nejsou k dispozici žádné výchozí hodnoty, Server vyvolá výjimku a nemůže se spustit.</span><span class="sxs-lookup"><span data-stu-id="55a68-305">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="55a68-306">Oddíl `Certificate` podporuje jak **cesty**&ndash;**hesla** , tak&ndash;ho **úložiště** **subjektu** .</span><span class="sxs-lookup"><span data-stu-id="55a68-306">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="55a68-307">V takovém případě může být definován libovolný počet koncových bodů, pokud to nezpůsobí konflikty portů.</span><span class="sxs-lookup"><span data-stu-id="55a68-307">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="55a68-308">`options.Configure(context.Configuration.GetSection("{SECTION}"))` vrátí `KestrelConfigurationLoader` s metodou `.Endpoint(string name, listenOptions => { })`, která se dá použít k doplnění nastavení nakonfigurovaného koncového bodu:</span><span class="sxs-lookup"><span data-stu-id="55a68-308">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

```csharp
webBuilder.UseKestrel((context, serverOptions) =>
{
    serverOptions.Configure(context.Configuration.GetSection("Kestrel"))
        .Endpoint("HTTPS", listenOptions =>
        {
            listenOptions.HttpsOptions.SslProtocols = SslProtocols.Tls12;
        });
});
```

<span data-ttu-id="55a68-309">`KestrelServerOptions.ConfigurationLoader` se dá získat přímo k pokračování v iteraci u stávajícího zavaděče, jako je třeba ten, který poskytuje <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="55a68-309">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="55a68-310">Konfigurační oddíl pro každý koncový bod je k dispozici na možnostech v metodě `Endpoint`, aby bylo možné číst vlastní nastavení.</span><span class="sxs-lookup"><span data-stu-id="55a68-310">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="55a68-311">Více konfigurací může být načteno voláním `options.Configure(context.Configuration.GetSection("{SECTION}"))` znovu s jiným oddílem.</span><span class="sxs-lookup"><span data-stu-id="55a68-311">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="55a68-312">Je použita pouze poslední konfigurace, pokud není metoda `Load` explicitně volána pro předchozí instance.</span><span class="sxs-lookup"><span data-stu-id="55a68-312">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="55a68-313">Metapackage nevolá `Load`, takže jeho výchozí konfigurační oddíl může být nahrazen.</span><span class="sxs-lookup"><span data-stu-id="55a68-313">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="55a68-314">`KestrelConfigurationLoader` zrcadlí `Listen` rodinu rozhraní API od `KestrelServerOptions` jako přetížení `Endpoint`, takže koncové body kódu a konfigurace lze konfigurovat na stejném místě.</span><span class="sxs-lookup"><span data-stu-id="55a68-314">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="55a68-315">Tato přetížení nepoužívají názvy a využívají pouze výchozí nastavení z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="55a68-315">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="55a68-316">*Změna výchozích hodnot v kódu*</span><span class="sxs-lookup"><span data-stu-id="55a68-316">*Change the defaults in code*</span></span>

<span data-ttu-id="55a68-317">`ConfigureEndpointDefaults` a `ConfigureHttpsDefaults` lze použít ke změně výchozího nastavení pro `ListenOptions` a `HttpsConnectionAdapterOptions`, včetně přepsání výchozího certifikátu zadaného v předchozím scénáři.</span><span class="sxs-lookup"><span data-stu-id="55a68-317">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="55a68-318">`ConfigureEndpointDefaults` a `ConfigureHttpsDefaults` by měly být volány před konfigurací koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="55a68-318">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

```csharp
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
```

<span data-ttu-id="55a68-319">*Podpora Kestrel pro SNI*</span><span class="sxs-lookup"><span data-stu-id="55a68-319">*Kestrel support for SNI*</span></span>

<span data-ttu-id="55a68-320">[Indikace názvu serveru (SNI)](https://tools.ietf.org/html/rfc6066#section-3) lze použít k hostování více domén na stejné IP adrese a portu.</span><span class="sxs-lookup"><span data-stu-id="55a68-320">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="55a68-321">Aby SNI fungovalo, klient pošle název hostitele pro zabezpečenou relaci serveru během metody handshake TLS, aby server mohl poskytovat správný certifikát.</span><span class="sxs-lookup"><span data-stu-id="55a68-321">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="55a68-322">Klient používá poskytnutý certifikát pro šifrovanou komunikaci se serverem během zabezpečené relace, která následuje za metodou handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="55a68-322">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="55a68-323">Kestrel podporuje SNI prostřednictvím zpětného volání `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="55a68-323">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="55a68-324">Zpětné volání je vyvoláno jednou za připojení, které aplikaci umožní zkontrolovat název hostitele a vybrat příslušný certifikát.</span><span class="sxs-lookup"><span data-stu-id="55a68-324">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="55a68-325">Podpora SNI vyžaduje:</span><span class="sxs-lookup"><span data-stu-id="55a68-325">SNI support requires:</span></span>

* <span data-ttu-id="55a68-326">Spuštění v cílovém rozhraní `netcoreapp2.1` nebo novější.</span><span class="sxs-lookup"><span data-stu-id="55a68-326">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="55a68-327">V `net461` nebo novějším je zpětné volání vyvoláno, ale `name` je vždy `null`.</span><span class="sxs-lookup"><span data-stu-id="55a68-327">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="55a68-328">`name` taky `null`, pokud klient neposkytne parametr názvu hostitele v handshaki TLS.</span><span class="sxs-lookup"><span data-stu-id="55a68-328">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="55a68-329">Všechny weby běží na stejné instanci Kestrel.</span><span class="sxs-lookup"><span data-stu-id="55a68-329">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="55a68-330">Kestrel nepodporuje sdílení IP adresy a portu napříč několika instancemi bez reverzního proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="55a68-330">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

```csharp
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
});
```

### <a name="connection-logging"></a><span data-ttu-id="55a68-331">Protokolování připojení</span><span class="sxs-lookup"><span data-stu-id="55a68-331">Connection logging</span></span>

<span data-ttu-id="55a68-332">Volání <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> k vygenerování protokolů na úrovni ladění pro komunikaci na úrovni bajtů v rámci připojení.</span><span class="sxs-lookup"><span data-stu-id="55a68-332">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="55a68-333">Protokolování připojení je užitečné při řešení problémů v komunikaci nízké úrovně, například během šifrování TLS a za proxy servery.</span><span class="sxs-lookup"><span data-stu-id="55a68-333">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="55a68-334">Pokud je `UseConnectionLogging` před `UseHttps`, zaprotokoluje se zašifrovaný provoz.</span><span class="sxs-lookup"><span data-stu-id="55a68-334">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="55a68-335">Je-li po `UseHttps`umístěn `UseConnectionLogging`, zašifrovaný provoz se zaznamená do protokolu.</span><span class="sxs-lookup"><span data-stu-id="55a68-335">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="55a68-336">Vytvoření vazby na soket TCP</span><span class="sxs-lookup"><span data-stu-id="55a68-336">Bind to a TCP socket</span></span>

<span data-ttu-id="55a68-337">Metoda <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> se váže k soketu TCP a obslužná rutina options umožňuje konfiguraci certifikátu X. 509:</span><span class="sxs-lookup"><span data-stu-id="55a68-337">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="55a68-338">Příklad konfiguruje HTTPS pro koncový bod s <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="55a68-338">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="55a68-339">Ke konfiguraci dalších nastavení Kestrel pro konkrétní koncové body použijte stejné rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="55a68-339">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="55a68-340">Vytvoření vazby na soket UNIX</span><span class="sxs-lookup"><span data-stu-id="55a68-340">Bind to a Unix socket</span></span>

<span data-ttu-id="55a68-341">Naslouchat na soketu se systémem UNIX pomocí <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> pro zlepšení výkonu pomocí Nginx, jak je znázorněno v tomto příkladu:</span><span class="sxs-lookup"><span data-stu-id="55a68-341">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

### <a name="port-0"></a><span data-ttu-id="55a68-342">Port 0</span><span class="sxs-lookup"><span data-stu-id="55a68-342">Port 0</span></span>

<span data-ttu-id="55a68-343">Když je zadané číslo portu `0`, dynamicky se váže k dostupnému portu.</span><span class="sxs-lookup"><span data-stu-id="55a68-343">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="55a68-344">Následující příklad ukazuje, jak určit, který port Kestrel je skutečně vázaný za běhu:</span><span class="sxs-lookup"><span data-stu-id="55a68-344">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="55a68-345">Když se aplikace spustí, výstup okna konzoly indikuje dynamický port, na který se aplikace dá získat:</span><span class="sxs-lookup"><span data-stu-id="55a68-345">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="55a68-346">Omezení</span><span class="sxs-lookup"><span data-stu-id="55a68-346">Limitations</span></span>

<span data-ttu-id="55a68-347">Konfigurace koncových bodů pomocí následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="55a68-347">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="55a68-348">argument příkazového řádku `--urls`</span><span class="sxs-lookup"><span data-stu-id="55a68-348">`--urls` command-line argument</span></span>
* <span data-ttu-id="55a68-349">konfigurační klíč hostitele `urls`</span><span class="sxs-lookup"><span data-stu-id="55a68-349">`urls` host configuration key</span></span>
* <span data-ttu-id="55a68-350">`ASPNETCORE_URLS` proměnná prostředí</span><span class="sxs-lookup"><span data-stu-id="55a68-350">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="55a68-351">Tyto metody jsou užitečné, pokud chcete, aby kód pracoval na jiných serverech než Kestrel.</span><span class="sxs-lookup"><span data-stu-id="55a68-351">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="55a68-352">Mějte ale na paměti následující omezení:</span><span class="sxs-lookup"><span data-stu-id="55a68-352">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="55a68-353">Protokol HTTPS nelze s těmito přístupy použít, pokud není k dispozici výchozí certifikát v konfiguraci koncového bodu HTTPS (například pomocí konfigurace `KestrelServerOptions` nebo konfiguračního souboru, jak je uvedeno výše v tomto tématu).</span><span class="sxs-lookup"><span data-stu-id="55a68-353">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="55a68-354">Při současném použití `Listen` i `UseUrls` jsou koncové body `Listen` popsány v koncových bodech `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="55a68-354">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="55a68-355">Konfigurace koncového bodu služby IIS</span><span class="sxs-lookup"><span data-stu-id="55a68-355">IIS endpoint configuration</span></span>

<span data-ttu-id="55a68-356">Při použití služby IIS jsou vazby adresy URL pro přepsání vazby služby IIS nastaveny buď pomocí `Listen`, nebo `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="55a68-356">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="55a68-357">Další informace najdete v tématu [ASP.NET Core modulu](xref:host-and-deploy/aspnet-core-module) .</span><span class="sxs-lookup"><span data-stu-id="55a68-357">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="55a68-358">ListenOptions. Protocols</span><span class="sxs-lookup"><span data-stu-id="55a68-358">ListenOptions.Protocols</span></span>

<span data-ttu-id="55a68-359">Vlastnost `Protocols` vytváří protokoly HTTP (`HttpProtocols`) povolené na koncovém bodu připojení nebo pro server.</span><span class="sxs-lookup"><span data-stu-id="55a68-359">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="55a68-360">Přiřaďte hodnotu vlastnosti `Protocols` ze výčtu `HttpProtocols`.</span><span class="sxs-lookup"><span data-stu-id="55a68-360">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="55a68-361">hodnota výčtu `HttpProtocols`</span><span class="sxs-lookup"><span data-stu-id="55a68-361">`HttpProtocols` enum value</span></span> | <span data-ttu-id="55a68-362">Povolený protokol připojení</span><span class="sxs-lookup"><span data-stu-id="55a68-362">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="55a68-363">Pouze HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="55a68-363">HTTP/1.1 only.</span></span> <span data-ttu-id="55a68-364">Dá se použít s TLS nebo bez něj.</span><span class="sxs-lookup"><span data-stu-id="55a68-364">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="55a68-365">Pouze HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="55a68-365">HTTP/2 only.</span></span> <span data-ttu-id="55a68-366">Dá se použít bez TLS jenom v případě, že klient podporuje [předchozí režim znalostní báze](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="55a68-366">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="55a68-367">HTTP/1.1 a HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="55a68-367">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="55a68-368">HTTP/2 vyžaduje, aby klient v rámci metody handshake protokolu TLS [(ALPN)](https://tools.ietf.org/html/rfc7301#section-3) vybral http/2. v opačném případě se připojení nastaví jako výchozí HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="55a68-368">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="55a68-369">Výchozí hodnota `ListenOptions.Protocols` pro libovolný koncový bod je `HttpProtocols.Http1AndHttp2`.</span><span class="sxs-lookup"><span data-stu-id="55a68-369">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="55a68-370">Omezení TLS pro HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="55a68-370">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="55a68-371">TLS verze 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="55a68-371">TLS version 1.2 or later</span></span>
* <span data-ttu-id="55a68-372">Opětovné vyjednávání je zakázané.</span><span class="sxs-lookup"><span data-stu-id="55a68-372">Renegotiation disabled</span></span>
* <span data-ttu-id="55a68-373">Komprese vypnuta</span><span class="sxs-lookup"><span data-stu-id="55a68-373">Compression disabled</span></span>
* <span data-ttu-id="55a68-374">Minimální velikosti dočasného výměny klíčů:</span><span class="sxs-lookup"><span data-stu-id="55a68-374">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="55a68-375">Eliptická křivka Diffie-Hellman (ECDH) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bity minima</span><span class="sxs-lookup"><span data-stu-id="55a68-375">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum</span></span>
  * <span data-ttu-id="55a68-376">Omezené pole Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bity minima</span><span class="sxs-lookup"><span data-stu-id="55a68-376">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bits minimum</span></span>
* <span data-ttu-id="55a68-377">Šifrovací sada není zakázaná.</span><span class="sxs-lookup"><span data-stu-id="55a68-377">Cipher suite not blacklisted</span></span>

<span data-ttu-id="55a68-378">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; s eliptickou křivkou P-256 &lbrack;`FIPS186`&rbrack; je ve výchozím nastavení podporován.</span><span class="sxs-lookup"><span data-stu-id="55a68-378">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="55a68-379">Následující příklad povoluje připojení HTTP/1.1 a HTTP/2 na portu 8000.</span><span class="sxs-lookup"><span data-stu-id="55a68-379">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="55a68-380">Připojení TLS zabezpečuje pomocí zadaného certifikátu:</span><span class="sxs-lookup"><span data-stu-id="55a68-380">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="55a68-381">Pomocí middleware připojení můžete v případě potřeby filtrovat handshake na základě jednotlivých připojení pro konkrétní šifry.</span><span class="sxs-lookup"><span data-stu-id="55a68-381">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="55a68-382">Následující příklad vyvolá <xref:System.NotSupportedException> pro jakýkoli šifrovací algoritmus, který aplikace nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="55a68-382">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="55a68-383">Případně můžete definovat a porovnat [ITlsHandshakeFeature. CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) se seznamem přijatelných šifrovacích sad.</span><span class="sxs-lookup"><span data-stu-id="55a68-383">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="55a68-384">Pro šifrovací algoritmus [CipherAlgorithmType. null](xref:System.Security.Authentication.CipherAlgorithmType) se nepoužívá žádné šifrování.</span><span class="sxs-lookup"><span data-stu-id="55a68-384">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

```csharp
// using System.Net;
// using Microsoft.AspNetCore.Connections;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.UseTlsFilter();
    });
});
```

```csharp
using System;
using System.Security.Authentication;
using Microsoft.AspNetCore.Connections.Features;

namespace Microsoft.AspNetCore.Connections
{
    public static class TlsFilterConnectionMiddlewareExtensions
    {
        public static IConnectionBuilder UseTlsFilter(
            this IConnectionBuilder builder)
        {
            return builder.Use((connection, next) =>
            {
                var tlsFeature = connection.Features.Get<ITlsHandshakeFeature>();

                if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
                {
                    throw new NotSupportedException("Prohibited cipher: " +
                        tlsFeature.CipherAlgorithm);
                }

                return next();
            });
        }
    }
}
```

<span data-ttu-id="55a68-385">Filtrování připojení lze také nakonfigurovat prostřednictvím lambda <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder>:</span><span class="sxs-lookup"><span data-stu-id="55a68-385">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

```csharp
// using System;
// using System.Net;
// using System.Security.Authentication;
// using Microsoft.AspNetCore.Connections;
// using Microsoft.AspNetCore.Connections.Features;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
        listenOptions.Use((context, next) =>
        {
            var tlsFeature = context.Features.Get<ITlsHandshakeFeature>();

            if (tlsFeature.CipherAlgorithm == CipherAlgorithmType.Null)
            {
                throw new NotSupportedException(
                    $"Prohibited cipher: {tlsFeature.CipherAlgorithm}");
            }

            return next();
        });
    });
});
```

<span data-ttu-id="55a68-386">V systému Linux lze pomocí <xref:System.Net.Security.CipherSuitesPolicy> filtrovat handshake TLS na jednotlivých připojeních:</span><span class="sxs-lookup"><span data-stu-id="55a68-386">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

```csharp
// using System.Net.Security;
// using Microsoft.AspNetCore.Hosting;
// using Microsoft.AspNetCore.Server.Kestrel.Core;
// using Microsoft.Extensions.DependencyInjection;
// using Microsoft.Extensions.Hosting;

webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureHttpsDefaults(listenOptions =>
    {
        listenOptions.OnAuthenticate = (context, sslOptions) =>
        {
            sslOptions.CipherSuitesPolicy = new CipherSuitesPolicy(
                new[]
                {
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256,
                    TlsCipherSuite.TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384,
                    // ...
                });
        };
    });
});
```

<span data-ttu-id="55a68-387">*Nastavit protokol z konfigurace*</span><span class="sxs-lookup"><span data-stu-id="55a68-387">*Set the protocol from configuration*</span></span>

<span data-ttu-id="55a68-388">`CreateDefaultBuilder` volá ve výchozím nastavení `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))`, aby se načetla konfigurace Kestrel.</span><span class="sxs-lookup"><span data-stu-id="55a68-388">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="55a68-389">Následující příklad *appSettings. JSON* vytvoří HTTP/1.1 jako výchozí protokol připojení pro všechny koncové body:</span><span class="sxs-lookup"><span data-stu-id="55a68-389">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="55a68-390">Následující příklad *appSettings. JSON* vytvoří protokol připojení HTTP/1.1 pro konkrétní koncový bod:</span><span class="sxs-lookup"><span data-stu-id="55a68-390">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="55a68-391">Protokoly zadané v hodnotách přepisu kódu nastavených podle konfigurace</span><span class="sxs-lookup"><span data-stu-id="55a68-391">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="55a68-392">Konfigurace přenosu</span><span class="sxs-lookup"><span data-stu-id="55a68-392">Transport configuration</span></span>

<span data-ttu-id="55a68-393">Pro projekty, které vyžadují použití Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span><span class="sxs-lookup"><span data-stu-id="55a68-393">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span></span>

* <span data-ttu-id="55a68-394">Přidejte do souboru projektu aplikace závislost pro balíček [Microsoft. AspNetCore. Server. Kestrel. Transport. Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) :</span><span class="sxs-lookup"><span data-stu-id="55a68-394">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* <span data-ttu-id="55a68-395">Na `IWebHostBuilder` volejte <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span><span class="sxs-lookup"><span data-stu-id="55a68-395">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> on the `IWebHostBuilder`:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="55a68-396">Předpony adresy URL</span><span class="sxs-lookup"><span data-stu-id="55a68-396">URL prefixes</span></span>

<span data-ttu-id="55a68-397">Při použití `UseUrls`, `--urls` argumentu příkazového řádku, `urls` konfigurační klíč hostitele nebo proměnná prostředí `ASPNETCORE_URLS`, můžou být předpony adresy URL v některém z následujících formátů.</span><span class="sxs-lookup"><span data-stu-id="55a68-397">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="55a68-398">Platné jsou pouze předpony adresy URL protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="55a68-398">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="55a68-399">Kestrel nepodporuje HTTPS při konfiguraci vazeb URL pomocí `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="55a68-399">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="55a68-400">Adresa IPv4 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="55a68-400">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="55a68-401">`0.0.0.0` je zvláštní případ, který se váže ke všem adresám IPv4.</span><span class="sxs-lookup"><span data-stu-id="55a68-401">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="55a68-402">Adresa IPv6 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="55a68-402">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="55a68-403">`[::]` je ekvivalent protokolu IPv4 `0.0.0.0`protokolu IPv4.</span><span class="sxs-lookup"><span data-stu-id="55a68-403">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="55a68-404">Název hostitele s číslem portu</span><span class="sxs-lookup"><span data-stu-id="55a68-404">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="55a68-405">Názvy hostitelů `*` a `+` nejsou speciální.</span><span class="sxs-lookup"><span data-stu-id="55a68-405">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="55a68-406">Cokoli se nerozpoznalo jako platná IP adresa nebo `localhost` váže ke všem IP adresám IPv4 a IPv6.</span><span class="sxs-lookup"><span data-stu-id="55a68-406">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="55a68-407">Pokud chcete navazovat různé názvy hostitelů na různé ASP.NET Core aplikace na stejném portu, použijte [http. sys](xref:fundamentals/servers/httpsys) nebo reverzní proxy server, třeba IIS, Nginx nebo Apache.</span><span class="sxs-lookup"><span data-stu-id="55a68-407">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="55a68-408">Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="55a68-408">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="55a68-409">Název hostitele `localhost` s číslem portu nebo IP adresou zpětné smyčky s číslem portu</span><span class="sxs-lookup"><span data-stu-id="55a68-409">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="55a68-410">Pokud je zadána hodnota `localhost`, pokusí se Kestrel vytvořit propojení s rozhraními zpětné smyčky IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="55a68-410">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="55a68-411">Pokud je požadovaný port používán jinou službou na rozhraní zpětné smyčky, spuštění Kestrel se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="55a68-411">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="55a68-412">Pokud je rozhraní zpětné smyčky v žádném jiném případě nedostupné (většinou kvůli tomu, že protokol IPv6 není podporovaný), Kestrel zaznamená upozornění.</span><span class="sxs-lookup"><span data-stu-id="55a68-412">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="55a68-413">Filtrování hostitele</span><span class="sxs-lookup"><span data-stu-id="55a68-413">Host filtering</span></span>

<span data-ttu-id="55a68-414">I když Kestrel podporuje konfiguraci na základě prefixů, jako je například `http://example.com:5000`, Kestrel převážně ignoruje název hostitele.</span><span class="sxs-lookup"><span data-stu-id="55a68-414">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="55a68-415">Hostitelská `localhost` je zvláštní případ, který se používá pro vazbu na adresy zpětné smyčky.</span><span class="sxs-lookup"><span data-stu-id="55a68-415">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="55a68-416">Každý hostitel, který je jiný než explicitní IP adresa, se váže na všechny veřejné IP adresy.</span><span class="sxs-lookup"><span data-stu-id="55a68-416">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="55a68-417">hlavičky `Host` nejsou ověřeny.</span><span class="sxs-lookup"><span data-stu-id="55a68-417">`Host` headers aren't validated.</span></span>

<span data-ttu-id="55a68-418">Jako alternativní řešení použijte middleware pro filtrování hostitele.</span><span class="sxs-lookup"><span data-stu-id="55a68-418">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="55a68-419">Middleware pro filtrování hostitele poskytuje balíček [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , který je implicitně k dispozici pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="55a68-419">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="55a68-420">Middleware se přidá pomocí <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, která volá <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="55a68-420">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="55a68-421">Middleware pro filtrování hostitele je ve výchozím nastavení zakázáno.</span><span class="sxs-lookup"><span data-stu-id="55a68-421">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="55a68-422">Chcete-li povolit middleware, definujte `AllowedHosts` klíč v souboru *appSettings. json*/*appSettings.\<prostředí >. JSON*.</span><span class="sxs-lookup"><span data-stu-id="55a68-422">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="55a68-423">Hodnota je seznam názvů hostitelů oddělených středníkem bez čísel portů:</span><span class="sxs-lookup"><span data-stu-id="55a68-423">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="55a68-424">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="55a68-424">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="55a68-425">[Middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer) má taky možnost <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="55a68-425">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="55a68-426">Middleware předávaných hlaviček a middleware pro filtrování hostitele mají podobné funkce pro různé scénáře.</span><span class="sxs-lookup"><span data-stu-id="55a68-426">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="55a68-427">Nastavení `AllowedHosts` s předaným middlewarem hlaviček je vhodné, když se hlavička `Host` nezachová při předávání požadavků pomocí reverzního proxy server nebo nástroje pro vyrovnávání zatížení.</span><span class="sxs-lookup"><span data-stu-id="55a68-427">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="55a68-428">Nastavení `AllowedHosts` pomocí middlewaru pro filtrování hostitele je vhodné, pokud se Kestrel používá jako veřejný hraniční Server nebo když je hlavička `Host` přímo předána.</span><span class="sxs-lookup"><span data-stu-id="55a68-428">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="55a68-429">Další informace o middlewaru předávaných hlaviček najdete v tématu <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="55a68-429">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="55a68-430">Kestrel je [webový server pro různé platformy pro ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="55a68-430">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="55a68-431">Kestrel je webový server, který je ve výchozím nastavení součástí ASP.NET Core šablon projektů.</span><span class="sxs-lookup"><span data-stu-id="55a68-431">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="55a68-432">Kestrel podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="55a68-432">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="55a68-433">HTTPS</span><span class="sxs-lookup"><span data-stu-id="55a68-433">HTTPS</span></span>
* <span data-ttu-id="55a68-434">Neprůhledný upgrade používaný k povolení [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="55a68-434">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="55a68-435">Technologie UNIX Sockets s vysokým výkonem za Nginx</span><span class="sxs-lookup"><span data-stu-id="55a68-435">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="55a68-436">HTTP/2 (kromě macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="55a68-436">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="55a68-437">&dagger;HTTP/2 bude v budoucí verzi podporován v macOS.</span><span class="sxs-lookup"><span data-stu-id="55a68-437">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="55a68-438">Kestrel se podporuje na všech platformách a verzích, které podporuje .NET Core.</span><span class="sxs-lookup"><span data-stu-id="55a68-438">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="55a68-439">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="55a68-439">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="55a68-440">Podpora HTTP/2</span><span class="sxs-lookup"><span data-stu-id="55a68-440">HTTP/2 support</span></span>

<span data-ttu-id="55a68-441">[Http/2](https://httpwg.org/specs/rfc7540.html) je k dispozici pro ASP.NET Core aplikace, pokud jsou splněné následující základní požadavky:</span><span class="sxs-lookup"><span data-stu-id="55a68-441">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="55a68-442">&dagger; operačního systému</span><span class="sxs-lookup"><span data-stu-id="55a68-442">Operating system&dagger;</span></span>
  * <span data-ttu-id="55a68-443">Windows Server 2016/Windows 10 nebo novější&Dagger;</span><span class="sxs-lookup"><span data-stu-id="55a68-443">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="55a68-444">Linux s OpenSSL 1.0.2 nebo novějším (například Ubuntu 16,04 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="55a68-444">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="55a68-445">Cílová architektura: .NET Core 2,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="55a68-445">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="55a68-446">Připojení [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="55a68-446">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="55a68-447">Připojení TLS 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="55a68-447">TLS 1.2 or later connection</span></span>

<span data-ttu-id="55a68-448">&dagger;HTTP/2 bude v budoucí verzi podporován v macOS.</span><span class="sxs-lookup"><span data-stu-id="55a68-448">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="55a68-449">&Dagger;Kestrel má omezená podpora HTTP/2 na Windows Serveru 2012 R2 a Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="55a68-449">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="55a68-450">Podpora je omezená, protože seznam podporovaných šifrovacích sad TLS, které jsou k dispozici v těchto operačních systémech, je omezený.</span><span class="sxs-lookup"><span data-stu-id="55a68-450">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="55a68-451">Pro zabezpečení připojení TLS může být vyžadován certifikát vygenerovaný pomocí algoritmu ECDSA (s připojením typu eliptická křivka).</span><span class="sxs-lookup"><span data-stu-id="55a68-451">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="55a68-452">Pokud je navázáno připojení HTTP/2, sestavy [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="55a68-452">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="55a68-453">HTTP/2 je ve výchozím nastavení zakázané.</span><span class="sxs-lookup"><span data-stu-id="55a68-453">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="55a68-454">Další informace o konfiguraci najdete v částech [Možnosti Kestrel](#kestrel-options) a [ListenOptions. Protocols](#listenoptionsprotocols) .</span><span class="sxs-lookup"><span data-stu-id="55a68-454">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="55a68-455">Kdy použít Kestrel s reverzním proxy serverem</span><span class="sxs-lookup"><span data-stu-id="55a68-455">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="55a68-456">Kestrel může používat sám sebe nebo s *reverzními proxy server*, jako je [Internetová informační služba (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="55a68-456">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="55a68-457">Reverzní proxy server přijímá požadavky HTTP ze sítě a přesměruje je na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="55a68-457">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="55a68-458">Kestrel použitý jako hraniční webový server (internetový):</span><span class="sxs-lookup"><span data-stu-id="55a68-458">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel komunikuje přímo s internetem bez zpětného proxy server](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="55a68-460">Kestrel se používá v konfiguraci reverzního proxy serveru:</span><span class="sxs-lookup"><span data-stu-id="55a68-460">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel nepřímo komunikuje s internetem prostřednictvím reverzní proxy server, jako je IIS, Nginx nebo Apache.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="55a68-462">Buď konfigurace, s zpětným proxy server nebo bez něj, je podporovaná konfigurace hostování.</span><span class="sxs-lookup"><span data-stu-id="55a68-462">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="55a68-463">Kestrel používané jako hraniční Server bez reverzních proxy server nepodporuje sdílení stejné IP adresy a portu mezi více procesy.</span><span class="sxs-lookup"><span data-stu-id="55a68-463">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="55a68-464">Když je Kestrel nakonfigurovaný k naslouchání na portu, Kestrel zpracovává veškerý provoz tohoto portu bez ohledu na hlavičky `Host`.</span><span class="sxs-lookup"><span data-stu-id="55a68-464">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="55a68-465">Reverzní proxy server, který může sdílet porty, má schopnost přesměrovat požadavky na Kestrel na jedinečné IP adresy a porty.</span><span class="sxs-lookup"><span data-stu-id="55a68-465">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="55a68-466">I v případě, že se zpětný proxy server nepožaduje, může být použití reverzní proxy server vhodnou volbou.</span><span class="sxs-lookup"><span data-stu-id="55a68-466">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="55a68-467">Reverzní proxy:</span><span class="sxs-lookup"><span data-stu-id="55a68-467">A reverse proxy:</span></span>

* <span data-ttu-id="55a68-468">Může omezit vystavenou veřejnou plochu v aplikacích, které hostuje.</span><span class="sxs-lookup"><span data-stu-id="55a68-468">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="55a68-469">Poskytněte další vrstvu konfigurace a obrany.</span><span class="sxs-lookup"><span data-stu-id="55a68-469">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="55a68-470">Může se lépe integrovat se stávající infrastrukturou.</span><span class="sxs-lookup"><span data-stu-id="55a68-470">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="55a68-471">Zjednodušte vyrovnávání zatížení a konfiguraci zabezpečené komunikace (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="55a68-471">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="55a68-472">Pouze reverzní proxy server vyžaduje certifikát X. 509 a tento server může komunikovat se servery aplikace v interní síti pomocí prostého protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="55a68-472">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="55a68-473">Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="55a68-473">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="55a68-474">Použití Kestrel v aplikacích ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="55a68-474">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="55a68-475">Balíček [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) je součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="55a68-475">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="55a68-476">ASP.NET Core šablony projektu ve výchozím nastavení používají Kestrel.</span><span class="sxs-lookup"><span data-stu-id="55a68-476">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="55a68-477">V *program.cs*kód šablony volá <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, která volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> na pozadí.</span><span class="sxs-lookup"><span data-stu-id="55a68-477">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="55a68-478">Další informace o `CreateDefaultBuilder` a vytváření hostitele najdete v části *Nastavení hostitele* v <xref:fundamentals/host/web-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="55a68-478">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="55a68-479">Pokud chcete po volání `CreateDefaultBuilder` zadat další konfiguraci, použijte `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="55a68-479">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="55a68-480">Pokud aplikace nevolá `CreateDefaultBuilder` pro nastavení hostitele, **před** voláním `ConfigureKestrel` volejte <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span><span class="sxs-lookup"><span data-stu-id="55a68-480">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="55a68-481">Kestrel možnosti</span><span class="sxs-lookup"><span data-stu-id="55a68-481">Kestrel options</span></span>

<span data-ttu-id="55a68-482">Webový server Kestrel má možnosti konfigurace omezení, které jsou zvláště užitečné v internetových nasazeních.</span><span class="sxs-lookup"><span data-stu-id="55a68-482">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="55a68-483">Nastavte omezení pro vlastnost <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> třídy <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="55a68-483">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="55a68-484">Vlastnost `Limits` obsahuje instanci třídy <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="55a68-484">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="55a68-485">V následujících příkladech se používá obor názvů <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:</span><span class="sxs-lookup"><span data-stu-id="55a68-485">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="55a68-486">Možnosti Kestrel, které jsou konfigurovány C# v kódu v následujících příkladech, lze také nastavit pomocí [poskytovatele konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="55a68-486">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="55a68-487">Poskytovatel konfigurace souboru může například načíst konfiguraci Kestrel z objektu *appSettings. JSON* nebo *appSettings. { Soubor Environment}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="55a68-487">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="55a68-488">Použijte **jeden** z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="55a68-488">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="55a68-489">Konfigurace Kestrel v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="55a68-489">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="55a68-490">Vloží instanci `IConfiguration` do třídy `Startup`.</span><span class="sxs-lookup"><span data-stu-id="55a68-490">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="55a68-491">Následující příklad předpokládá, že vložená konfigurace je přiřazena vlastnosti `Configuration`.</span><span class="sxs-lookup"><span data-stu-id="55a68-491">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="55a68-492">V `Startup.ConfigureServices` načtěte oddíl `Kestrel` konfigurace do konfigurace Kestrel.</span><span class="sxs-lookup"><span data-stu-id="55a68-492">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration.</span></span>

     ```csharp
     // using Microsoft.Extensions.Configuration

     public void ConfigureServices(IServiceCollection services)
     {
         services.Configure<KestrelServerOptions>(
             Configuration.GetSection("Kestrel"));
     }
     ```

* <span data-ttu-id="55a68-493">Nakonfigurovat Kestrel při sestavování hostitele:</span><span class="sxs-lookup"><span data-stu-id="55a68-493">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="55a68-494">V *program.cs*do konfigurace Kestrel načtěte oddíl `Kestrel` konfigurace:</span><span class="sxs-lookup"><span data-stu-id="55a68-494">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="55a68-495">Obě předchozí přístupy fungují u libovolného [poskytovatele konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="55a68-495">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="55a68-496">Časový limit Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="55a68-496">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="55a68-497">Získá nebo nastaví [časový limit zachování](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="55a68-497">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="55a68-498">Výchozí hodnota je 2 minuty.</span><span class="sxs-lookup"><span data-stu-id="55a68-498">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="55a68-499">Maximální počet připojení klientů</span><span class="sxs-lookup"><span data-stu-id="55a68-499">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="55a68-500">Maximální počet souběžných otevřených připojení TCP lze pro celou aplikaci nastavit pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="55a68-500">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="55a68-501">Existuje zvláštní omezení pro připojení, která byla upgradována z protokolu HTTP nebo HTTPS na jiný protokol (například na žádost WebSockets).</span><span class="sxs-lookup"><span data-stu-id="55a68-501">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="55a68-502">Po upgradu připojení se nepočítá s limitem `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="55a68-502">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="55a68-503">Ve výchozím nastavení je maximální počet připojení neomezené (null).</span><span class="sxs-lookup"><span data-stu-id="55a68-503">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="55a68-504">Maximální velikost textu požadavku</span><span class="sxs-lookup"><span data-stu-id="55a68-504">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="55a68-505">Výchozí maximální velikost textu požadavku je 30 000 000 bajtů, což je přibližně 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="55a68-505">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="55a68-506">Doporučený postup pro přepsání limitu v aplikaci ASP.NET Core MVC je použití atributu <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> u metody akce:</span><span class="sxs-lookup"><span data-stu-id="55a68-506">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="55a68-507">Tady je příklad, který ukazuje, jak nakonfigurovat omezení pro aplikaci na každém požadavku:</span><span class="sxs-lookup"><span data-stu-id="55a68-507">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="55a68-508">Přepsat nastavení u konkrétního požadavku v middleware:</span><span class="sxs-lookup"><span data-stu-id="55a68-508">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="55a68-509">Výjimka je vyvolána, pokud aplikace po zahájení aplikace nakonfiguruje limit na žádost.</span><span class="sxs-lookup"><span data-stu-id="55a68-509">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="55a68-510">Existuje vlastnost `IsReadOnly`, která určuje, zda je vlastnost `MaxRequestBodySize` ve stavu jen pro čtení, což znamená, že je příliš pozdě pro konfiguraci limitu.</span><span class="sxs-lookup"><span data-stu-id="55a68-510">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="55a68-511">Když je aplikace spouštěna [mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za [ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module), je omezení velikosti textu požadavku Kestrel zakázané, protože služba IIS tento limit již nastavuje.</span><span class="sxs-lookup"><span data-stu-id="55a68-511">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="55a68-512">Minimální rychlost těla zprávy požadavku</span><span class="sxs-lookup"><span data-stu-id="55a68-512">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="55a68-513">Kestrel kontroluje každou sekundu, pokud data přicházejí za zadanou rychlost v bajtech za sekundu.</span><span class="sxs-lookup"><span data-stu-id="55a68-513">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="55a68-514">Pokud frekvence klesne pod minimální hodnotu, vypršel časový limit připojení. Doba odkladu je doba, kterou Kestrel umožňuje klientovi zvýšit rychlost odesílání až na minimum; frekvence není během této doby kontrolována.</span><span class="sxs-lookup"><span data-stu-id="55a68-514">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="55a68-515">Doba odkladu pomáhá vyhnout se vyřazení připojení, která při počátečním posílání dat zpomalují rychlost, kvůli pomalému spuštění protokolu TCP.</span><span class="sxs-lookup"><span data-stu-id="55a68-515">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="55a68-516">Výchozí minimální sazba je 240 bajtů za sekundu s obdobím 5 sekund odkladu.</span><span class="sxs-lookup"><span data-stu-id="55a68-516">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="55a68-517">Pro odpověď platí také minimální sazba.</span><span class="sxs-lookup"><span data-stu-id="55a68-517">A minimum rate also applies to the response.</span></span> <span data-ttu-id="55a68-518">Kód pro nastavení limitu požadavků a limit odpovědi je stejný s výjimkou `RequestBody` nebo `Response` v názvech vlastností a rozhraní.</span><span class="sxs-lookup"><span data-stu-id="55a68-518">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="55a68-519">Tady je příklad, který ukazuje, jak nakonfigurovat minimální datové tarify v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="55a68-519">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="55a68-520">Přepsat minimální limity přenosové rychlosti na žádost v middlewaru:</span><span class="sxs-lookup"><span data-stu-id="55a68-520">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="55a68-521">V předchozí ukázce není k dispozici žádná míra – funkce `HttpContext.Features` pro požadavky HTTP/2, protože Změna omezení přenosové rychlosti u jednotlivých požadavků není pro protokol HTTP/2 podporovaná kvůli podpoře protokolu pro multiplexování požadavků.</span><span class="sxs-lookup"><span data-stu-id="55a68-521">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="55a68-522">Omezení přenosové rychlosti pro všechny servery nakonfigurovaná přes `KestrelServerOptions.Limits` se stále vztahují na připojení HTTP/1. x i HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="55a68-522">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="55a68-523">Časový limit hlaviček žádostí</span><span class="sxs-lookup"><span data-stu-id="55a68-523">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="55a68-524">Získá nebo nastaví maximální dobu, po kterou server stráví příjem hlaviček požadavků.</span><span class="sxs-lookup"><span data-stu-id="55a68-524">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="55a68-525">Výchozí hodnota je 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="55a68-525">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="55a68-526">Maximální počet proudů na připojení</span><span class="sxs-lookup"><span data-stu-id="55a68-526">Maximum streams per connection</span></span>

<span data-ttu-id="55a68-527">`Http2.MaxStreamsPerConnection` omezuje počet souběžných proudů požadavků na připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="55a68-527">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="55a68-528">Nadbytečné proudy jsou odmítnuty.</span><span class="sxs-lookup"><span data-stu-id="55a68-528">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="55a68-529">Výchozí hodnota je 100.</span><span class="sxs-lookup"><span data-stu-id="55a68-529">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="55a68-530">Velikost tabulky hlaviček</span><span class="sxs-lookup"><span data-stu-id="55a68-530">Header table size</span></span>

<span data-ttu-id="55a68-531">Dekodér HPACK dekomprimuje hlavičky HTTP pro připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="55a68-531">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="55a68-532">`Http2.HeaderTableSize` omezuje velikost kompresní tabulky hlaviček, kterou používá dekodér HPACK.</span><span class="sxs-lookup"><span data-stu-id="55a68-532">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="55a68-533">Hodnota je uvedena v oktetech a musí být větší než nula (0).</span><span class="sxs-lookup"><span data-stu-id="55a68-533">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="55a68-534">Výchozí hodnota je 4096.</span><span class="sxs-lookup"><span data-stu-id="55a68-534">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="55a68-535">Maximální velikost rámce</span><span class="sxs-lookup"><span data-stu-id="55a68-535">Maximum frame size</span></span>

<span data-ttu-id="55a68-536">`Http2.MaxFrameSize` označuje maximální velikost datové části rámce připojení HTTP/2, která se má přijmout.</span><span class="sxs-lookup"><span data-stu-id="55a68-536">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="55a68-537">Hodnota je uvedena v oktetech a musí být v rozmezí 2 ^ 14 (16 384) až 2 ^ 24-1 (16 777 215).</span><span class="sxs-lookup"><span data-stu-id="55a68-537">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="55a68-538">Výchozí hodnota je 2 ^ 14 (16 384).</span><span class="sxs-lookup"><span data-stu-id="55a68-538">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="55a68-539">Maximální velikost hlavičky požadavku</span><span class="sxs-lookup"><span data-stu-id="55a68-539">Maximum request header size</span></span>

<span data-ttu-id="55a68-540">`Http2.MaxRequestHeaderFieldSize` označuje maximální povolenou velikost v oktetech hodnot hlaviček požadavku.</span><span class="sxs-lookup"><span data-stu-id="55a68-540">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="55a68-541">Toto omezení platí pro názvy i hodnoty společně v komprimovaných a nekomprimovaných reprezentujcích.</span><span class="sxs-lookup"><span data-stu-id="55a68-541">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="55a68-542">Hodnota musí být větší než nula (0).</span><span class="sxs-lookup"><span data-stu-id="55a68-542">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="55a68-543">Výchozí hodnota je 8 192.</span><span class="sxs-lookup"><span data-stu-id="55a68-543">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="55a68-544">Velikost okna počátečního připojení</span><span class="sxs-lookup"><span data-stu-id="55a68-544">Initial connection window size</span></span>

<span data-ttu-id="55a68-545">`Http2.InitialConnectionWindowSize` označuje maximální velikost textu žádosti v bajtech. vyrovnávací paměti serveru se v jednom okamžiku agreguje napříč všemi požadavky (datovými proudy) na připojení.</span><span class="sxs-lookup"><span data-stu-id="55a68-545">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="55a68-546">Žádosti jsou také omezené pomocí `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="55a68-546">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="55a68-547">Hodnota musí být větší než nebo rovna 65 535 a menší než 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="55a68-547">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="55a68-548">Výchozí hodnota je 128 KB (131 072).</span><span class="sxs-lookup"><span data-stu-id="55a68-548">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="55a68-549">Počáteční velikost okna datového proudu</span><span class="sxs-lookup"><span data-stu-id="55a68-549">Initial stream window size</span></span>

<span data-ttu-id="55a68-550">`Http2.InitialStreamWindowSize` označuje maximální velikost textu požadavku v bajtech na jednu žádost (Stream) na jednom místě.</span><span class="sxs-lookup"><span data-stu-id="55a68-550">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="55a68-551">Žádosti jsou také omezené pomocí `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="55a68-551">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="55a68-552">Hodnota musí být větší než nebo rovna 65 535 a menší než 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="55a68-552">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="55a68-553">Výchozí hodnota je 96 KB (98 304).</span><span class="sxs-lookup"><span data-stu-id="55a68-553">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="55a68-554">Synchronní v/v</span><span class="sxs-lookup"><span data-stu-id="55a68-554">Synchronous IO</span></span>

<span data-ttu-id="55a68-555"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> řídí, zda je pro požadavek a odpověď povolena synchronní v/v.</span><span class="sxs-lookup"><span data-stu-id="55a68-555"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="55a68-556">Výchozí hodnota je `true`.</span><span class="sxs-lookup"><span data-stu-id="55a68-556">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="55a68-557">Velký počet blokování synchronních vstupně-výstupních operací může vést ke vyčerpání fondu vláken, což aplikaci nereaguje.</span><span class="sxs-lookup"><span data-stu-id="55a68-557">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="55a68-558">Při použití knihovny, která nepodporuje asynchronní vstupně-výstupní operace, povolte pouze `AllowSynchronousIO`.</span><span class="sxs-lookup"><span data-stu-id="55a68-558">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="55a68-559">Následující příklad povoluje synchronní vstupně-výstupní operace:</span><span class="sxs-lookup"><span data-stu-id="55a68-559">The following example enables synchronous IO:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="55a68-560">Informace o dalších možnostech a omezeních Kestrel najdete v tématech:</span><span class="sxs-lookup"><span data-stu-id="55a68-560">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="55a68-561">Konfigurace koncového bodu</span><span class="sxs-lookup"><span data-stu-id="55a68-561">Endpoint configuration</span></span>

<span data-ttu-id="55a68-562">Ve výchozím nastavení ASP.NET Core váže k:</span><span class="sxs-lookup"><span data-stu-id="55a68-562">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="55a68-563">`https://localhost:5001` (Pokud je k dispozici místní vývojový certifikát)</span><span class="sxs-lookup"><span data-stu-id="55a68-563">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="55a68-564">Zadejte adresy URL pomocí:</span><span class="sxs-lookup"><span data-stu-id="55a68-564">Specify URLs using the:</span></span>

* <span data-ttu-id="55a68-565">`ASPNETCORE_URLS` proměnnou prostředí.</span><span class="sxs-lookup"><span data-stu-id="55a68-565">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="55a68-566">argument příkazového řádku `--urls`</span><span class="sxs-lookup"><span data-stu-id="55a68-566">`--urls` command-line argument.</span></span>
* <span data-ttu-id="55a68-567">`urls` konfigurační klíč hostitele.</span><span class="sxs-lookup"><span data-stu-id="55a68-567">`urls` host configuration key.</span></span>
* <span data-ttu-id="55a68-568">Metoda rozšíření `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="55a68-568">`UseUrls` extension method.</span></span>

<span data-ttu-id="55a68-569">Hodnota zadaná pomocí těchto přístupů může být jeden nebo více koncových bodů HTTP a HTTPS (HTTPS, pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="55a68-569">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="55a68-570">Nakonfigurujte hodnotu jako seznam oddělený středníkem (například `"Urls": "http://localhost:8000; http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="55a68-570">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="55a68-571">Další informace o těchto přístupůch najdete v tématu [adresy URL serveru](xref:fundamentals/host/web-host#server-urls) a [potlačení konfigurace](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="55a68-571">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="55a68-572">Vytvoří se vývojový certifikát:</span><span class="sxs-lookup"><span data-stu-id="55a68-572">A development certificate is created:</span></span>

* <span data-ttu-id="55a68-573">Po instalaci [.NET Core SDK](/dotnet/core/sdk) .</span><span class="sxs-lookup"><span data-stu-id="55a68-573">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="55a68-574">[Nástroj pro vývoj a certifikáty](xref:aspnetcore-2.1#https) slouží k vytvoření certifikátu.</span><span class="sxs-lookup"><span data-stu-id="55a68-574">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="55a68-575">Některé prohlížeče vyžadují udělení explicitního oprávnění pro důvěřování místního vývojového certifikátu.</span><span class="sxs-lookup"><span data-stu-id="55a68-575">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="55a68-576">Šablony projektů konfigurují aplikace tak, aby běžely ve výchozím nastavení HTTPS a zahrnovaly [přesměrování https a podporu HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="55a68-576">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="55a68-577">Pokud chcete nakonfigurovat předpony a porty adres URL pro Kestrel, volejte <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> nebo <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> na <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="55a68-577">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="55a68-578">`UseUrls`, argument příkazového řádku `--urls`, `urls` konfigurační klíč hostitele a proměnná prostředí `ASPNETCORE_URLS` fungují, ale mají omezení zaznamenaná později v této části (pro konfiguraci koncového bodu HTTPS musí být k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="55a68-578">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="55a68-579">Konfigurace `KestrelServerOptions`:</span><span class="sxs-lookup"><span data-stu-id="55a68-579">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="55a68-580">ConfigureEndpointDefaults (Action\<ListenOptions >)</span><span class="sxs-lookup"><span data-stu-id="55a68-580">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="55a68-581">Určuje konfiguraci `Action`, která se má spustit pro každý zadaný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="55a68-581">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="55a68-582">Volání `ConfigureEndpointDefaults` několikrát nahrazuje předchozí `Action` s určeným posledním `Action`.</span><span class="sxs-lookup"><span data-stu-id="55a68-582">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="55a68-583">ConfigureHttpsDefaults (Action\<HttpsConnectionAdapterOptions >)</span><span class="sxs-lookup"><span data-stu-id="55a68-583">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="55a68-584">Určuje konfiguraci `Action` pro spuštění každého koncového bodu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="55a68-584">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="55a68-585">Volání `ConfigureHttpsDefaults` několikrát nahrazuje předchozí `Action` s určeným posledním `Action`.</span><span class="sxs-lookup"><span data-stu-id="55a68-585">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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

### <a name="configureiconfiguration"></a><span data-ttu-id="55a68-586">Konfigurace (IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="55a68-586">Configure(IConfiguration)</span></span>

<span data-ttu-id="55a68-587">Vytvoří zavaděč konfigurace pro nastavení Kestrel, který jako vstup převezme <xref:Microsoft.Extensions.Configuration.IConfiguration>.</span><span class="sxs-lookup"><span data-stu-id="55a68-587">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="55a68-588">Konfigurace musí být vymezená na konfigurační oddíl pro Kestrel.</span><span class="sxs-lookup"><span data-stu-id="55a68-588">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="55a68-589">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="55a68-589">ListenOptions.UseHttps</span></span>

<span data-ttu-id="55a68-590">Nakonfigurujte Kestrel tak, aby používal protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="55a68-590">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="55a68-591">rozšíření `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="55a68-591">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="55a68-592">`UseHttps` &ndash; nakonfigurujte Kestrel tak, aby používal HTTPS s výchozím certifikátem.</span><span class="sxs-lookup"><span data-stu-id="55a68-592">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="55a68-593">Vyvolá výjimku, pokud není nakonfigurován žádný výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="55a68-593">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="55a68-594">parametry `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="55a68-594">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="55a68-595">`filename` je cesta a název souboru certifikátu vzhledem k adresáři, který obsahuje soubory obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="55a68-595">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="55a68-596">`password` je heslo vyžadované pro přístup k datům certifikátu X. 509.</span><span class="sxs-lookup"><span data-stu-id="55a68-596">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="55a68-597">`configureOptions` je `Action` ke konfiguraci `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="55a68-597">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="55a68-598">Vrátí `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="55a68-598">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="55a68-599">`storeName` je úložiště certifikátů, ze kterého se má certifikát načíst.</span><span class="sxs-lookup"><span data-stu-id="55a68-599">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="55a68-600">`subject` je název subjektu certifikátu.</span><span class="sxs-lookup"><span data-stu-id="55a68-600">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="55a68-601">`allowInvalid` označuje, zda by měly být zváženy neplatné certifikáty, například certifikáty podepsané svým držitelem.</span><span class="sxs-lookup"><span data-stu-id="55a68-601">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="55a68-602">`location` je umístění úložiště, ze kterého se má načíst certifikát.</span><span class="sxs-lookup"><span data-stu-id="55a68-602">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="55a68-603">`serverCertificate` je certifikát X. 509.</span><span class="sxs-lookup"><span data-stu-id="55a68-603">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="55a68-604">V produkčním prostředí musí být HTTPS explicitně nakonfigurovaný.</span><span class="sxs-lookup"><span data-stu-id="55a68-604">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="55a68-605">Musí být zadán minimálně výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="55a68-605">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="55a68-606">Podporované konfigurace popsané dál:</span><span class="sxs-lookup"><span data-stu-id="55a68-606">Supported configurations described next:</span></span>

* <span data-ttu-id="55a68-607">Žádná konfigurace</span><span class="sxs-lookup"><span data-stu-id="55a68-607">No configuration</span></span>
* <span data-ttu-id="55a68-608">Nahradit výchozí certifikát z konfigurace</span><span class="sxs-lookup"><span data-stu-id="55a68-608">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="55a68-609">Změna výchozích hodnot v kódu</span><span class="sxs-lookup"><span data-stu-id="55a68-609">Change the defaults in code</span></span>

<span data-ttu-id="55a68-610">*Žádná konfigurace*</span><span class="sxs-lookup"><span data-stu-id="55a68-610">*No configuration*</span></span>

<span data-ttu-id="55a68-611">Kestrel naslouchá na `http://localhost:5000` a `https://localhost:5001` (Pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="55a68-611">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="55a68-612">*Nahradit výchozí certifikát z konfigurace*</span><span class="sxs-lookup"><span data-stu-id="55a68-612">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="55a68-613">`CreateDefaultBuilder` volá ve výchozím nastavení `Configure(context.Configuration.GetSection("Kestrel"))`, aby se načetla konfigurace Kestrel.</span><span class="sxs-lookup"><span data-stu-id="55a68-613">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="55a68-614">Pro Kestrel je k dispozici výchozí schéma konfigurace nastavení aplikace HTTPS.</span><span class="sxs-lookup"><span data-stu-id="55a68-614">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="55a68-615">Nakonfigurujte několik koncových bodů, včetně adres URL a certifikátů, které se mají použít, buď ze souboru na disku, nebo z úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="55a68-615">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="55a68-616">V následujícím příkladu *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="55a68-616">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="55a68-617">Nastavte **AllowInvalid** na `true`, pokud chcete povolit použití neplatných certifikátů (například certifikátů podepsaných svým držitelem).</span><span class="sxs-lookup"><span data-stu-id="55a68-617">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="55a68-618">Libovolný koncový bod HTTPS, který neurčuje certifikát (**HttpsDefaultCert** v následujícím příkladu), se vrátí k certifikátu definovanému v části **certifikáty** > **výchozí** nebo vývojový certifikát.</span><span class="sxs-lookup"><span data-stu-id="55a68-618">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="55a68-619">Alternativou k použití **cesty** a **hesla** pro libovolný uzel certifikátu je zadání certifikátu pomocí polí úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="55a68-619">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="55a68-620">Například **certifikáty** > **výchozí** certifikát lze zadat jako:</span><span class="sxs-lookup"><span data-stu-id="55a68-620">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="55a68-621">Poznámky ke schématu:</span><span class="sxs-lookup"><span data-stu-id="55a68-621">Schema notes:</span></span>

* <span data-ttu-id="55a68-622">V názvech koncových bodů se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="55a68-622">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="55a68-623">Například `HTTPS` a `Https` jsou platné.</span><span class="sxs-lookup"><span data-stu-id="55a68-623">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="55a68-624">U každého koncového bodu je vyžadován parametr `Url`.</span><span class="sxs-lookup"><span data-stu-id="55a68-624">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="55a68-625">Formát tohoto parametru je stejný jako konfigurační parametr `Urls` nejvyšší úrovně s tím rozdílem, že je omezen na jedinou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="55a68-625">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="55a68-626">Tyto koncové body nahrazují hodnoty definované v konfiguraci `Urls` na nejvyšší úrovni místo jejich přidávání do těchto koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="55a68-626">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="55a68-627">Koncové body definované v kódu prostřednictvím `Listen` jsou kumulativní s koncovými body definovanými v konfiguračním oddílu.</span><span class="sxs-lookup"><span data-stu-id="55a68-627">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="55a68-628">Oddíl `Certificate` je nepovinný.</span><span class="sxs-lookup"><span data-stu-id="55a68-628">The `Certificate` section is optional.</span></span> <span data-ttu-id="55a68-629">Pokud se nezadá oddíl `Certificate`, použijí se výchozí hodnoty definované v dřívějších scénářích.</span><span class="sxs-lookup"><span data-stu-id="55a68-629">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="55a68-630">Pokud nejsou k dispozici žádné výchozí hodnoty, Server vyvolá výjimku a nemůže se spustit.</span><span class="sxs-lookup"><span data-stu-id="55a68-630">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="55a68-631">Oddíl `Certificate` podporuje jak **cesty**&ndash;**hesla** , tak&ndash;ho **úložiště** **subjektu** .</span><span class="sxs-lookup"><span data-stu-id="55a68-631">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="55a68-632">V takovém případě může být definován libovolný počet koncových bodů, pokud to nezpůsobí konflikty portů.</span><span class="sxs-lookup"><span data-stu-id="55a68-632">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="55a68-633">`options.Configure(context.Configuration.GetSection("{SECTION}"))` vrátí `KestrelConfigurationLoader` s metodou `.Endpoint(string name, listenOptions => { })`, která se dá použít k doplnění nastavení nakonfigurovaného koncového bodu:</span><span class="sxs-lookup"><span data-stu-id="55a68-633">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="55a68-634">`KestrelServerOptions.ConfigurationLoader` se dá získat přímo k pokračování v iteraci u stávajícího zavaděče, jako je třeba ten, který poskytuje <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="55a68-634">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="55a68-635">Konfigurační oddíl pro každý koncový bod je k dispozici na možnostech v metodě `Endpoint`, aby bylo možné číst vlastní nastavení.</span><span class="sxs-lookup"><span data-stu-id="55a68-635">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="55a68-636">Více konfigurací může být načteno voláním `options.Configure(context.Configuration.GetSection("{SECTION}"))` znovu s jiným oddílem.</span><span class="sxs-lookup"><span data-stu-id="55a68-636">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="55a68-637">Je použita pouze poslední konfigurace, pokud není metoda `Load` explicitně volána pro předchozí instance.</span><span class="sxs-lookup"><span data-stu-id="55a68-637">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="55a68-638">Metapackage nevolá `Load`, takže jeho výchozí konfigurační oddíl může být nahrazen.</span><span class="sxs-lookup"><span data-stu-id="55a68-638">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="55a68-639">`KestrelConfigurationLoader` zrcadlí `Listen` rodinu rozhraní API od `KestrelServerOptions` jako přetížení `Endpoint`, takže koncové body kódu a konfigurace lze konfigurovat na stejném místě.</span><span class="sxs-lookup"><span data-stu-id="55a68-639">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="55a68-640">Tato přetížení nepoužívají názvy a využívají pouze výchozí nastavení z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="55a68-640">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="55a68-641">*Změna výchozích hodnot v kódu*</span><span class="sxs-lookup"><span data-stu-id="55a68-641">*Change the defaults in code*</span></span>

<span data-ttu-id="55a68-642">`ConfigureEndpointDefaults` a `ConfigureHttpsDefaults` lze použít ke změně výchozího nastavení pro `ListenOptions` a `HttpsConnectionAdapterOptions`, včetně přepsání výchozího certifikátu zadaného v předchozím scénáři.</span><span class="sxs-lookup"><span data-stu-id="55a68-642">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="55a68-643">`ConfigureEndpointDefaults` a `ConfigureHttpsDefaults` by měly být volány před konfigurací koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="55a68-643">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="55a68-644">*Podpora Kestrel pro SNI*</span><span class="sxs-lookup"><span data-stu-id="55a68-644">*Kestrel support for SNI*</span></span>

<span data-ttu-id="55a68-645">[Indikace názvu serveru (SNI)](https://tools.ietf.org/html/rfc6066#section-3) lze použít k hostování více domén na stejné IP adrese a portu.</span><span class="sxs-lookup"><span data-stu-id="55a68-645">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="55a68-646">Aby SNI fungovalo, klient pošle název hostitele pro zabezpečenou relaci serveru během metody handshake TLS, aby server mohl poskytovat správný certifikát.</span><span class="sxs-lookup"><span data-stu-id="55a68-646">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="55a68-647">Klient používá poskytnutý certifikát pro šifrovanou komunikaci se serverem během zabezpečené relace, která následuje za metodou handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="55a68-647">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="55a68-648">Kestrel podporuje SNI prostřednictvím zpětného volání `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="55a68-648">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="55a68-649">Zpětné volání je vyvoláno jednou za připojení, které aplikaci umožní zkontrolovat název hostitele a vybrat příslušný certifikát.</span><span class="sxs-lookup"><span data-stu-id="55a68-649">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="55a68-650">Podpora SNI vyžaduje:</span><span class="sxs-lookup"><span data-stu-id="55a68-650">SNI support requires:</span></span>

* <span data-ttu-id="55a68-651">Spuštění v cílovém rozhraní `netcoreapp2.1` nebo novější.</span><span class="sxs-lookup"><span data-stu-id="55a68-651">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="55a68-652">V `net461` nebo novějším je zpětné volání vyvoláno, ale `name` je vždy `null`.</span><span class="sxs-lookup"><span data-stu-id="55a68-652">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="55a68-653">`name` taky `null`, pokud klient neposkytne parametr názvu hostitele v handshaki TLS.</span><span class="sxs-lookup"><span data-stu-id="55a68-653">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="55a68-654">Všechny weby běží na stejné instanci Kestrel.</span><span class="sxs-lookup"><span data-stu-id="55a68-654">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="55a68-655">Kestrel nepodporuje sdílení IP adresy a portu napříč několika instancemi bez reverzního proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="55a68-655">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="55a68-656">Protokolování připojení</span><span class="sxs-lookup"><span data-stu-id="55a68-656">Connection logging</span></span>

<span data-ttu-id="55a68-657">Volání <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> k vygenerování protokolů na úrovni ladění pro komunikaci na úrovni bajtů v rámci připojení.</span><span class="sxs-lookup"><span data-stu-id="55a68-657">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="55a68-658">Protokolování připojení je užitečné při řešení problémů v komunikaci nízké úrovně, například během šifrování TLS a za proxy servery.</span><span class="sxs-lookup"><span data-stu-id="55a68-658">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="55a68-659">Pokud je `UseConnectionLogging` před `UseHttps`, zaprotokoluje se zašifrovaný provoz.</span><span class="sxs-lookup"><span data-stu-id="55a68-659">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="55a68-660">Je-li po `UseHttps`umístěn `UseConnectionLogging`, zašifrovaný provoz se zaznamená do protokolu.</span><span class="sxs-lookup"><span data-stu-id="55a68-660">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="55a68-661">Vytvoření vazby na soket TCP</span><span class="sxs-lookup"><span data-stu-id="55a68-661">Bind to a TCP socket</span></span>

<span data-ttu-id="55a68-662">Metoda <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> se váže k soketu TCP a obslužná rutina options umožňuje konfiguraci certifikátu X. 509:</span><span class="sxs-lookup"><span data-stu-id="55a68-662">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="55a68-663">Příklad konfiguruje HTTPS pro koncový bod s <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="55a68-663">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="55a68-664">Ke konfiguraci dalších nastavení Kestrel pro konkrétní koncové body použijte stejné rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="55a68-664">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="55a68-665">Vytvoření vazby na soket UNIX</span><span class="sxs-lookup"><span data-stu-id="55a68-665">Bind to a Unix socket</span></span>

<span data-ttu-id="55a68-666">Naslouchat na soketu se systémem UNIX pomocí <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> pro zlepšení výkonu pomocí Nginx, jak je znázorněno v tomto příkladu:</span><span class="sxs-lookup"><span data-stu-id="55a68-666">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

### <a name="port-0"></a><span data-ttu-id="55a68-667">Port 0</span><span class="sxs-lookup"><span data-stu-id="55a68-667">Port 0</span></span>

<span data-ttu-id="55a68-668">Když je zadané číslo portu `0`, dynamicky se váže k dostupnému portu.</span><span class="sxs-lookup"><span data-stu-id="55a68-668">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="55a68-669">Následující příklad ukazuje, jak určit, který port Kestrel je skutečně vázaný za běhu:</span><span class="sxs-lookup"><span data-stu-id="55a68-669">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="55a68-670">Když se aplikace spustí, výstup okna konzoly indikuje dynamický port, na který se aplikace dá získat:</span><span class="sxs-lookup"><span data-stu-id="55a68-670">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="55a68-671">Omezení</span><span class="sxs-lookup"><span data-stu-id="55a68-671">Limitations</span></span>

<span data-ttu-id="55a68-672">Konfigurace koncových bodů pomocí následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="55a68-672">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="55a68-673">argument příkazového řádku `--urls`</span><span class="sxs-lookup"><span data-stu-id="55a68-673">`--urls` command-line argument</span></span>
* <span data-ttu-id="55a68-674">konfigurační klíč hostitele `urls`</span><span class="sxs-lookup"><span data-stu-id="55a68-674">`urls` host configuration key</span></span>
* <span data-ttu-id="55a68-675">`ASPNETCORE_URLS` proměnná prostředí</span><span class="sxs-lookup"><span data-stu-id="55a68-675">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="55a68-676">Tyto metody jsou užitečné, pokud chcete, aby kód pracoval na jiných serverech než Kestrel.</span><span class="sxs-lookup"><span data-stu-id="55a68-676">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="55a68-677">Mějte ale na paměti následující omezení:</span><span class="sxs-lookup"><span data-stu-id="55a68-677">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="55a68-678">Protokol HTTPS nelze s těmito přístupy použít, pokud není k dispozici výchozí certifikát v konfiguraci koncového bodu HTTPS (například pomocí konfigurace `KestrelServerOptions` nebo konfiguračního souboru, jak je uvedeno výše v tomto tématu).</span><span class="sxs-lookup"><span data-stu-id="55a68-678">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="55a68-679">Při současném použití `Listen` i `UseUrls` jsou koncové body `Listen` popsány v koncových bodech `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="55a68-679">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="55a68-680">Konfigurace koncového bodu služby IIS</span><span class="sxs-lookup"><span data-stu-id="55a68-680">IIS endpoint configuration</span></span>

<span data-ttu-id="55a68-681">Při použití služby IIS jsou vazby adresy URL pro přepsání vazby služby IIS nastaveny buď pomocí `Listen`, nebo `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="55a68-681">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="55a68-682">Další informace najdete v tématu [ASP.NET Core modulu](xref:host-and-deploy/aspnet-core-module) .</span><span class="sxs-lookup"><span data-stu-id="55a68-682">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="55a68-683">ListenOptions. Protocols</span><span class="sxs-lookup"><span data-stu-id="55a68-683">ListenOptions.Protocols</span></span>

<span data-ttu-id="55a68-684">Vlastnost `Protocols` vytváří protokoly HTTP (`HttpProtocols`) povolené na koncovém bodu připojení nebo pro server.</span><span class="sxs-lookup"><span data-stu-id="55a68-684">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="55a68-685">Přiřaďte hodnotu vlastnosti `Protocols` ze výčtu `HttpProtocols`.</span><span class="sxs-lookup"><span data-stu-id="55a68-685">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="55a68-686">hodnota výčtu `HttpProtocols`</span><span class="sxs-lookup"><span data-stu-id="55a68-686">`HttpProtocols` enum value</span></span> | <span data-ttu-id="55a68-687">Povolený protokol připojení</span><span class="sxs-lookup"><span data-stu-id="55a68-687">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="55a68-688">Pouze HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="55a68-688">HTTP/1.1 only.</span></span> <span data-ttu-id="55a68-689">Dá se použít s TLS nebo bez něj.</span><span class="sxs-lookup"><span data-stu-id="55a68-689">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="55a68-690">Pouze HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="55a68-690">HTTP/2 only.</span></span> <span data-ttu-id="55a68-691">Dá se použít bez TLS jenom v případě, že klient podporuje [předchozí režim znalostní báze](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="55a68-691">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="55a68-692">HTTP/1.1 a HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="55a68-692">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="55a68-693">HTTP/2 vyžaduje připojení TLS a [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) ; v opačném případě se připojení nastaví jako výchozí HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="55a68-693">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="55a68-694">Výchozí protokol HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="55a68-694">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="55a68-695">Omezení TLS pro HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="55a68-695">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="55a68-696">TLS verze 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="55a68-696">TLS version 1.2 or later</span></span>
* <span data-ttu-id="55a68-697">Opětovné vyjednávání je zakázané.</span><span class="sxs-lookup"><span data-stu-id="55a68-697">Renegotiation disabled</span></span>
* <span data-ttu-id="55a68-698">Komprese vypnuta</span><span class="sxs-lookup"><span data-stu-id="55a68-698">Compression disabled</span></span>
* <span data-ttu-id="55a68-699">Minimální velikosti dočasného výměny klíčů:</span><span class="sxs-lookup"><span data-stu-id="55a68-699">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="55a68-700">Eliptická křivka Diffie-Hellman (ECDH) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bity minima</span><span class="sxs-lookup"><span data-stu-id="55a68-700">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum</span></span>
  * <span data-ttu-id="55a68-701">Omezené pole Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bity minima</span><span class="sxs-lookup"><span data-stu-id="55a68-701">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bits minimum</span></span>
* <span data-ttu-id="55a68-702">Šifrovací sada není zakázaná.</span><span class="sxs-lookup"><span data-stu-id="55a68-702">Cipher suite not blacklisted</span></span>

<span data-ttu-id="55a68-703">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; s eliptickou křivkou P-256 &lbrack;`FIPS186`&rbrack; je ve výchozím nastavení podporován.</span><span class="sxs-lookup"><span data-stu-id="55a68-703">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="55a68-704">Následující příklad povoluje připojení HTTP/1.1 a HTTP/2 na portu 8000.</span><span class="sxs-lookup"><span data-stu-id="55a68-704">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="55a68-705">Připojení TLS zabezpečuje pomocí zadaného certifikátu:</span><span class="sxs-lookup"><span data-stu-id="55a68-705">Connections are secured by TLS with a supplied certificate:</span></span>

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

<span data-ttu-id="55a68-706">Volitelně můžete vytvořit implementaci `IConnectionAdapter` pro filtrování ověřování TLS na základě jednotlivých připojení pro konkrétní šifry:</span><span class="sxs-lookup"><span data-stu-id="55a68-706">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

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
        // No encryption is used with a CipherAlgorithmType.Null cipher algorithm.
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

<span data-ttu-id="55a68-707">*Nastavit protokol z konfigurace*</span><span class="sxs-lookup"><span data-stu-id="55a68-707">*Set the protocol from configuration*</span></span>

<span data-ttu-id="55a68-708"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> volá ve výchozím nastavení `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))`, aby se načetla konfigurace Kestrel.</span><span class="sxs-lookup"><span data-stu-id="55a68-708"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="55a68-709">V následujícím příkladu *appSettings. JSON* se pro všechny koncové body Kestrel vytvoří výchozí protokol připojení (http/1.1 a HTTP/2):</span><span class="sxs-lookup"><span data-stu-id="55a68-709">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="55a68-710">Následující příklad konfiguračního souboru vytvoří protokol připojení pro konkrétní koncový bod:</span><span class="sxs-lookup"><span data-stu-id="55a68-710">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="55a68-711">Protokoly zadané v hodnotách přepisu kódu nastavených podle konfigurace</span><span class="sxs-lookup"><span data-stu-id="55a68-711">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="55a68-712">Konfigurace přenosu</span><span class="sxs-lookup"><span data-stu-id="55a68-712">Transport configuration</span></span>

<span data-ttu-id="55a68-713">S vydáním ASP.NET Core 2,1 není výchozí přenos Kestrel založen na Libuv, ale na základě spravovaných soketů.</span><span class="sxs-lookup"><span data-stu-id="55a68-713">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="55a68-714">Jedná se o zásadní změnu pro aplikace ASP.NET Core 2,0 upgradované na 2,1, která volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> a závisí na jednom z následujících balíčků:</span><span class="sxs-lookup"><span data-stu-id="55a68-714">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="55a68-715">[Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (přímý odkaz na balíček)</span><span class="sxs-lookup"><span data-stu-id="55a68-715">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="55a68-716">Microsoft. AspNetCore. app</span><span class="sxs-lookup"><span data-stu-id="55a68-716">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="55a68-717">Pro projekty, které vyžadují použití Libuv:</span><span class="sxs-lookup"><span data-stu-id="55a68-717">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="55a68-718">Přidejte do souboru projektu aplikace závislost pro balíček [Microsoft. AspNetCore. Server. Kestrel. Transport. Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) :</span><span class="sxs-lookup"><span data-stu-id="55a68-718">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="55a68-719">Volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span><span class="sxs-lookup"><span data-stu-id="55a68-719">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="55a68-720">Předpony adresy URL</span><span class="sxs-lookup"><span data-stu-id="55a68-720">URL prefixes</span></span>

<span data-ttu-id="55a68-721">Při použití `UseUrls`, `--urls` argumentu příkazového řádku, `urls` konfigurační klíč hostitele nebo proměnná prostředí `ASPNETCORE_URLS`, můžou být předpony adresy URL v některém z následujících formátů.</span><span class="sxs-lookup"><span data-stu-id="55a68-721">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="55a68-722">Platné jsou pouze předpony adresy URL protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="55a68-722">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="55a68-723">Kestrel nepodporuje HTTPS při konfiguraci vazeb URL pomocí `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="55a68-723">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="55a68-724">Adresa IPv4 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="55a68-724">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="55a68-725">`0.0.0.0` je zvláštní případ, který se váže ke všem adresám IPv4.</span><span class="sxs-lookup"><span data-stu-id="55a68-725">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="55a68-726">Adresa IPv6 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="55a68-726">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="55a68-727">`[::]` je ekvivalent protokolu IPv4 `0.0.0.0`protokolu IPv4.</span><span class="sxs-lookup"><span data-stu-id="55a68-727">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="55a68-728">Název hostitele s číslem portu</span><span class="sxs-lookup"><span data-stu-id="55a68-728">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="55a68-729">Názvy hostitelů `*` a `+` nejsou speciální.</span><span class="sxs-lookup"><span data-stu-id="55a68-729">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="55a68-730">Cokoli se nerozpoznalo jako platná IP adresa nebo `localhost` váže ke všem IP adresám IPv4 a IPv6.</span><span class="sxs-lookup"><span data-stu-id="55a68-730">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="55a68-731">Pokud chcete navazovat různé názvy hostitelů na různé ASP.NET Core aplikace na stejném portu, použijte [http. sys](xref:fundamentals/servers/httpsys) nebo reverzní proxy server, třeba IIS, Nginx nebo Apache.</span><span class="sxs-lookup"><span data-stu-id="55a68-731">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="55a68-732">Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="55a68-732">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="55a68-733">Název hostitele `localhost` s číslem portu nebo IP adresou zpětné smyčky s číslem portu</span><span class="sxs-lookup"><span data-stu-id="55a68-733">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="55a68-734">Pokud je zadána hodnota `localhost`, pokusí se Kestrel vytvořit propojení s rozhraními zpětné smyčky IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="55a68-734">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="55a68-735">Pokud je požadovaný port používán jinou službou na rozhraní zpětné smyčky, spuštění Kestrel se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="55a68-735">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="55a68-736">Pokud je rozhraní zpětné smyčky v žádném jiném případě nedostupné (většinou kvůli tomu, že protokol IPv6 není podporovaný), Kestrel zaznamená upozornění.</span><span class="sxs-lookup"><span data-stu-id="55a68-736">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="55a68-737">Filtrování hostitele</span><span class="sxs-lookup"><span data-stu-id="55a68-737">Host filtering</span></span>

<span data-ttu-id="55a68-738">I když Kestrel podporuje konfiguraci na základě prefixů, jako je například `http://example.com:5000`, Kestrel převážně ignoruje název hostitele.</span><span class="sxs-lookup"><span data-stu-id="55a68-738">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="55a68-739">Hostitelská `localhost` je zvláštní případ, který se používá pro vazbu na adresy zpětné smyčky.</span><span class="sxs-lookup"><span data-stu-id="55a68-739">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="55a68-740">Každý hostitel, který je jiný než explicitní IP adresa, se váže na všechny veřejné IP adresy.</span><span class="sxs-lookup"><span data-stu-id="55a68-740">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="55a68-741">hlavičky `Host` nejsou ověřeny.</span><span class="sxs-lookup"><span data-stu-id="55a68-741">`Host` headers aren't validated.</span></span>

<span data-ttu-id="55a68-742">Jako alternativní řešení použijte middleware pro filtrování hostitele.</span><span class="sxs-lookup"><span data-stu-id="55a68-742">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="55a68-743">Middleware pro filtrování hostitele poskytuje balíček [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , který je součástí [Microsoft. AspNetCore. App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 nebo 2,2).</span><span class="sxs-lookup"><span data-stu-id="55a68-743">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="55a68-744">Middleware se přidá pomocí <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, která volá <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="55a68-744">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="55a68-745">Middleware pro filtrování hostitele je ve výchozím nastavení zakázáno.</span><span class="sxs-lookup"><span data-stu-id="55a68-745">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="55a68-746">Chcete-li povolit middleware, definujte `AllowedHosts` klíč v souboru *appSettings. json*/*appSettings.\<prostředí >. JSON*.</span><span class="sxs-lookup"><span data-stu-id="55a68-746">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="55a68-747">Hodnota je seznam názvů hostitelů oddělených středníkem bez čísel portů:</span><span class="sxs-lookup"><span data-stu-id="55a68-747">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="55a68-748">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="55a68-748">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="55a68-749">[Middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer) má taky možnost <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="55a68-749">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="55a68-750">Middleware předávaných hlaviček a middleware pro filtrování hostitele mají podobné funkce pro různé scénáře.</span><span class="sxs-lookup"><span data-stu-id="55a68-750">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="55a68-751">Nastavení `AllowedHosts` s předaným middlewarem hlaviček je vhodné, když se hlavička `Host` nezachová při předávání požadavků pomocí reverzního proxy server nebo nástroje pro vyrovnávání zatížení.</span><span class="sxs-lookup"><span data-stu-id="55a68-751">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="55a68-752">Nastavení `AllowedHosts` pomocí middlewaru pro filtrování hostitele je vhodné, pokud se Kestrel používá jako veřejný hraniční Server nebo když je hlavička `Host` přímo předána.</span><span class="sxs-lookup"><span data-stu-id="55a68-752">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="55a68-753">Další informace o middlewaru předávaných hlaviček najdete v tématu <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="55a68-753">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="55a68-754">Kestrel je [webový server pro různé platformy pro ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="55a68-754">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="55a68-755">Kestrel je webový server, který je ve výchozím nastavení součástí ASP.NET Core šablon projektů.</span><span class="sxs-lookup"><span data-stu-id="55a68-755">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="55a68-756">Kestrel podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="55a68-756">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="55a68-757">HTTPS</span><span class="sxs-lookup"><span data-stu-id="55a68-757">HTTPS</span></span>
* <span data-ttu-id="55a68-758">Neprůhledný upgrade používaný k povolení [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="55a68-758">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="55a68-759">Technologie UNIX Sockets s vysokým výkonem za Nginx</span><span class="sxs-lookup"><span data-stu-id="55a68-759">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="55a68-760">Kestrel se podporuje na všech platformách a verzích, které podporuje .NET Core.</span><span class="sxs-lookup"><span data-stu-id="55a68-760">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="55a68-761">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="55a68-761">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="55a68-762">Kdy použít Kestrel s reverzním proxy serverem</span><span class="sxs-lookup"><span data-stu-id="55a68-762">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="55a68-763">Kestrel může používat sám sebe nebo s *reverzními proxy server*, jako je [Internetová informační služba (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="55a68-763">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="55a68-764">Reverzní proxy server přijímá požadavky HTTP ze sítě a přesměruje je na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="55a68-764">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="55a68-765">Kestrel použitý jako hraniční webový server (internetový):</span><span class="sxs-lookup"><span data-stu-id="55a68-765">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel komunikuje přímo s internetem bez zpětného proxy server](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="55a68-767">Kestrel se používá v konfiguraci reverzního proxy serveru:</span><span class="sxs-lookup"><span data-stu-id="55a68-767">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel nepřímo komunikuje s internetem prostřednictvím reverzní proxy server, jako je IIS, Nginx nebo Apache.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="55a68-769">Buď konfigurace, s zpětným proxy server nebo bez něj, je podporovaná konfigurace hostování.</span><span class="sxs-lookup"><span data-stu-id="55a68-769">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="55a68-770">Kestrel používané jako hraniční Server bez reverzních proxy server nepodporuje sdílení stejné IP adresy a portu mezi více procesy.</span><span class="sxs-lookup"><span data-stu-id="55a68-770">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="55a68-771">Když je Kestrel nakonfigurovaný k naslouchání na portu, Kestrel zpracovává veškerý provoz tohoto portu bez ohledu na hlavičky `Host`.</span><span class="sxs-lookup"><span data-stu-id="55a68-771">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="55a68-772">Reverzní proxy server, který může sdílet porty, má schopnost přesměrovat požadavky na Kestrel na jedinečné IP adresy a porty.</span><span class="sxs-lookup"><span data-stu-id="55a68-772">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="55a68-773">I v případě, že se zpětný proxy server nepožaduje, může být použití reverzní proxy server vhodnou volbou.</span><span class="sxs-lookup"><span data-stu-id="55a68-773">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="55a68-774">Reverzní proxy:</span><span class="sxs-lookup"><span data-stu-id="55a68-774">A reverse proxy:</span></span>

* <span data-ttu-id="55a68-775">Může omezit vystavenou veřejnou plochu v aplikacích, které hostuje.</span><span class="sxs-lookup"><span data-stu-id="55a68-775">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="55a68-776">Poskytněte další vrstvu konfigurace a obrany.</span><span class="sxs-lookup"><span data-stu-id="55a68-776">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="55a68-777">Může se lépe integrovat se stávající infrastrukturou.</span><span class="sxs-lookup"><span data-stu-id="55a68-777">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="55a68-778">Zjednodušte vyrovnávání zatížení a konfiguraci zabezpečené komunikace (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="55a68-778">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="55a68-779">Pouze reverzní proxy server vyžaduje certifikát X. 509 a tento server může komunikovat se servery aplikace v interní síti pomocí prostého protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="55a68-779">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="55a68-780">Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="55a68-780">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="55a68-781">Použití Kestrel v aplikacích ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="55a68-781">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="55a68-782">Balíček [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) je součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="55a68-782">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="55a68-783">ASP.NET Core šablony projektu ve výchozím nastavení používají Kestrel.</span><span class="sxs-lookup"><span data-stu-id="55a68-783">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="55a68-784">V *program.cs*kód šablony volá <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, která volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> na pozadí.</span><span class="sxs-lookup"><span data-stu-id="55a68-784">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="55a68-785">Pro poskytnutí další konfigurace po volání `CreateDefaultBuilder` volejte <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span><span class="sxs-lookup"><span data-stu-id="55a68-785">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="55a68-786">Další informace o `CreateDefaultBuilder` a vytváření hostitele najdete v části *Nastavení hostitele* v <xref:fundamentals/host/web-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="55a68-786">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="55a68-787">Kestrel možnosti</span><span class="sxs-lookup"><span data-stu-id="55a68-787">Kestrel options</span></span>

<span data-ttu-id="55a68-788">Webový server Kestrel má možnosti konfigurace omezení, které jsou zvláště užitečné v internetových nasazeních.</span><span class="sxs-lookup"><span data-stu-id="55a68-788">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="55a68-789">Nastavte omezení pro vlastnost <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> třídy <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="55a68-789">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="55a68-790">Vlastnost `Limits` obsahuje instanci třídy <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="55a68-790">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="55a68-791">V následujících příkladech se používá obor názvů <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:</span><span class="sxs-lookup"><span data-stu-id="55a68-791">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="55a68-792">Možnosti Kestrel, které jsou konfigurovány C# v kódu v následujících příkladech, lze také nastavit pomocí [poskytovatele konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="55a68-792">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="55a68-793">Poskytovatel konfigurace souboru může například načíst konfiguraci Kestrel z objektu *appSettings. JSON* nebo *appSettings. { Soubor Environment}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="55a68-793">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

```json
{
  "Kestrel": {
    "Limits": {
      "MaxConcurrentConnections": 100,
      "MaxConcurrentUpgradedConnections": 100
    }
  }
}
```

<span data-ttu-id="55a68-794">Použijte **jeden** z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="55a68-794">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="55a68-795">Konfigurace Kestrel v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="55a68-795">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="55a68-796">Vloží instanci `IConfiguration` do třídy `Startup`.</span><span class="sxs-lookup"><span data-stu-id="55a68-796">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="55a68-797">Následující příklad předpokládá, že vložená konfigurace je přiřazena vlastnosti `Configuration`.</span><span class="sxs-lookup"><span data-stu-id="55a68-797">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="55a68-798">V `Startup.ConfigureServices` načtěte oddíl `Kestrel` konfigurace do konfigurace Kestrel.</span><span class="sxs-lookup"><span data-stu-id="55a68-798">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration.</span></span>

     ```csharp
     // using Microsoft.Extensions.Configuration

     public void ConfigureServices(IServiceCollection services)
     {
         services.Configure<KestrelServerOptions>(
             Configuration.GetSection("Kestrel"));
     }
     ```

* <span data-ttu-id="55a68-799">Nakonfigurovat Kestrel při sestavování hostitele:</span><span class="sxs-lookup"><span data-stu-id="55a68-799">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="55a68-800">V *program.cs*do konfigurace Kestrel načtěte oddíl `Kestrel` konfigurace:</span><span class="sxs-lookup"><span data-stu-id="55a68-800">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

  ```csharp
  // using Microsoft.Extensions.DependencyInjection;

  public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
      WebHost.CreateDefaultBuilder(args)
          .ConfigureServices((context, services) =>
          {
              services.Configure<KestrelServerOptions>(
                  context.Configuration.GetSection("Kestrel"));
          })
          .UseStartup<Startup>();
  ```

<span data-ttu-id="55a68-801">Obě předchozí přístupy fungují u libovolného [poskytovatele konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="55a68-801">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="55a68-802">Časový limit Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="55a68-802">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="55a68-803">Získá nebo nastaví [časový limit zachování](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="55a68-803">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="55a68-804">Výchozí hodnota je 2 minuty.</span><span class="sxs-lookup"><span data-stu-id="55a68-804">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="55a68-805">Maximální počet připojení klientů</span><span class="sxs-lookup"><span data-stu-id="55a68-805">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="55a68-806">Maximální počet souběžných otevřených připojení TCP lze pro celou aplikaci nastavit pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="55a68-806">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="55a68-807">Existuje zvláštní omezení pro připojení, která byla upgradována z protokolu HTTP nebo HTTPS na jiný protokol (například na žádost WebSockets).</span><span class="sxs-lookup"><span data-stu-id="55a68-807">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="55a68-808">Po upgradu připojení se nepočítá s limitem `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="55a68-808">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="55a68-809">Ve výchozím nastavení je maximální počet připojení neomezené (null).</span><span class="sxs-lookup"><span data-stu-id="55a68-809">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="55a68-810">Maximální velikost textu požadavku</span><span class="sxs-lookup"><span data-stu-id="55a68-810">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="55a68-811">Výchozí maximální velikost textu požadavku je 30 000 000 bajtů, což je přibližně 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="55a68-811">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="55a68-812">Doporučený postup pro přepsání limitu v aplikaci ASP.NET Core MVC je použití atributu <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> u metody akce:</span><span class="sxs-lookup"><span data-stu-id="55a68-812">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="55a68-813">Tady je příklad, který ukazuje, jak nakonfigurovat omezení pro aplikaci na každém požadavku:</span><span class="sxs-lookup"><span data-stu-id="55a68-813">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="55a68-814">Přepsat nastavení u konkrétního požadavku v middleware:</span><span class="sxs-lookup"><span data-stu-id="55a68-814">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="55a68-815">Výjimka je vyvolána, pokud aplikace po zahájení aplikace nakonfiguruje limit na žádost.</span><span class="sxs-lookup"><span data-stu-id="55a68-815">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="55a68-816">Existuje vlastnost `IsReadOnly`, která určuje, zda je vlastnost `MaxRequestBodySize` ve stavu jen pro čtení, což znamená, že je příliš pozdě pro konfiguraci limitu.</span><span class="sxs-lookup"><span data-stu-id="55a68-816">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="55a68-817">Když je aplikace spouštěna [mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za [ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module), je omezení velikosti textu požadavku Kestrel zakázané, protože služba IIS tento limit již nastavuje.</span><span class="sxs-lookup"><span data-stu-id="55a68-817">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="55a68-818">Minimální rychlost těla zprávy požadavku</span><span class="sxs-lookup"><span data-stu-id="55a68-818">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="55a68-819">Kestrel kontroluje každou sekundu, pokud data přicházejí za zadanou rychlost v bajtech za sekundu.</span><span class="sxs-lookup"><span data-stu-id="55a68-819">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="55a68-820">Pokud frekvence klesne pod minimální hodnotu, vypršel časový limit připojení. Doba odkladu je doba, kterou Kestrel umožňuje klientovi zvýšit rychlost odesílání až na minimum; frekvence není během této doby kontrolována.</span><span class="sxs-lookup"><span data-stu-id="55a68-820">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="55a68-821">Doba odkladu pomáhá vyhnout se vyřazení připojení, která při počátečním posílání dat zpomalují rychlost, kvůli pomalému spuštění protokolu TCP.</span><span class="sxs-lookup"><span data-stu-id="55a68-821">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="55a68-822">Výchozí minimální sazba je 240 bajtů za sekundu s obdobím 5 sekund odkladu.</span><span class="sxs-lookup"><span data-stu-id="55a68-822">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="55a68-823">Pro odpověď platí také minimální sazba.</span><span class="sxs-lookup"><span data-stu-id="55a68-823">A minimum rate also applies to the response.</span></span> <span data-ttu-id="55a68-824">Kód pro nastavení limitu požadavků a limit odpovědi je stejný s výjimkou `RequestBody` nebo `Response` v názvech vlastností a rozhraní.</span><span class="sxs-lookup"><span data-stu-id="55a68-824">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="55a68-825">Tady je příklad, který ukazuje, jak nakonfigurovat minimální datové tarify v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="55a68-825">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

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

### <a name="request-headers-timeout"></a><span data-ttu-id="55a68-826">Časový limit hlaviček žádostí</span><span class="sxs-lookup"><span data-stu-id="55a68-826">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="55a68-827">Získá nebo nastaví maximální dobu, po kterou server stráví příjem hlaviček požadavků.</span><span class="sxs-lookup"><span data-stu-id="55a68-827">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="55a68-828">Výchozí hodnota je 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="55a68-828">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="55a68-829">Synchronní v/v</span><span class="sxs-lookup"><span data-stu-id="55a68-829">Synchronous IO</span></span>

<span data-ttu-id="55a68-830"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> řídí, zda je pro požadavek a odpověď povolena synchronní v/v.</span><span class="sxs-lookup"><span data-stu-id="55a68-830"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="55a68-831">Výchozí hodnota je `true`.</span><span class="sxs-lookup"><span data-stu-id="55a68-831">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="55a68-832">Velký počet blokování synchronních vstupně-výstupních operací může vést ke vyčerpání fondu vláken, což aplikaci nereaguje.</span><span class="sxs-lookup"><span data-stu-id="55a68-832">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="55a68-833">Při použití knihovny, která nepodporuje asynchronní vstupně-výstupní operace, povolte pouze `AllowSynchronousIO`.</span><span class="sxs-lookup"><span data-stu-id="55a68-833">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="55a68-834">Následující příklad zakáže synchronní v/v:</span><span class="sxs-lookup"><span data-stu-id="55a68-834">The following example disables synchronous IO:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="55a68-835">Informace o dalších možnostech a omezeních Kestrel najdete v tématech:</span><span class="sxs-lookup"><span data-stu-id="55a68-835">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="55a68-836">Konfigurace koncového bodu</span><span class="sxs-lookup"><span data-stu-id="55a68-836">Endpoint configuration</span></span>

<span data-ttu-id="55a68-837">Ve výchozím nastavení ASP.NET Core váže k:</span><span class="sxs-lookup"><span data-stu-id="55a68-837">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="55a68-838">`https://localhost:5001` (Pokud je k dispozici místní vývojový certifikát)</span><span class="sxs-lookup"><span data-stu-id="55a68-838">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="55a68-839">Zadejte adresy URL pomocí:</span><span class="sxs-lookup"><span data-stu-id="55a68-839">Specify URLs using the:</span></span>

* <span data-ttu-id="55a68-840">`ASPNETCORE_URLS` proměnnou prostředí.</span><span class="sxs-lookup"><span data-stu-id="55a68-840">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="55a68-841">argument příkazového řádku `--urls`</span><span class="sxs-lookup"><span data-stu-id="55a68-841">`--urls` command-line argument.</span></span>
* <span data-ttu-id="55a68-842">`urls` konfigurační klíč hostitele.</span><span class="sxs-lookup"><span data-stu-id="55a68-842">`urls` host configuration key.</span></span>
* <span data-ttu-id="55a68-843">Metoda rozšíření `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="55a68-843">`UseUrls` extension method.</span></span>

<span data-ttu-id="55a68-844">Hodnota zadaná pomocí těchto přístupů může být jeden nebo více koncových bodů HTTP a HTTPS (HTTPS, pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="55a68-844">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="55a68-845">Nakonfigurujte hodnotu jako seznam oddělený středníkem (například `"Urls": "http://localhost:8000; http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="55a68-845">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="55a68-846">Další informace o těchto přístupůch najdete v tématu [adresy URL serveru](xref:fundamentals/host/web-host#server-urls) a [potlačení konfigurace](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="55a68-846">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="55a68-847">Vytvoří se vývojový certifikát:</span><span class="sxs-lookup"><span data-stu-id="55a68-847">A development certificate is created:</span></span>

* <span data-ttu-id="55a68-848">Po instalaci [.NET Core SDK](/dotnet/core/sdk) .</span><span class="sxs-lookup"><span data-stu-id="55a68-848">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="55a68-849">[Nástroj pro vývoj a certifikáty](xref:aspnetcore-2.1#https) slouží k vytvoření certifikátu.</span><span class="sxs-lookup"><span data-stu-id="55a68-849">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="55a68-850">Některé prohlížeče vyžadují udělení explicitního oprávnění pro důvěřování místního vývojového certifikátu.</span><span class="sxs-lookup"><span data-stu-id="55a68-850">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="55a68-851">Šablony projektů konfigurují aplikace tak, aby běžely ve výchozím nastavení HTTPS a zahrnovaly [přesměrování https a podporu HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="55a68-851">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="55a68-852">Pokud chcete nakonfigurovat předpony a porty adres URL pro Kestrel, volejte <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> nebo <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> na <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="55a68-852">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="55a68-853">`UseUrls`, argument příkazového řádku `--urls`, `urls` konfigurační klíč hostitele a proměnná prostředí `ASPNETCORE_URLS` fungují, ale mají omezení zaznamenaná později v této části (pro konfiguraci koncového bodu HTTPS musí být k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="55a68-853">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="55a68-854">Konfigurace `KestrelServerOptions`:</span><span class="sxs-lookup"><span data-stu-id="55a68-854">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="55a68-855">ConfigureEndpointDefaults (Action\<ListenOptions >)</span><span class="sxs-lookup"><span data-stu-id="55a68-855">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="55a68-856">Určuje konfiguraci `Action`, která se má spustit pro každý zadaný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="55a68-856">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="55a68-857">Volání `ConfigureEndpointDefaults` několikrát nahrazuje předchozí `Action` s určeným posledním `Action`.</span><span class="sxs-lookup"><span data-stu-id="55a68-857">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="55a68-858">ConfigureHttpsDefaults (Action\<HttpsConnectionAdapterOptions >)</span><span class="sxs-lookup"><span data-stu-id="55a68-858">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="55a68-859">Určuje konfiguraci `Action` pro spuštění každého koncového bodu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="55a68-859">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="55a68-860">Volání `ConfigureHttpsDefaults` několikrát nahrazuje předchozí `Action` s určeným posledním `Action`.</span><span class="sxs-lookup"><span data-stu-id="55a68-860">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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

### <a name="configureiconfiguration"></a><span data-ttu-id="55a68-861">Konfigurace (IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="55a68-861">Configure(IConfiguration)</span></span>

<span data-ttu-id="55a68-862">Vytvoří zavaděč konfigurace pro nastavení Kestrel, který jako vstup převezme <xref:Microsoft.Extensions.Configuration.IConfiguration>.</span><span class="sxs-lookup"><span data-stu-id="55a68-862">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="55a68-863">Konfigurace musí být vymezená na konfigurační oddíl pro Kestrel.</span><span class="sxs-lookup"><span data-stu-id="55a68-863">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="55a68-864">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="55a68-864">ListenOptions.UseHttps</span></span>

<span data-ttu-id="55a68-865">Nakonfigurujte Kestrel tak, aby používal protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="55a68-865">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="55a68-866">rozšíření `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="55a68-866">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="55a68-867">`UseHttps` &ndash; nakonfigurujte Kestrel tak, aby používal HTTPS s výchozím certifikátem.</span><span class="sxs-lookup"><span data-stu-id="55a68-867">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="55a68-868">Vyvolá výjimku, pokud není nakonfigurován žádný výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="55a68-868">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="55a68-869">parametry `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="55a68-869">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="55a68-870">`filename` je cesta a název souboru certifikátu vzhledem k adresáři, který obsahuje soubory obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="55a68-870">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="55a68-871">`password` je heslo vyžadované pro přístup k datům certifikátu X. 509.</span><span class="sxs-lookup"><span data-stu-id="55a68-871">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="55a68-872">`configureOptions` je `Action` ke konfiguraci `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="55a68-872">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="55a68-873">Vrátí `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="55a68-873">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="55a68-874">`storeName` je úložiště certifikátů, ze kterého se má certifikát načíst.</span><span class="sxs-lookup"><span data-stu-id="55a68-874">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="55a68-875">`subject` je název subjektu certifikátu.</span><span class="sxs-lookup"><span data-stu-id="55a68-875">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="55a68-876">`allowInvalid` označuje, zda by měly být zváženy neplatné certifikáty, například certifikáty podepsané svým držitelem.</span><span class="sxs-lookup"><span data-stu-id="55a68-876">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="55a68-877">`location` je umístění úložiště, ze kterého se má načíst certifikát.</span><span class="sxs-lookup"><span data-stu-id="55a68-877">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="55a68-878">`serverCertificate` je certifikát X. 509.</span><span class="sxs-lookup"><span data-stu-id="55a68-878">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="55a68-879">V produkčním prostředí musí být HTTPS explicitně nakonfigurovaný.</span><span class="sxs-lookup"><span data-stu-id="55a68-879">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="55a68-880">Musí být zadán minimálně výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="55a68-880">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="55a68-881">Podporované konfigurace popsané dál:</span><span class="sxs-lookup"><span data-stu-id="55a68-881">Supported configurations described next:</span></span>

* <span data-ttu-id="55a68-882">Žádná konfigurace</span><span class="sxs-lookup"><span data-stu-id="55a68-882">No configuration</span></span>
* <span data-ttu-id="55a68-883">Nahradit výchozí certifikát z konfigurace</span><span class="sxs-lookup"><span data-stu-id="55a68-883">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="55a68-884">Změna výchozích hodnot v kódu</span><span class="sxs-lookup"><span data-stu-id="55a68-884">Change the defaults in code</span></span>

<span data-ttu-id="55a68-885">*Žádná konfigurace*</span><span class="sxs-lookup"><span data-stu-id="55a68-885">*No configuration*</span></span>

<span data-ttu-id="55a68-886">Kestrel naslouchá na `http://localhost:5000` a `https://localhost:5001` (Pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="55a68-886">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="55a68-887">*Nahradit výchozí certifikát z konfigurace*</span><span class="sxs-lookup"><span data-stu-id="55a68-887">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="55a68-888">`CreateDefaultBuilder` volá ve výchozím nastavení `Configure(context.Configuration.GetSection("Kestrel"))`, aby se načetla konfigurace Kestrel.</span><span class="sxs-lookup"><span data-stu-id="55a68-888">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="55a68-889">Pro Kestrel je k dispozici výchozí schéma konfigurace nastavení aplikace HTTPS.</span><span class="sxs-lookup"><span data-stu-id="55a68-889">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="55a68-890">Nakonfigurujte několik koncových bodů, včetně adres URL a certifikátů, které se mají použít, buď ze souboru na disku, nebo z úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="55a68-890">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="55a68-891">V následujícím příkladu *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="55a68-891">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="55a68-892">Nastavte **AllowInvalid** na `true`, pokud chcete povolit použití neplatných certifikátů (například certifikátů podepsaných svým držitelem).</span><span class="sxs-lookup"><span data-stu-id="55a68-892">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="55a68-893">Libovolný koncový bod HTTPS, který neurčuje certifikát (**HttpsDefaultCert** v následujícím příkladu), se vrátí k certifikátu definovanému v části **certifikáty** > **výchozí** nebo vývojový certifikát.</span><span class="sxs-lookup"><span data-stu-id="55a68-893">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="55a68-894">Alternativou k použití **cesty** a **hesla** pro libovolný uzel certifikátu je zadání certifikátu pomocí polí úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="55a68-894">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="55a68-895">Například **certifikáty** > **výchozí** certifikát lze zadat jako:</span><span class="sxs-lookup"><span data-stu-id="55a68-895">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="55a68-896">Poznámky ke schématu:</span><span class="sxs-lookup"><span data-stu-id="55a68-896">Schema notes:</span></span>

* <span data-ttu-id="55a68-897">V názvech koncových bodů se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="55a68-897">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="55a68-898">Například `HTTPS` a `Https` jsou platné.</span><span class="sxs-lookup"><span data-stu-id="55a68-898">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="55a68-899">U každého koncového bodu je vyžadován parametr `Url`.</span><span class="sxs-lookup"><span data-stu-id="55a68-899">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="55a68-900">Formát tohoto parametru je stejný jako konfigurační parametr `Urls` nejvyšší úrovně s tím rozdílem, že je omezen na jedinou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="55a68-900">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="55a68-901">Tyto koncové body nahrazují hodnoty definované v konfiguraci `Urls` na nejvyšší úrovni místo jejich přidávání do těchto koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="55a68-901">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="55a68-902">Koncové body definované v kódu prostřednictvím `Listen` jsou kumulativní s koncovými body definovanými v konfiguračním oddílu.</span><span class="sxs-lookup"><span data-stu-id="55a68-902">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="55a68-903">Oddíl `Certificate` je nepovinný.</span><span class="sxs-lookup"><span data-stu-id="55a68-903">The `Certificate` section is optional.</span></span> <span data-ttu-id="55a68-904">Pokud se nezadá oddíl `Certificate`, použijí se výchozí hodnoty definované v dřívějších scénářích.</span><span class="sxs-lookup"><span data-stu-id="55a68-904">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="55a68-905">Pokud nejsou k dispozici žádné výchozí hodnoty, Server vyvolá výjimku a nemůže se spustit.</span><span class="sxs-lookup"><span data-stu-id="55a68-905">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="55a68-906">Oddíl `Certificate` podporuje jak **cesty**&ndash;**hesla** , tak&ndash;ho **úložiště** **subjektu** .</span><span class="sxs-lookup"><span data-stu-id="55a68-906">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="55a68-907">V takovém případě může být definován libovolný počet koncových bodů, pokud to nezpůsobí konflikty portů.</span><span class="sxs-lookup"><span data-stu-id="55a68-907">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="55a68-908">`options.Configure(context.Configuration.GetSection("{SECTION}"))` vrátí `KestrelConfigurationLoader` s metodou `.Endpoint(string name, listenOptions => { })`, která se dá použít k doplnění nastavení nakonfigurovaného koncového bodu:</span><span class="sxs-lookup"><span data-stu-id="55a68-908">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="55a68-909">`KestrelServerOptions.ConfigurationLoader` se dá získat přímo k pokračování v iteraci u stávajícího zavaděče, jako je třeba ten, který poskytuje <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="55a68-909">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="55a68-910">Konfigurační oddíl pro každý koncový bod je k dispozici na možnostech v metodě `Endpoint`, aby bylo možné číst vlastní nastavení.</span><span class="sxs-lookup"><span data-stu-id="55a68-910">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="55a68-911">Více konfigurací může být načteno voláním `options.Configure(context.Configuration.GetSection("{SECTION}"))` znovu s jiným oddílem.</span><span class="sxs-lookup"><span data-stu-id="55a68-911">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="55a68-912">Je použita pouze poslední konfigurace, pokud není metoda `Load` explicitně volána pro předchozí instance.</span><span class="sxs-lookup"><span data-stu-id="55a68-912">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="55a68-913">Metapackage nevolá `Load`, takže jeho výchozí konfigurační oddíl může být nahrazen.</span><span class="sxs-lookup"><span data-stu-id="55a68-913">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="55a68-914">`KestrelConfigurationLoader` zrcadlí `Listen` rodinu rozhraní API od `KestrelServerOptions` jako přetížení `Endpoint`, takže koncové body kódu a konfigurace lze konfigurovat na stejném místě.</span><span class="sxs-lookup"><span data-stu-id="55a68-914">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="55a68-915">Tato přetížení nepoužívají názvy a využívají pouze výchozí nastavení z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="55a68-915">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="55a68-916">*Změna výchozích hodnot v kódu*</span><span class="sxs-lookup"><span data-stu-id="55a68-916">*Change the defaults in code*</span></span>

<span data-ttu-id="55a68-917">`ConfigureEndpointDefaults` a `ConfigureHttpsDefaults` lze použít ke změně výchozího nastavení pro `ListenOptions` a `HttpsConnectionAdapterOptions`, včetně přepsání výchozího certifikátu zadaného v předchozím scénáři.</span><span class="sxs-lookup"><span data-stu-id="55a68-917">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="55a68-918">`ConfigureEndpointDefaults` a `ConfigureHttpsDefaults` by měly být volány před konfigurací koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="55a68-918">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="55a68-919">*Podpora Kestrel pro SNI*</span><span class="sxs-lookup"><span data-stu-id="55a68-919">*Kestrel support for SNI*</span></span>

<span data-ttu-id="55a68-920">[Indikace názvu serveru (SNI)](https://tools.ietf.org/html/rfc6066#section-3) lze použít k hostování více domén na stejné IP adrese a portu.</span><span class="sxs-lookup"><span data-stu-id="55a68-920">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="55a68-921">Aby SNI fungovalo, klient pošle název hostitele pro zabezpečenou relaci serveru během metody handshake TLS, aby server mohl poskytovat správný certifikát.</span><span class="sxs-lookup"><span data-stu-id="55a68-921">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="55a68-922">Klient používá poskytnutý certifikát pro šifrovanou komunikaci se serverem během zabezpečené relace, která následuje za metodou handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="55a68-922">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="55a68-923">Kestrel podporuje SNI prostřednictvím zpětného volání `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="55a68-923">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="55a68-924">Zpětné volání je vyvoláno jednou za připojení, které aplikaci umožní zkontrolovat název hostitele a vybrat příslušný certifikát.</span><span class="sxs-lookup"><span data-stu-id="55a68-924">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="55a68-925">Podpora SNI vyžaduje:</span><span class="sxs-lookup"><span data-stu-id="55a68-925">SNI support requires:</span></span>

* <span data-ttu-id="55a68-926">Spuštění v cílovém rozhraní `netcoreapp2.1` nebo novější.</span><span class="sxs-lookup"><span data-stu-id="55a68-926">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="55a68-927">V `net461` nebo novějším je zpětné volání vyvoláno, ale `name` je vždy `null`.</span><span class="sxs-lookup"><span data-stu-id="55a68-927">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="55a68-928">`name` taky `null`, pokud klient neposkytne parametr názvu hostitele v handshaki TLS.</span><span class="sxs-lookup"><span data-stu-id="55a68-928">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="55a68-929">Všechny weby běží na stejné instanci Kestrel.</span><span class="sxs-lookup"><span data-stu-id="55a68-929">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="55a68-930">Kestrel nepodporuje sdílení IP adresy a portu napříč několika instancemi bez reverzního proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="55a68-930">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="55a68-931">Protokolování připojení</span><span class="sxs-lookup"><span data-stu-id="55a68-931">Connection logging</span></span>

<span data-ttu-id="55a68-932">Volání <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> k vygenerování protokolů na úrovni ladění pro komunikaci na úrovni bajtů v rámci připojení.</span><span class="sxs-lookup"><span data-stu-id="55a68-932">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="55a68-933">Protokolování připojení je užitečné při řešení problémů v komunikaci nízké úrovně, například během šifrování TLS a za proxy servery.</span><span class="sxs-lookup"><span data-stu-id="55a68-933">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="55a68-934">Pokud je `UseConnectionLogging` před `UseHttps`, zaprotokoluje se zašifrovaný provoz.</span><span class="sxs-lookup"><span data-stu-id="55a68-934">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="55a68-935">Je-li po `UseHttps`umístěn `UseConnectionLogging`, zašifrovaný provoz se zaznamená do protokolu.</span><span class="sxs-lookup"><span data-stu-id="55a68-935">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="55a68-936">Vytvoření vazby na soket TCP</span><span class="sxs-lookup"><span data-stu-id="55a68-936">Bind to a TCP socket</span></span>

<span data-ttu-id="55a68-937">Metoda <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> se váže k soketu TCP a obslužná rutina options umožňuje konfiguraci certifikátu X. 509:</span><span class="sxs-lookup"><span data-stu-id="55a68-937">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

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

<span data-ttu-id="55a68-938">Příklad konfiguruje HTTPS pro koncový bod s <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="55a68-938">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="55a68-939">Ke konfiguraci dalších nastavení Kestrel pro konkrétní koncové body použijte stejné rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="55a68-939">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="55a68-940">Vytvoření vazby na soket UNIX</span><span class="sxs-lookup"><span data-stu-id="55a68-940">Bind to a Unix socket</span></span>

<span data-ttu-id="55a68-941">Naslouchat na soketu se systémem UNIX pomocí <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> pro zlepšení výkonu pomocí Nginx, jak je znázorněno v tomto příkladu:</span><span class="sxs-lookup"><span data-stu-id="55a68-941">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

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

### <a name="port-0"></a><span data-ttu-id="55a68-942">Port 0</span><span class="sxs-lookup"><span data-stu-id="55a68-942">Port 0</span></span>

<span data-ttu-id="55a68-943">Když je zadané číslo portu `0`, dynamicky se váže k dostupnému portu.</span><span class="sxs-lookup"><span data-stu-id="55a68-943">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="55a68-944">Následující příklad ukazuje, jak určit, který port Kestrel je skutečně vázaný za běhu:</span><span class="sxs-lookup"><span data-stu-id="55a68-944">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="55a68-945">Když se aplikace spustí, výstup okna konzoly indikuje dynamický port, na který se aplikace dá získat:</span><span class="sxs-lookup"><span data-stu-id="55a68-945">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="55a68-946">Omezení</span><span class="sxs-lookup"><span data-stu-id="55a68-946">Limitations</span></span>

<span data-ttu-id="55a68-947">Konfigurace koncových bodů pomocí následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="55a68-947">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="55a68-948">argument příkazového řádku `--urls`</span><span class="sxs-lookup"><span data-stu-id="55a68-948">`--urls` command-line argument</span></span>
* <span data-ttu-id="55a68-949">konfigurační klíč hostitele `urls`</span><span class="sxs-lookup"><span data-stu-id="55a68-949">`urls` host configuration key</span></span>
* <span data-ttu-id="55a68-950">`ASPNETCORE_URLS` proměnná prostředí</span><span class="sxs-lookup"><span data-stu-id="55a68-950">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="55a68-951">Tyto metody jsou užitečné, pokud chcete, aby kód pracoval na jiných serverech než Kestrel.</span><span class="sxs-lookup"><span data-stu-id="55a68-951">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="55a68-952">Mějte ale na paměti následující omezení:</span><span class="sxs-lookup"><span data-stu-id="55a68-952">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="55a68-953">Protokol HTTPS nelze s těmito přístupy použít, pokud není k dispozici výchozí certifikát v konfiguraci koncového bodu HTTPS (například pomocí konfigurace `KestrelServerOptions` nebo konfiguračního souboru, jak je uvedeno výše v tomto tématu).</span><span class="sxs-lookup"><span data-stu-id="55a68-953">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="55a68-954">Při současném použití `Listen` i `UseUrls` jsou koncové body `Listen` popsány v koncových bodech `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="55a68-954">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="55a68-955">Konfigurace koncového bodu služby IIS</span><span class="sxs-lookup"><span data-stu-id="55a68-955">IIS endpoint configuration</span></span>

<span data-ttu-id="55a68-956">Při použití služby IIS jsou vazby adresy URL pro přepsání vazby služby IIS nastaveny buď pomocí `Listen`, nebo `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="55a68-956">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="55a68-957">Další informace najdete v tématu [ASP.NET Core modulu](xref:host-and-deploy/aspnet-core-module) .</span><span class="sxs-lookup"><span data-stu-id="55a68-957">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="55a68-958">Konfigurace přenosu</span><span class="sxs-lookup"><span data-stu-id="55a68-958">Transport configuration</span></span>

<span data-ttu-id="55a68-959">S vydáním ASP.NET Core 2,1 není výchozí přenos Kestrel založen na Libuv, ale na základě spravovaných soketů.</span><span class="sxs-lookup"><span data-stu-id="55a68-959">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="55a68-960">Jedná se o zásadní změnu pro aplikace ASP.NET Core 2,0 upgradované na 2,1, která volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> a závisí na jednom z následujících balíčků:</span><span class="sxs-lookup"><span data-stu-id="55a68-960">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="55a68-961">[Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (přímý odkaz na balíček)</span><span class="sxs-lookup"><span data-stu-id="55a68-961">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="55a68-962">Microsoft. AspNetCore. app</span><span class="sxs-lookup"><span data-stu-id="55a68-962">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="55a68-963">Pro projekty, které vyžadují použití Libuv:</span><span class="sxs-lookup"><span data-stu-id="55a68-963">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="55a68-964">Přidejte do souboru projektu aplikace závislost pro balíček [Microsoft. AspNetCore. Server. Kestrel. Transport. Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) :</span><span class="sxs-lookup"><span data-stu-id="55a68-964">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="55a68-965">Volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span><span class="sxs-lookup"><span data-stu-id="55a68-965">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="55a68-966">Předpony adresy URL</span><span class="sxs-lookup"><span data-stu-id="55a68-966">URL prefixes</span></span>

<span data-ttu-id="55a68-967">Při použití `UseUrls`, `--urls` argumentu příkazového řádku, `urls` konfigurační klíč hostitele nebo proměnná prostředí `ASPNETCORE_URLS`, můžou být předpony adresy URL v některém z následujících formátů.</span><span class="sxs-lookup"><span data-stu-id="55a68-967">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="55a68-968">Platné jsou pouze předpony adresy URL protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="55a68-968">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="55a68-969">Kestrel nepodporuje HTTPS při konfiguraci vazeb URL pomocí `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="55a68-969">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="55a68-970">Adresa IPv4 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="55a68-970">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="55a68-971">`0.0.0.0` je zvláštní případ, který se váže ke všem adresám IPv4.</span><span class="sxs-lookup"><span data-stu-id="55a68-971">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="55a68-972">Adresa IPv6 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="55a68-972">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="55a68-973">`[::]` je ekvivalent protokolu IPv4 `0.0.0.0`protokolu IPv4.</span><span class="sxs-lookup"><span data-stu-id="55a68-973">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="55a68-974">Název hostitele s číslem portu</span><span class="sxs-lookup"><span data-stu-id="55a68-974">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="55a68-975">Názvy hostitelů `*` a `+` nejsou speciální.</span><span class="sxs-lookup"><span data-stu-id="55a68-975">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="55a68-976">Cokoli se nerozpoznalo jako platná IP adresa nebo `localhost` váže ke všem IP adresám IPv4 a IPv6.</span><span class="sxs-lookup"><span data-stu-id="55a68-976">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="55a68-977">Pokud chcete navazovat různé názvy hostitelů na různé ASP.NET Core aplikace na stejném portu, použijte [http. sys](xref:fundamentals/servers/httpsys) nebo reverzní proxy server, třeba IIS, Nginx nebo Apache.</span><span class="sxs-lookup"><span data-stu-id="55a68-977">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="55a68-978">Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="55a68-978">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="55a68-979">Název hostitele `localhost` s číslem portu nebo IP adresou zpětné smyčky s číslem portu</span><span class="sxs-lookup"><span data-stu-id="55a68-979">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="55a68-980">Pokud je zadána hodnota `localhost`, pokusí se Kestrel vytvořit propojení s rozhraními zpětné smyčky IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="55a68-980">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="55a68-981">Pokud je požadovaný port používán jinou službou na rozhraní zpětné smyčky, spuštění Kestrel se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="55a68-981">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="55a68-982">Pokud je rozhraní zpětné smyčky v žádném jiném případě nedostupné (většinou kvůli tomu, že protokol IPv6 není podporovaný), Kestrel zaznamená upozornění.</span><span class="sxs-lookup"><span data-stu-id="55a68-982">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="55a68-983">Filtrování hostitele</span><span class="sxs-lookup"><span data-stu-id="55a68-983">Host filtering</span></span>

<span data-ttu-id="55a68-984">I když Kestrel podporuje konfiguraci na základě prefixů, jako je například `http://example.com:5000`, Kestrel převážně ignoruje název hostitele.</span><span class="sxs-lookup"><span data-stu-id="55a68-984">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="55a68-985">Hostitelská `localhost` je zvláštní případ, který se používá pro vazbu na adresy zpětné smyčky.</span><span class="sxs-lookup"><span data-stu-id="55a68-985">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="55a68-986">Každý hostitel, který je jiný než explicitní IP adresa, se váže na všechny veřejné IP adresy.</span><span class="sxs-lookup"><span data-stu-id="55a68-986">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="55a68-987">hlavičky `Host` nejsou ověřeny.</span><span class="sxs-lookup"><span data-stu-id="55a68-987">`Host` headers aren't validated.</span></span>

<span data-ttu-id="55a68-988">Jako alternativní řešení použijte middleware pro filtrování hostitele.</span><span class="sxs-lookup"><span data-stu-id="55a68-988">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="55a68-989">Middleware pro filtrování hostitele poskytuje balíček [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , který je součástí [Microsoft. AspNetCore. App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 nebo 2,2).</span><span class="sxs-lookup"><span data-stu-id="55a68-989">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="55a68-990">Middleware se přidá pomocí <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, která volá <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="55a68-990">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="55a68-991">Middleware pro filtrování hostitele je ve výchozím nastavení zakázáno.</span><span class="sxs-lookup"><span data-stu-id="55a68-991">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="55a68-992">Chcete-li povolit middleware, definujte `AllowedHosts` klíč v souboru *appSettings. json*/*appSettings.\<prostředí >. JSON*.</span><span class="sxs-lookup"><span data-stu-id="55a68-992">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="55a68-993">Hodnota je seznam názvů hostitelů oddělených středníkem bez čísel portů:</span><span class="sxs-lookup"><span data-stu-id="55a68-993">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="55a68-994">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="55a68-994">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="55a68-995">[Middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer) má taky možnost <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="55a68-995">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="55a68-996">Middleware předávaných hlaviček a middleware pro filtrování hostitele mají podobné funkce pro různé scénáře.</span><span class="sxs-lookup"><span data-stu-id="55a68-996">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="55a68-997">Nastavení `AllowedHosts` s předaným middlewarem hlaviček je vhodné, když se hlavička `Host` nezachová při předávání požadavků pomocí reverzního proxy server nebo nástroje pro vyrovnávání zatížení.</span><span class="sxs-lookup"><span data-stu-id="55a68-997">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="55a68-998">Nastavení `AllowedHosts` pomocí middlewaru pro filtrování hostitele je vhodné, pokud se Kestrel používá jako veřejný hraniční Server nebo když je hlavička `Host` přímo předána.</span><span class="sxs-lookup"><span data-stu-id="55a68-998">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="55a68-999">Další informace o middlewaru předávaných hlaviček najdete v tématu <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="55a68-999">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="55a68-1000">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="55a68-1000">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="55a68-1001">RFC 7230: syntaxe zprávy a směrování (oddíl 5,4: hostitel)</span><span class="sxs-lookup"><span data-stu-id="55a68-1001">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
