---
title: Implementace webového serveru Kestrel v ASP.NET Core
author: guardrex
description: Přečtěte si o Kestrel, webovém serveru pro různé platformy pro ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2019
uid: fundamentals/servers/kestrel
ms.openlocfilehash: 5565011f6531ef5e95eb02f310e7107f9ed547b2
ms.sourcegitcommit: dd026eceee79e943bd6b4a37b144803b50617583
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72378871"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="fd14e-103">Implementace webového serveru Kestrel v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fd14e-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="fd14e-104">Tím, že [Dykstra](https://github.com/tdykstra), [Chris Rossův](https://github.com/Tratcher), [Stephen zastavili](https://twitter.com/halter73)a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="fd14e-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), [Stephen Halter](https://twitter.com/halter73), and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fd14e-105">Kestrel je [webový server pro různé platformy pro ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="fd14e-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="fd14e-106">Kestrel je webový server, který je ve výchozím nastavení součástí ASP.NET Core šablon projektů.</span><span class="sxs-lookup"><span data-stu-id="fd14e-106">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="fd14e-107">Kestrel podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="fd14e-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="fd14e-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="fd14e-108">HTTPS</span></span>
* <span data-ttu-id="fd14e-109">Neprůhledný upgrade používaný k povolení [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="fd14e-109">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="fd14e-110">Technologie UNIX Sockets s vysokým výkonem za Nginx</span><span class="sxs-lookup"><span data-stu-id="fd14e-110">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="fd14e-111">HTTP/2 (s výjimkou macOS @ no__t-0)</span><span class="sxs-lookup"><span data-stu-id="fd14e-111">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="fd14e-112">@no__t – 0HTTP/2 bude v budoucí verzi podporován v macOS.</span><span class="sxs-lookup"><span data-stu-id="fd14e-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="fd14e-113">Kestrel se podporuje na všech platformách a verzích, které podporuje .NET Core.</span><span class="sxs-lookup"><span data-stu-id="fd14e-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="fd14e-114">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fd14e-114">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="fd14e-115">Podpora HTTP/2</span><span class="sxs-lookup"><span data-stu-id="fd14e-115">HTTP/2 support</span></span>

<span data-ttu-id="fd14e-116">[Http/2](https://httpwg.org/specs/rfc7540.html) je k dispozici pro ASP.NET Core aplikace, pokud jsou splněné následující základní požadavky:</span><span class="sxs-lookup"><span data-stu-id="fd14e-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="fd14e-117">Operační systém @ no__t-0</span><span class="sxs-lookup"><span data-stu-id="fd14e-117">Operating system&dagger;</span></span>
  * <span data-ttu-id="fd14e-118">Windows Server 2016/Windows 10 nebo novější @ no__t-0</span><span class="sxs-lookup"><span data-stu-id="fd14e-118">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="fd14e-119">Linux s OpenSSL 1.0.2 nebo novějším (například Ubuntu 16,04 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="fd14e-119">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="fd14e-120">Cílová architektura: .NET Core 2,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="fd14e-120">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="fd14e-121">Připojení [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="fd14e-121">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="fd14e-122">Připojení TLS 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="fd14e-122">TLS 1.2 or later connection</span></span>

<span data-ttu-id="fd14e-123">@no__t – 0HTTP/2 bude v budoucí verzi podporován v macOS.</span><span class="sxs-lookup"><span data-stu-id="fd14e-123">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="fd14e-124">@no__t – 0Kestrel má omezená podpora HTTP/2 na Windows Serveru 2012 R2 a Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="fd14e-124">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="fd14e-125">Podpora je omezená, protože seznam podporovaných šifrovacích sad TLS, které jsou k dispozici v těchto operačních systémech, je omezený.</span><span class="sxs-lookup"><span data-stu-id="fd14e-125">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="fd14e-126">Pro zabezpečení připojení TLS může být vyžadován certifikát vygenerovaný pomocí algoritmu ECDSA (s připojením typu eliptická křivka).</span><span class="sxs-lookup"><span data-stu-id="fd14e-126">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="fd14e-127">Pokud je navázáno připojení HTTP/2, sestavy [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-127">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="fd14e-128">HTTP/2 je ve výchozím nastavení zakázané.</span><span class="sxs-lookup"><span data-stu-id="fd14e-128">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="fd14e-129">Další informace o konfiguraci najdete v částech [Možnosti Kestrel](#kestrel-options) a [ListenOptions. Protocols](#listenoptionsprotocols) .</span><span class="sxs-lookup"><span data-stu-id="fd14e-129">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="fd14e-130">Kdy použít Kestrel s reverzním proxy serverem</span><span class="sxs-lookup"><span data-stu-id="fd14e-130">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="fd14e-131">Kestrel může používat sám sebe nebo s *reverzními proxy server*, jako je [Internetová informační služba (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="fd14e-131">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="fd14e-132">Reverzní proxy server přijímá požadavky HTTP ze sítě a přesměruje je na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="fd14e-132">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="fd14e-133">Kestrel použitý jako hraniční webový server (internetový):</span><span class="sxs-lookup"><span data-stu-id="fd14e-133">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel komunikuje přímo s internetem bez zpětného proxy server](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="fd14e-135">Kestrel se používá v konfiguraci reverzního proxy serveru:</span><span class="sxs-lookup"><span data-stu-id="fd14e-135">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel nepřímo komunikuje s internetem prostřednictvím reverzní proxy server, jako je IIS, Nginx nebo Apache.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="fd14e-137">Buď konfigurace, s zpětným proxy server nebo bez něj, je podporovaná konfigurace hostování.</span><span class="sxs-lookup"><span data-stu-id="fd14e-137">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="fd14e-138">Kestrel používané jako hraniční Server bez reverzních proxy server nepodporuje sdílení stejné IP adresy a portu mezi více procesy.</span><span class="sxs-lookup"><span data-stu-id="fd14e-138">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="fd14e-139">Když je Kestrel nakonfigurovaný k naslouchání na portu, Kestrel zpracovává veškerý provoz tohoto portu bez ohledu na hlavičky `Host`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-139">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="fd14e-140">Reverzní proxy server, který může sdílet porty, má schopnost přesměrovat požadavky na Kestrel na jedinečné IP adresy a porty.</span><span class="sxs-lookup"><span data-stu-id="fd14e-140">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="fd14e-141">I v případě, že se zpětný proxy server nepožaduje, může být použití reverzní proxy server vhodnou volbou.</span><span class="sxs-lookup"><span data-stu-id="fd14e-141">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="fd14e-142">Reverzní proxy:</span><span class="sxs-lookup"><span data-stu-id="fd14e-142">A reverse proxy:</span></span>

* <span data-ttu-id="fd14e-143">Může omezit vystavenou veřejnou plochu v aplikacích, které hostuje.</span><span class="sxs-lookup"><span data-stu-id="fd14e-143">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="fd14e-144">Poskytněte další vrstvu konfigurace a obrany.</span><span class="sxs-lookup"><span data-stu-id="fd14e-144">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="fd14e-145">Může se lépe integrovat se stávající infrastrukturou.</span><span class="sxs-lookup"><span data-stu-id="fd14e-145">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="fd14e-146">Zjednodušte vyrovnávání zatížení a konfiguraci zabezpečené komunikace (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="fd14e-146">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="fd14e-147">Pouze reverzní proxy server vyžaduje certifikát X. 509 a tento server může komunikovat se servery aplikace v interní síti pomocí prostého protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="fd14e-147">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="fd14e-148">Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="fd14e-148">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="fd14e-149">Použití Kestrel v aplikacích ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fd14e-149">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="fd14e-150">ASP.NET Core šablony projektu ve výchozím nastavení používají Kestrel.</span><span class="sxs-lookup"><span data-stu-id="fd14e-150">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="fd14e-151">V *program.cs*kód šablony volá <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>, která volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> na pozadí.</span><span class="sxs-lookup"><span data-stu-id="fd14e-151">In *Program.cs*, the template code calls <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="fd14e-152">Další informace o @no__t – 0 a vytváření hostitele najdete v oddílech nastavení *hostitele* a *výchozí tvůrce* pro <xref:fundamentals/host/generic-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="fd14e-152">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="fd14e-153">Pokud chcete po volání `CreateDefaultBuilder` a `ConfigureWebHostDefaults` zadat další konfiguraci, použijte `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="fd14e-153">To provide additional configuration after calling `CreateDefaultBuilder` and `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

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

<span data-ttu-id="fd14e-154">Pokud aplikace nevolá `CreateDefaultBuilder` pro nastavení hostitele, **před** voláním `ConfigureKestrel` volejte <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span><span class="sxs-lookup"><span data-stu-id="fd14e-154">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="fd14e-155">Kestrel možnosti</span><span class="sxs-lookup"><span data-stu-id="fd14e-155">Kestrel options</span></span>

<span data-ttu-id="fd14e-156">Webový server Kestrel má možnosti konfigurace omezení, které jsou zvláště užitečné v internetových nasazeních.</span><span class="sxs-lookup"><span data-stu-id="fd14e-156">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="fd14e-157">Nastavte omezení pro vlastnost <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> třídy <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="fd14e-157">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="fd14e-158">Vlastnost `Limits` obsahuje instanci třídy <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="fd14e-158">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="fd14e-159">V následujících příkladech se používá obor názvů <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:</span><span class="sxs-lookup"><span data-stu-id="fd14e-159">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="fd14e-160">Možnosti Kestrel, které jsou konfigurovány C# v kódu v následujících příkladech, lze také nastavit pomocí [poskytovatele konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="fd14e-160">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="fd14e-161">Poskytovatel konfigurace souboru může například načíst konfiguraci Kestrel z objektu *appSettings. JSON* nebo *appSettings. { Soubor Environment}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="fd14e-161">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="fd14e-162">Použijte **jeden** z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="fd14e-162">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="fd14e-163">Konfigurace Kestrel v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="fd14e-163">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="fd14e-164">Vloží instanci `IConfiguration` do třídy `Startup`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-164">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="fd14e-165">Následující příklad předpokládá, že vložená konfigurace je přiřazena vlastnosti `Configuration`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-165">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="fd14e-166">V `Startup.ConfigureServices` načtěte oddíl `Kestrel` konfigurace do konfigurace Kestrel.</span><span class="sxs-lookup"><span data-stu-id="fd14e-166">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration.</span></span>

     ```csharp
     // using Microsoft.Extensions.Configuration

     public void ConfigureServices(IServiceCollection services)
     {
         services.Configure<KestrelServerOptions>(
             Configuration.GetSection("Kestrel"));
     }
     ```

* <span data-ttu-id="fd14e-167">Nakonfigurovat Kestrel při sestavování hostitele:</span><span class="sxs-lookup"><span data-stu-id="fd14e-167">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="fd14e-168">V *program.cs*do konfigurace Kestrel načtěte oddíl `Kestrel` konfigurace:</span><span class="sxs-lookup"><span data-stu-id="fd14e-168">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="fd14e-169">Obě předchozí přístupy fungují u libovolného [poskytovatele konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="fd14e-169">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="fd14e-170">Časový limit Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="fd14e-170">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="fd14e-171">Získá nebo nastaví [časový limit zachování](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="fd14e-171">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="fd14e-172">Výchozí hodnota je 2 minuty.</span><span class="sxs-lookup"><span data-stu-id="fd14e-172">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="fd14e-173">Maximální počet připojení klientů</span><span class="sxs-lookup"><span data-stu-id="fd14e-173">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="fd14e-174">Maximální počet souběžných otevřených připojení TCP lze pro celou aplikaci nastavit pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="fd14e-174">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="fd14e-175">Existuje zvláštní omezení pro připojení, která byla upgradována z protokolu HTTP nebo HTTPS na jiný protokol (například na žádost WebSockets).</span><span class="sxs-lookup"><span data-stu-id="fd14e-175">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="fd14e-176">Po upgradu připojení se nepočítá s limitem @no__t 0.</span><span class="sxs-lookup"><span data-stu-id="fd14e-176">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="fd14e-177">Ve výchozím nastavení je maximální počet připojení neomezené (null).</span><span class="sxs-lookup"><span data-stu-id="fd14e-177">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="fd14e-178">Maximální velikost textu požadavku</span><span class="sxs-lookup"><span data-stu-id="fd14e-178">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="fd14e-179">Výchozí maximální velikost textu požadavku je 30 000 000 bajtů, což je přibližně 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="fd14e-179">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="fd14e-180">Doporučený postup pro přepsání limitu v aplikaci ASP.NET Core MVC je použití atributu <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> u metody akce:</span><span class="sxs-lookup"><span data-stu-id="fd14e-180">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="fd14e-181">Tady je příklad, který ukazuje, jak nakonfigurovat omezení pro aplikaci na každém požadavku:</span><span class="sxs-lookup"><span data-stu-id="fd14e-181">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="fd14e-182">Přepsat nastavení u konkrétního požadavku v middleware:</span><span class="sxs-lookup"><span data-stu-id="fd14e-182">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="fd14e-183">Výjimka je vyvolána, pokud aplikace po zahájení aplikace nakonfiguruje limit na žádost.</span><span class="sxs-lookup"><span data-stu-id="fd14e-183">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="fd14e-184">Existuje vlastnost `IsReadOnly`, která určuje, zda je vlastnost `MaxRequestBodySize` ve stavu jen pro čtení, což znamená, že je příliš pozdě pro konfiguraci limitu.</span><span class="sxs-lookup"><span data-stu-id="fd14e-184">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="fd14e-185">Když je aplikace spouštěna [mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za [ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module), je omezení velikosti textu požadavku Kestrel zakázané, protože služba IIS tento limit již nastavuje.</span><span class="sxs-lookup"><span data-stu-id="fd14e-185">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="fd14e-186">Minimální rychlost těla zprávy požadavku</span><span class="sxs-lookup"><span data-stu-id="fd14e-186">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="fd14e-187">Kestrel kontroluje každou sekundu, pokud data přicházejí za zadanou rychlost v bajtech za sekundu.</span><span class="sxs-lookup"><span data-stu-id="fd14e-187">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="fd14e-188">Pokud frekvence klesne pod minimální hodnotu, vypršel časový limit připojení. Doba odkladu je doba, kterou Kestrel umožňuje klientovi zvýšit rychlost odesílání až na minimum; frekvence není během této doby kontrolována.</span><span class="sxs-lookup"><span data-stu-id="fd14e-188">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="fd14e-189">Doba odkladu pomáhá vyhnout se vyřazení připojení, která při počátečním posílání dat zpomalují rychlost, kvůli pomalému spuštění protokolu TCP.</span><span class="sxs-lookup"><span data-stu-id="fd14e-189">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="fd14e-190">Výchozí minimální sazba je 240 bajtů za sekundu s obdobím 5 sekund odkladu.</span><span class="sxs-lookup"><span data-stu-id="fd14e-190">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="fd14e-191">Pro odpověď platí také minimální sazba.</span><span class="sxs-lookup"><span data-stu-id="fd14e-191">A minimum rate also applies to the response.</span></span> <span data-ttu-id="fd14e-192">Kód pro nastavení limitu požadavků a limit odpovědi je stejný s výjimkou `RequestBody` nebo `Response` v názvech vlastností a rozhraní.</span><span class="sxs-lookup"><span data-stu-id="fd14e-192">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="fd14e-193">Tady je příklad, který ukazuje, jak nakonfigurovat minimální datové tarify v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="fd14e-193">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="fd14e-194">Přepsat minimální limity přenosové rychlosti na žádost v middlewaru:</span><span class="sxs-lookup"><span data-stu-id="fd14e-194">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="fd14e-195">@No__t-0, na které se odkazuje v předchozí ukázce, není k dispozici v `HttpContext.Features` pro požadavky HTTP/2, protože Změna omezení přenosové rychlosti u jednotlivých požadavků je obecně Nepodporovaná pro HTTP/2 kvůli podpoře protokolu pro multiplexování požadavků.</span><span class="sxs-lookup"><span data-stu-id="fd14e-195">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="fd14e-196">@No__t-0 se ale pořád vyskytuje `HttpContext.Features` pro požadavky HTTP/2, protože limit míry čtení se může u požadavků na jednotlivé požadavky pořád *úplně vypnout* nastavením `IHttpMinRequestBodyDataRateFeature.MinDataRate` na `null` i pro požadavek HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="fd14e-196">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="fd14e-197">Při pokusu o čtení `IHttpMinRequestBodyDataRateFeature.MinDataRate` nebo při jeho nastavování na jinou hodnotu než `null` dojde k tomu, že se v důsledku požadavku HTTP/2 vyvolá @no__t 2.</span><span class="sxs-lookup"><span data-stu-id="fd14e-197">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="fd14e-198">Omezení přenosové rychlosti pro všechny servery nakonfigurovaná přes `KestrelServerOptions.Limits` se stále vztahují na připojení HTTP/1. x i HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="fd14e-198">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="fd14e-199">Časový limit hlaviček žádostí</span><span class="sxs-lookup"><span data-stu-id="fd14e-199">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="fd14e-200">Získá nebo nastaví maximální dobu, po kterou server stráví příjem hlaviček požadavků.</span><span class="sxs-lookup"><span data-stu-id="fd14e-200">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="fd14e-201">Výchozí hodnota je 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="fd14e-201">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="fd14e-202">Maximální počet proudů na připojení</span><span class="sxs-lookup"><span data-stu-id="fd14e-202">Maximum streams per connection</span></span>

<span data-ttu-id="fd14e-203">`Http2.MaxStreamsPerConnection` omezuje počet souběžných proudů požadavků na připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="fd14e-203">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="fd14e-204">Nadbytečné proudy jsou odmítnuty.</span><span class="sxs-lookup"><span data-stu-id="fd14e-204">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="fd14e-205">Výchozí hodnota je 100.</span><span class="sxs-lookup"><span data-stu-id="fd14e-205">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="fd14e-206">Velikost tabulky hlaviček</span><span class="sxs-lookup"><span data-stu-id="fd14e-206">Header table size</span></span>

<span data-ttu-id="fd14e-207">Dekodér HPACK dekomprimuje hlavičky HTTP pro připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="fd14e-207">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="fd14e-208">`Http2.HeaderTableSize` omezuje velikost kompresní tabulky hlaviček, kterou používá dekodér HPACK.</span><span class="sxs-lookup"><span data-stu-id="fd14e-208">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="fd14e-209">Hodnota je uvedena v oktetech a musí být větší než nula (0).</span><span class="sxs-lookup"><span data-stu-id="fd14e-209">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="fd14e-210">Výchozí hodnota je 4096.</span><span class="sxs-lookup"><span data-stu-id="fd14e-210">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="fd14e-211">Maximální velikost rámce</span><span class="sxs-lookup"><span data-stu-id="fd14e-211">Maximum frame size</span></span>

<span data-ttu-id="fd14e-212">`Http2.MaxFrameSize` označuje maximální povolenou velikost datové části rámce připojení HTTP/2 přijatého nebo odesílaného serverem.</span><span class="sxs-lookup"><span data-stu-id="fd14e-212">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="fd14e-213">Hodnota je uvedena v oktetech a musí být v rozmezí 2 ^ 14 (16 384) až 2 ^ 24-1 (16 777 215).</span><span class="sxs-lookup"><span data-stu-id="fd14e-213">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="fd14e-214">Výchozí hodnota je 2 ^ 14 (16 384).</span><span class="sxs-lookup"><span data-stu-id="fd14e-214">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="fd14e-215">Maximální velikost hlavičky požadavku</span><span class="sxs-lookup"><span data-stu-id="fd14e-215">Maximum request header size</span></span>

<span data-ttu-id="fd14e-216">`Http2.MaxRequestHeaderFieldSize` označuje maximální povolenou velikost v oktetech hodnot hlaviček požadavku.</span><span class="sxs-lookup"><span data-stu-id="fd14e-216">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="fd14e-217">Toto omezení se vztahuje na název i hodnotu v jejich komprimovaných a nekomprimovaných reprezentujcích.</span><span class="sxs-lookup"><span data-stu-id="fd14e-217">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="fd14e-218">Hodnota musí být větší než nula (0).</span><span class="sxs-lookup"><span data-stu-id="fd14e-218">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="fd14e-219">Výchozí hodnota je 8 192.</span><span class="sxs-lookup"><span data-stu-id="fd14e-219">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="fd14e-220">Velikost okna počátečního připojení</span><span class="sxs-lookup"><span data-stu-id="fd14e-220">Initial connection window size</span></span>

<span data-ttu-id="fd14e-221">`Http2.InitialConnectionWindowSize` označuje maximální velikost textu žádosti v bajtech. vyrovnávací paměti serveru se v jednom okamžiku agreguje napříč všemi požadavky (datovými proudy) na připojení.</span><span class="sxs-lookup"><span data-stu-id="fd14e-221">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="fd14e-222">Žádosti jsou také omezené pomocí `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-222">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="fd14e-223">Hodnota musí být větší než nebo rovna 65 535 a menší než 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="fd14e-223">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="fd14e-224">Výchozí hodnota je 128 KB (131 072).</span><span class="sxs-lookup"><span data-stu-id="fd14e-224">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="fd14e-225">Počáteční velikost okna datového proudu</span><span class="sxs-lookup"><span data-stu-id="fd14e-225">Initial stream window size</span></span>

<span data-ttu-id="fd14e-226">`Http2.InitialStreamWindowSize` označuje maximální velikost textu požadavku v bajtech na jednu žádost (Stream) na jednom místě.</span><span class="sxs-lookup"><span data-stu-id="fd14e-226">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="fd14e-227">Žádosti jsou také omezené pomocí `Http2.InitialConnectionWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-227">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="fd14e-228">Hodnota musí být větší než nebo rovna 65 535 a menší než 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="fd14e-228">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="fd14e-229">Výchozí hodnota je 96 KB (98 304).</span><span class="sxs-lookup"><span data-stu-id="fd14e-229">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="fd14e-230">Synchronní v/v</span><span class="sxs-lookup"><span data-stu-id="fd14e-230">Synchronous IO</span></span>

<span data-ttu-id="fd14e-231"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> řídí, zda je pro požadavek a odpověď povolena synchronní v/v.</span><span class="sxs-lookup"><span data-stu-id="fd14e-231"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="fd14e-232">Výchozí hodnota je `false`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-232">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="fd14e-233">Velký počet blokování synchronních vstupně-výstupních operací může vést ke vyčerpání fondu vláken, což aplikaci nereaguje.</span><span class="sxs-lookup"><span data-stu-id="fd14e-233">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="fd14e-234">Při použití knihovny, která nepodporuje asynchronní vstupně-výstupní operace, povolte pouze `AllowSynchronousIO`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-234">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="fd14e-235">Následující příklad povoluje synchronní vstupně-výstupní operace:</span><span class="sxs-lookup"><span data-stu-id="fd14e-235">The following example enables synchronous IO:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="fd14e-236">Informace o dalších možnostech a omezeních Kestrel najdete v tématech:</span><span class="sxs-lookup"><span data-stu-id="fd14e-236">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="fd14e-237">Konfigurace koncového bodu</span><span class="sxs-lookup"><span data-stu-id="fd14e-237">Endpoint configuration</span></span>

<span data-ttu-id="fd14e-238">Ve výchozím nastavení ASP.NET Core váže k:</span><span class="sxs-lookup"><span data-stu-id="fd14e-238">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="fd14e-239">`https://localhost:5001` (Pokud je k dispozici místní vývojový certifikát)</span><span class="sxs-lookup"><span data-stu-id="fd14e-239">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="fd14e-240">Zadejte adresy URL pomocí:</span><span class="sxs-lookup"><span data-stu-id="fd14e-240">Specify URLs using the:</span></span>

* <span data-ttu-id="fd14e-241">Proměnná prostředí `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="fd14e-241">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="fd14e-242">argument příkazového řádku `--urls`</span><span class="sxs-lookup"><span data-stu-id="fd14e-242">`--urls` command-line argument.</span></span>
* <span data-ttu-id="fd14e-243">`urls` konfigurační klíč hostitele.</span><span class="sxs-lookup"><span data-stu-id="fd14e-243">`urls` host configuration key.</span></span>
* <span data-ttu-id="fd14e-244">Metoda rozšíření `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-244">`UseUrls` extension method.</span></span>

<span data-ttu-id="fd14e-245">Hodnota zadaná pomocí těchto přístupů může být jeden nebo více koncových bodů HTTP a HTTPS (HTTPS, pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="fd14e-245">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="fd14e-246">Nakonfigurujte hodnotu jako seznam oddělený středníkem (například `"Urls": "http://localhost:8000; http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="fd14e-246">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="fd14e-247">Další informace o těchto přístupůch najdete v tématu [adresy URL serveru](xref:fundamentals/host/web-host#server-urls) a [potlačení konfigurace](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="fd14e-247">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="fd14e-248">Vytvoří se vývojový certifikát:</span><span class="sxs-lookup"><span data-stu-id="fd14e-248">A development certificate is created:</span></span>

* <span data-ttu-id="fd14e-249">Po instalaci [.NET Core SDK](/dotnet/core/sdk) .</span><span class="sxs-lookup"><span data-stu-id="fd14e-249">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="fd14e-250">[Nástroj pro vývoj a certifikáty](xref:aspnetcore-2.1#https) slouží k vytvoření certifikátu.</span><span class="sxs-lookup"><span data-stu-id="fd14e-250">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="fd14e-251">Některé prohlížeče vyžadují udělení explicitního oprávnění pro důvěřování místního vývojového certifikátu.</span><span class="sxs-lookup"><span data-stu-id="fd14e-251">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="fd14e-252">Šablony projektů konfigurují aplikace tak, aby běžely ve výchozím nastavení HTTPS a zahrnovaly [přesměrování https a podporu HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="fd14e-252">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="fd14e-253">Pokud chcete nakonfigurovat předpony a porty adres URL pro Kestrel, volejte <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> nebo <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> na <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="fd14e-253">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="fd14e-254">`UseUrls`, argument příkazového řádku `--urls`, konfigurační klíč hostitele `urls` a proměnná prostředí `ASPNETCORE_URLS` fungují, ale mají omezení zaznamenaná později v této části (pro konfiguraci koncového bodu HTTPS musí být k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="fd14e-254">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="fd14e-255">Konfigurace `KestrelServerOptions`:</span><span class="sxs-lookup"><span data-stu-id="fd14e-255">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="fd14e-256">ConfigureEndpointDefaults (akce @ no__t-0ListenOptions >)</span><span class="sxs-lookup"><span data-stu-id="fd14e-256">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="fd14e-257">Určuje konfiguraci `Action`, která se má spustit pro každý zadaný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="fd14e-257">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="fd14e-258">Volání `ConfigureEndpointDefaults` několikrát nahrazuje předchozí `Action` s určeným posledním `Action`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-258">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="fd14e-259">ConfigureHttpsDefaults (akce @ no__t-0HttpsConnectionAdapterOptions >)</span><span class="sxs-lookup"><span data-stu-id="fd14e-259">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="fd14e-260">Určuje konfiguraci `Action` pro spuštění každého koncového bodu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="fd14e-260">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="fd14e-261">Volání `ConfigureHttpsDefaults` několikrát nahrazuje předchozí `Action` s určeným posledním `Action`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-261">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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

### <a name="configureiconfiguration"></a><span data-ttu-id="fd14e-262">Konfigurace (IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="fd14e-262">Configure(IConfiguration)</span></span>

<span data-ttu-id="fd14e-263">Vytvoří zavaděč konfigurace pro nastavení Kestrel, který jako vstup převezme <xref:Microsoft.Extensions.Configuration.IConfiguration>.</span><span class="sxs-lookup"><span data-stu-id="fd14e-263">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="fd14e-264">Konfigurace musí být vymezená na konfigurační oddíl pro Kestrel.</span><span class="sxs-lookup"><span data-stu-id="fd14e-264">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="fd14e-265">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="fd14e-265">ListenOptions.UseHttps</span></span>

<span data-ttu-id="fd14e-266">Nakonfigurujte Kestrel tak, aby používal protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="fd14e-266">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="fd14e-267">rozšíření `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="fd14e-267">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="fd14e-268">`UseHttps` &ndash; nakonfigurujte Kestrel tak, aby používal HTTPS s výchozím certifikátem.</span><span class="sxs-lookup"><span data-stu-id="fd14e-268">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="fd14e-269">Vyvolá výjimku, pokud není nakonfigurován žádný výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="fd14e-269">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="fd14e-270">parametry `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="fd14e-270">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="fd14e-271">`filename` je cesta a název souboru certifikátu vzhledem k adresáři, který obsahuje soubory obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="fd14e-271">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="fd14e-272">`password` je heslo vyžadované pro přístup k datům certifikátu X. 509.</span><span class="sxs-lookup"><span data-stu-id="fd14e-272">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="fd14e-273">`configureOptions` je `Action` ke konfiguraci `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-273">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="fd14e-274">Vrátí `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-274">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="fd14e-275">`storeName` je úložiště certifikátů, ze kterého se má certifikát načíst.</span><span class="sxs-lookup"><span data-stu-id="fd14e-275">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="fd14e-276">`subject` je název subjektu certifikátu.</span><span class="sxs-lookup"><span data-stu-id="fd14e-276">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="fd14e-277">`allowInvalid` označuje, zda by měly být zváženy neplatné certifikáty, například certifikáty podepsané svým držitelem.</span><span class="sxs-lookup"><span data-stu-id="fd14e-277">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="fd14e-278">`location` je umístění úložiště, ze kterého se má načíst certifikát.</span><span class="sxs-lookup"><span data-stu-id="fd14e-278">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="fd14e-279">`serverCertificate` je certifikát X. 509.</span><span class="sxs-lookup"><span data-stu-id="fd14e-279">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="fd14e-280">V produkčním prostředí musí být HTTPS explicitně nakonfigurovaný.</span><span class="sxs-lookup"><span data-stu-id="fd14e-280">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="fd14e-281">Musí být zadán minimálně výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="fd14e-281">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="fd14e-282">Podporované konfigurace popsané dál:</span><span class="sxs-lookup"><span data-stu-id="fd14e-282">Supported configurations described next:</span></span>

* <span data-ttu-id="fd14e-283">Žádná konfigurace</span><span class="sxs-lookup"><span data-stu-id="fd14e-283">No configuration</span></span>
* <span data-ttu-id="fd14e-284">Nahradit výchozí certifikát z konfigurace</span><span class="sxs-lookup"><span data-stu-id="fd14e-284">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="fd14e-285">Změna výchozích hodnot v kódu</span><span class="sxs-lookup"><span data-stu-id="fd14e-285">Change the defaults in code</span></span>

<span data-ttu-id="fd14e-286">*Žádná konfigurace*</span><span class="sxs-lookup"><span data-stu-id="fd14e-286">*No configuration*</span></span>

<span data-ttu-id="fd14e-287">Kestrel naslouchá na `http://localhost:5000` a `https://localhost:5001` (Pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="fd14e-287">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="fd14e-288">*Nahradit výchozí certifikát z konfigurace*</span><span class="sxs-lookup"><span data-stu-id="fd14e-288">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="fd14e-289">`CreateDefaultBuilder` volá ve výchozím nastavení `Configure(context.Configuration.GetSection("Kestrel"))`, aby se načetla konfigurace Kestrel.</span><span class="sxs-lookup"><span data-stu-id="fd14e-289">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="fd14e-290">Pro Kestrel je k dispozici výchozí schéma konfigurace nastavení aplikace HTTPS.</span><span class="sxs-lookup"><span data-stu-id="fd14e-290">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="fd14e-291">Nakonfigurujte několik koncových bodů, včetně adres URL a certifikátů, které se mají použít, buď ze souboru na disku, nebo z úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="fd14e-291">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="fd14e-292">V následujícím příkladu *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="fd14e-292">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="fd14e-293">Nastavte **AllowInvalid** na `true`, pokud chcete povolit použití neplatných certifikátů (například certifikátů podepsaných svým držitelem).</span><span class="sxs-lookup"><span data-stu-id="fd14e-293">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="fd14e-294">Libovolný koncový bod HTTPS, který neurčuje certifikát (**HttpsDefaultCert** v následujícím příkladu), se vrátí k certifikátu definovanému v části **certifikáty** > **výchozí** nebo vývojový certifikát.</span><span class="sxs-lookup"><span data-stu-id="fd14e-294">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="fd14e-295">Alternativou k použití **cesty** a **hesla** pro libovolný uzel certifikátu je zadání certifikátu pomocí polí úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="fd14e-295">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="fd14e-296">Například **certifikáty** > **výchozí** certifikát lze zadat jako:</span><span class="sxs-lookup"><span data-stu-id="fd14e-296">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="fd14e-297">Poznámky ke schématu:</span><span class="sxs-lookup"><span data-stu-id="fd14e-297">Schema notes:</span></span>

* <span data-ttu-id="fd14e-298">V názvech koncových bodů se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="fd14e-298">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="fd14e-299">Například `HTTPS` a `Https` jsou platné.</span><span class="sxs-lookup"><span data-stu-id="fd14e-299">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="fd14e-300">U každého koncového bodu je vyžadován parametr `Url`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-300">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="fd14e-301">Formát tohoto parametru je stejný jako konfigurační parametr `Urls` nejvyšší úrovně s tím rozdílem, že je omezen na jedinou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="fd14e-301">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="fd14e-302">Tyto koncové body nahrazují hodnoty definované v konfiguraci `Urls` na nejvyšší úrovni místo jejich přidávání do těchto koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="fd14e-302">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="fd14e-303">Koncové body definované v kódu prostřednictvím `Listen` jsou kumulativní s koncovými body definovanými v konfiguračním oddílu.</span><span class="sxs-lookup"><span data-stu-id="fd14e-303">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="fd14e-304">Oddíl `Certificate` je nepovinný.</span><span class="sxs-lookup"><span data-stu-id="fd14e-304">The `Certificate` section is optional.</span></span> <span data-ttu-id="fd14e-305">Pokud se nezadá oddíl `Certificate`, použijí se výchozí hodnoty definované v dřívějších scénářích.</span><span class="sxs-lookup"><span data-stu-id="fd14e-305">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="fd14e-306">Pokud nejsou k dispozici žádné výchozí hodnoty, Server vyvolá výjimku a nemůže se spustit.</span><span class="sxs-lookup"><span data-stu-id="fd14e-306">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="fd14e-307">Oddíl `Certificate` podporuje jak**heslo** &ndash; **, tak**i **certifikát @no__t-** 5**Store** .</span><span class="sxs-lookup"><span data-stu-id="fd14e-307">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="fd14e-308">V takovém případě může být definován libovolný počet koncových bodů, pokud to nezpůsobí konflikty portů.</span><span class="sxs-lookup"><span data-stu-id="fd14e-308">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="fd14e-309">`options.Configure(context.Configuration.GetSection("{SECTION}"))` vrátí `KestrelConfigurationLoader` s metodou `.Endpoint(string name, listenOptions => { })`, která se dá použít k doplnění nastavení nakonfigurovaného koncového bodu:</span><span class="sxs-lookup"><span data-stu-id="fd14e-309">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="fd14e-310">`KestrelServerOptions.ConfigurationLoader` se dá získat přímo k pokračování v iteraci u stávajícího zavaděče, jako je třeba ten, který poskytuje <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="fd14e-310">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="fd14e-311">Konfigurační oddíl pro každý koncový bod je k dispozici na možnostech v metodě @no__t 0, aby bylo možné číst vlastní nastavení.</span><span class="sxs-lookup"><span data-stu-id="fd14e-311">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="fd14e-312">Více konfigurací může být načteno voláním `options.Configure(context.Configuration.GetSection("{SECTION}"))` znovu s jiným oddílem.</span><span class="sxs-lookup"><span data-stu-id="fd14e-312">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="fd14e-313">Je použita pouze poslední konfigurace, pokud není metoda `Load` explicitně volána pro předchozí instance.</span><span class="sxs-lookup"><span data-stu-id="fd14e-313">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="fd14e-314">Metapackage nevolá `Load`, takže jeho výchozí konfigurační oddíl může být nahrazen.</span><span class="sxs-lookup"><span data-stu-id="fd14e-314">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="fd14e-315">`KestrelConfigurationLoader` zrcadlí `Listen` rodinu rozhraní API od `KestrelServerOptions` jako přetížení `Endpoint`, takže koncové body kódu a konfigurace lze konfigurovat na stejném místě.</span><span class="sxs-lookup"><span data-stu-id="fd14e-315">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="fd14e-316">Tato přetížení nepoužívají názvy a využívají pouze výchozí nastavení z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="fd14e-316">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="fd14e-317">*Změna výchozích hodnot v kódu*</span><span class="sxs-lookup"><span data-stu-id="fd14e-317">*Change the defaults in code*</span></span>

<span data-ttu-id="fd14e-318">`ConfigureEndpointDefaults` a `ConfigureHttpsDefaults` lze použít ke změně výchozího nastavení pro `ListenOptions` a `HttpsConnectionAdapterOptions`, včetně přepsání výchozího certifikátu zadaného v předchozím scénáři.</span><span class="sxs-lookup"><span data-stu-id="fd14e-318">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="fd14e-319">`ConfigureEndpointDefaults` a `ConfigureHttpsDefaults` by měly být volány před konfigurací koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="fd14e-319">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="fd14e-320">*Podpora Kestrel pro SNI*</span><span class="sxs-lookup"><span data-stu-id="fd14e-320">*Kestrel support for SNI*</span></span>

<span data-ttu-id="fd14e-321">[Indikace názvu serveru (SNI)](https://tools.ietf.org/html/rfc6066#section-3) lze použít k hostování více domén na stejné IP adrese a portu.</span><span class="sxs-lookup"><span data-stu-id="fd14e-321">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="fd14e-322">Aby SNI fungovalo, klient pošle název hostitele pro zabezpečenou relaci serveru během metody handshake TLS, aby server mohl poskytovat správný certifikát.</span><span class="sxs-lookup"><span data-stu-id="fd14e-322">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="fd14e-323">Klient používá poskytnutý certifikát pro šifrovanou komunikaci se serverem během zabezpečené relace, která následuje za metodou handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="fd14e-323">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="fd14e-324">Kestrel podporuje SNI prostřednictvím zpětného volání `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-324">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="fd14e-325">Zpětné volání je vyvoláno jednou za připojení, které aplikaci umožní zkontrolovat název hostitele a vybrat příslušný certifikát.</span><span class="sxs-lookup"><span data-stu-id="fd14e-325">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="fd14e-326">Podpora SNI vyžaduje:</span><span class="sxs-lookup"><span data-stu-id="fd14e-326">SNI support requires:</span></span>

* <span data-ttu-id="fd14e-327">Spuštění v cílovém rozhraní `netcoreapp2.1` nebo novější.</span><span class="sxs-lookup"><span data-stu-id="fd14e-327">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="fd14e-328">V `net461` nebo novějším je zpětné volání vyvoláno, ale `name` je vždy `null`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-328">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="fd14e-329">@No__t-0 je také `null`, pokud klient neposkytne parametr název hostitele v handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="fd14e-329">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="fd14e-330">Všechny weby běží na stejné instanci Kestrel.</span><span class="sxs-lookup"><span data-stu-id="fd14e-330">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="fd14e-331">Kestrel nepodporuje sdílení IP adresy a portu napříč několika instancemi bez reverzního proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="fd14e-331">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="fd14e-332">Vytvoření vazby na soket TCP</span><span class="sxs-lookup"><span data-stu-id="fd14e-332">Bind to a TCP socket</span></span>

<span data-ttu-id="fd14e-333">Metoda <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> se váže k soketu TCP a obslužná rutina options umožňuje konfiguraci certifikátu X. 509:</span><span class="sxs-lookup"><span data-stu-id="fd14e-333">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="fd14e-334">Příklad konfiguruje HTTPS pro koncový bod s <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="fd14e-334">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="fd14e-335">Ke konfiguraci dalších nastavení Kestrel pro konkrétní koncové body použijte stejné rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="fd14e-335">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="fd14e-336">Vytvoření vazby na soket UNIX</span><span class="sxs-lookup"><span data-stu-id="fd14e-336">Bind to a Unix socket</span></span>

<span data-ttu-id="fd14e-337">Naslouchat na soketu se systémem UNIX pomocí <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> pro zlepšení výkonu pomocí Nginx, jak je znázorněno v tomto příkladu:</span><span class="sxs-lookup"><span data-stu-id="fd14e-337">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

### <a name="port-0"></a><span data-ttu-id="fd14e-338">Port 0</span><span class="sxs-lookup"><span data-stu-id="fd14e-338">Port 0</span></span>

<span data-ttu-id="fd14e-339">Když je zadané číslo portu `0`, dynamicky se váže k dostupnému portu.</span><span class="sxs-lookup"><span data-stu-id="fd14e-339">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="fd14e-340">Následující příklad ukazuje, jak určit, který port Kestrel je skutečně vázaný za běhu:</span><span class="sxs-lookup"><span data-stu-id="fd14e-340">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="fd14e-341">Když se aplikace spustí, výstup okna konzoly indikuje dynamický port, na který se aplikace dá získat:</span><span class="sxs-lookup"><span data-stu-id="fd14e-341">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="fd14e-342">Omezení</span><span class="sxs-lookup"><span data-stu-id="fd14e-342">Limitations</span></span>

<span data-ttu-id="fd14e-343">Konfigurace koncových bodů pomocí následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="fd14e-343">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="fd14e-344">argument příkazového řádku `--urls`</span><span class="sxs-lookup"><span data-stu-id="fd14e-344">`--urls` command-line argument</span></span>
* <span data-ttu-id="fd14e-345">konfigurační klíč hostitele `urls`</span><span class="sxs-lookup"><span data-stu-id="fd14e-345">`urls` host configuration key</span></span>
* <span data-ttu-id="fd14e-346">Proměnná prostředí `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="fd14e-346">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="fd14e-347">Tyto metody jsou užitečné, pokud chcete, aby kód pracoval na jiných serverech než Kestrel.</span><span class="sxs-lookup"><span data-stu-id="fd14e-347">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="fd14e-348">Mějte ale na paměti následující omezení:</span><span class="sxs-lookup"><span data-stu-id="fd14e-348">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="fd14e-349">Protokol HTTPS nelze s těmito přístupy použít, pokud není k dispozici výchozí certifikát v konfiguraci koncového bodu HTTPS (například pomocí konfigurace `KestrelServerOptions` nebo konfiguračního souboru, jak je uvedeno výše v tomto tématu).</span><span class="sxs-lookup"><span data-stu-id="fd14e-349">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="fd14e-350">Při současném použití `Listen` i `UseUrls` jsou koncové body `Listen` popsány v koncových bodech `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-350">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="fd14e-351">Konfigurace koncového bodu služby IIS</span><span class="sxs-lookup"><span data-stu-id="fd14e-351">IIS endpoint configuration</span></span>

<span data-ttu-id="fd14e-352">Při použití služby IIS jsou vazby adresy URL pro přepsání vazby služby IIS nastaveny buď pomocí `Listen`, nebo `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-352">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="fd14e-353">Další informace najdete v tématu [ASP.NET Core modulu](xref:host-and-deploy/aspnet-core-module) .</span><span class="sxs-lookup"><span data-stu-id="fd14e-353">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="fd14e-354">ListenOptions. Protocols</span><span class="sxs-lookup"><span data-stu-id="fd14e-354">ListenOptions.Protocols</span></span>

<span data-ttu-id="fd14e-355">Vlastnost `Protocols` vytváří protokoly HTTP (`HttpProtocols`) povolené na koncovém bodu připojení nebo pro server.</span><span class="sxs-lookup"><span data-stu-id="fd14e-355">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="fd14e-356">Přiřaďte hodnotu vlastnosti `Protocols` ze výčtu `HttpProtocols`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-356">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="fd14e-357">hodnota výčtu `HttpProtocols`</span><span class="sxs-lookup"><span data-stu-id="fd14e-357">`HttpProtocols` enum value</span></span> | <span data-ttu-id="fd14e-358">Povolený protokol připojení</span><span class="sxs-lookup"><span data-stu-id="fd14e-358">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="fd14e-359">Pouze HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="fd14e-359">HTTP/1.1 only.</span></span> <span data-ttu-id="fd14e-360">Dá se použít s TLS nebo bez něj.</span><span class="sxs-lookup"><span data-stu-id="fd14e-360">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="fd14e-361">Pouze HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="fd14e-361">HTTP/2 only.</span></span> <span data-ttu-id="fd14e-362">Dá se použít bez TLS jenom v případě, že klient podporuje [předchozí režim znalostní báze](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="fd14e-362">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="fd14e-363">HTTP/1.1 a HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="fd14e-363">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="fd14e-364">HTTP/2 vyžaduje, aby klient v rámci metody handshake protokolu TLS [(ALPN)](https://tools.ietf.org/html/rfc7301#section-3) vybral http/2. v opačném případě se připojení nastaví jako výchozí HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="fd14e-364">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="fd14e-365">Výchozí hodnota `ListenOptions.Protocols` pro libovolný koncový bod je `HttpProtocols.Http1AndHttp2`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-365">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="fd14e-366">Omezení TLS pro HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="fd14e-366">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="fd14e-367">TLS verze 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="fd14e-367">TLS version 1.2 or later</span></span>
* <span data-ttu-id="fd14e-368">Opětovné vyjednávání je zakázané.</span><span class="sxs-lookup"><span data-stu-id="fd14e-368">Renegotiation disabled</span></span>
* <span data-ttu-id="fd14e-369">Komprese vypnuta</span><span class="sxs-lookup"><span data-stu-id="fd14e-369">Compression disabled</span></span>
* <span data-ttu-id="fd14e-370">Minimální velikosti dočasného výměny klíčů:</span><span class="sxs-lookup"><span data-stu-id="fd14e-370">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="fd14e-371">Eliptická křivka Diffie-Hellman (ECDH) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bity minima</span><span class="sxs-lookup"><span data-stu-id="fd14e-371">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum</span></span>
  * <span data-ttu-id="fd14e-372">Omezené pole Diffie-Hellman (DHE) &lbrack; @ no__t-1 @ no__t-2 &ndash; 2048 bity minima</span><span class="sxs-lookup"><span data-stu-id="fd14e-372">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bits minimum</span></span>
* <span data-ttu-id="fd14e-373">Šifrovací sada není zakázaná.</span><span class="sxs-lookup"><span data-stu-id="fd14e-373">Cipher suite not blacklisted</span></span>

<span data-ttu-id="fd14e-374">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack; @ no__t-2 @ no__t-3 s eliptickou křivkou P-256, &lbrack; @ no__t-5 @ no__t-6 je ve výchozím nastavení podporováno.</span><span class="sxs-lookup"><span data-stu-id="fd14e-374">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="fd14e-375">Následující příklad povoluje připojení HTTP/1.1 a HTTP/2 na portu 8000.</span><span class="sxs-lookup"><span data-stu-id="fd14e-375">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="fd14e-376">Připojení TLS zabezpečuje pomocí zadaného certifikátu:</span><span class="sxs-lookup"><span data-stu-id="fd14e-376">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="fd14e-377">Pomocí middleware připojení můžete v případě potřeby filtrovat handshake na základě jednotlivých připojení pro konkrétní šifry.</span><span class="sxs-lookup"><span data-stu-id="fd14e-377">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="fd14e-378">Následující příklad vyvolá <xref:System.NotSupportedException> pro jakýkoli šifrovací algoritmus, který aplikace nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="fd14e-378">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="fd14e-379">Případně můžete definovat a porovnat [ITlsHandshakeFeature. CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) se seznamem přijatelných šifrovacích sad.</span><span class="sxs-lookup"><span data-stu-id="fd14e-379">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="fd14e-380">Pro šifrovací algoritmus [CipherAlgorithmType. null](xref:System.Security.Authentication.CipherAlgorithmType) se nepoužívá žádné šifrování.</span><span class="sxs-lookup"><span data-stu-id="fd14e-380">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

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

<span data-ttu-id="fd14e-381">Filtrování připojení lze také nakonfigurovat prostřednictvím lambda <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder>:</span><span class="sxs-lookup"><span data-stu-id="fd14e-381">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

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

<span data-ttu-id="fd14e-382">V systému Linux lze pomocí <xref:System.Net.Security.CipherSuitesPolicy> filtrovat handshake TLS na jednotlivých připojeních:</span><span class="sxs-lookup"><span data-stu-id="fd14e-382">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

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

<span data-ttu-id="fd14e-383">*Nastavit protokol z konfigurace*</span><span class="sxs-lookup"><span data-stu-id="fd14e-383">*Set the protocol from configuration*</span></span>

<span data-ttu-id="fd14e-384">`CreateDefaultBuilder` volá ve výchozím nastavení `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))`, aby se načetla konfigurace Kestrel.</span><span class="sxs-lookup"><span data-stu-id="fd14e-384">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="fd14e-385">Následující příklad *appSettings. JSON* vytvoří HTTP/1.1 jako výchozí protokol připojení pro všechny koncové body:</span><span class="sxs-lookup"><span data-stu-id="fd14e-385">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="fd14e-386">Následující příklad *appSettings. JSON* vytvoří protokol připojení HTTP/1.1 pro konkrétní koncový bod:</span><span class="sxs-lookup"><span data-stu-id="fd14e-386">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="fd14e-387">Protokoly zadané v hodnotách přepisu kódu nastavených podle konfigurace</span><span class="sxs-lookup"><span data-stu-id="fd14e-387">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="fd14e-388">Konfigurace přenosu</span><span class="sxs-lookup"><span data-stu-id="fd14e-388">Transport configuration</span></span>

<span data-ttu-id="fd14e-389">Pro projekty, které vyžadují použití Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span><span class="sxs-lookup"><span data-stu-id="fd14e-389">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span></span>

* <span data-ttu-id="fd14e-390">Přidejte do souboru projektu aplikace závislost pro balíček [Microsoft. AspNetCore. Server. Kestrel. Transport. Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) :</span><span class="sxs-lookup"><span data-stu-id="fd14e-390">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* <span data-ttu-id="fd14e-391">Na `IWebHostBuilder` volejte <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span><span class="sxs-lookup"><span data-stu-id="fd14e-391">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> on the `IWebHostBuilder`:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="fd14e-392">Předpony adresy URL</span><span class="sxs-lookup"><span data-stu-id="fd14e-392">URL prefixes</span></span>

<span data-ttu-id="fd14e-393">Při použití argumentu příkazového řádku `UseUrls`, `--urls`, klíče konfigurace hostitele `urls` nebo proměnné prostředí `ASPNETCORE_URLS` můžou být předpony adresy URL v některém z následujících formátů.</span><span class="sxs-lookup"><span data-stu-id="fd14e-393">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="fd14e-394">Platné jsou pouze předpony adresy URL protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="fd14e-394">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="fd14e-395">Kestrel nepodporuje HTTPS při konfiguraci vazeb URL pomocí `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-395">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="fd14e-396">Adresa IPv4 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="fd14e-396">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="fd14e-397">`0.0.0.0` je zvláštní případ, který se váže ke všem adresám IPv4.</span><span class="sxs-lookup"><span data-stu-id="fd14e-397">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="fd14e-398">Adresa IPv6 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="fd14e-398">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="fd14e-399">`[::]` je ekvivalent protokolu IPv4 `0.0.0.0` protokolu IPv6.</span><span class="sxs-lookup"><span data-stu-id="fd14e-399">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="fd14e-400">Název hostitele s číslem portu</span><span class="sxs-lookup"><span data-stu-id="fd14e-400">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="fd14e-401">Názvy hostitelů `*` a `+` nejsou speciální.</span><span class="sxs-lookup"><span data-stu-id="fd14e-401">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="fd14e-402">Cokoli se nerozpoznalo jako platná IP adresa nebo `localhost` váže ke všem IP adresám IPv4 a IPv6.</span><span class="sxs-lookup"><span data-stu-id="fd14e-402">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="fd14e-403">Pokud chcete navazovat různé názvy hostitelů na různé ASP.NET Core aplikace na stejném portu, použijte [http. sys](xref:fundamentals/servers/httpsys) nebo reverzní proxy server, třeba IIS, Nginx nebo Apache.</span><span class="sxs-lookup"><span data-stu-id="fd14e-403">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="fd14e-404">Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="fd14e-404">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="fd14e-405">Hostitel @no__t – 0 název s číslem portu nebo IP adresou zpětné smyčky s číslem portu</span><span class="sxs-lookup"><span data-stu-id="fd14e-405">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="fd14e-406">Pokud je zadána hodnota `localhost`, pokusí se Kestrel vytvořit propojení s rozhraními zpětné smyčky IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="fd14e-406">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="fd14e-407">Pokud je požadovaný port používán jinou službou na rozhraní zpětné smyčky, spuštění Kestrel se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="fd14e-407">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="fd14e-408">Pokud je rozhraní zpětné smyčky v žádném jiném případě nedostupné (většinou kvůli tomu, že protokol IPv6 není podporovaný), Kestrel zaznamená upozornění.</span><span class="sxs-lookup"><span data-stu-id="fd14e-408">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="fd14e-409">Filtrování hostitele</span><span class="sxs-lookup"><span data-stu-id="fd14e-409">Host filtering</span></span>

<span data-ttu-id="fd14e-410">I když Kestrel podporuje konfiguraci na základě prefixů, jako je například `http://example.com:5000`, Kestrel převážně ignoruje název hostitele.</span><span class="sxs-lookup"><span data-stu-id="fd14e-410">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="fd14e-411">Hostitel @no__t – 0 je zvláštní případ, který se používá pro vazbu na adresy zpětné smyčky.</span><span class="sxs-lookup"><span data-stu-id="fd14e-411">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="fd14e-412">Každý hostitel, který je jiný než explicitní IP adresa, se váže na všechny veřejné IP adresy.</span><span class="sxs-lookup"><span data-stu-id="fd14e-412">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="fd14e-413">hlavičky `Host` nejsou ověřeny.</span><span class="sxs-lookup"><span data-stu-id="fd14e-413">`Host` headers aren't validated.</span></span>

<span data-ttu-id="fd14e-414">Jako alternativní řešení použijte middleware pro filtrování hostitele.</span><span class="sxs-lookup"><span data-stu-id="fd14e-414">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="fd14e-415">Middleware pro filtrování hostitele poskytuje balíček [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , který je implicitně k dispozici pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="fd14e-415">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="fd14e-416">Middleware se přidá pomocí <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, která volá <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="fd14e-416">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="fd14e-417">Middleware pro filtrování hostitele je ve výchozím nastavení zakázáno.</span><span class="sxs-lookup"><span data-stu-id="fd14e-417">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="fd14e-418">Chcete-li povolit middleware, definujte @no__t klíč-0 v souboru *appSettings. json*/*appSettings. \<EnvironmentName >. JSON*.</span><span class="sxs-lookup"><span data-stu-id="fd14e-418">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="fd14e-419">Hodnota je seznam názvů hostitelů oddělených středníkem bez čísel portů:</span><span class="sxs-lookup"><span data-stu-id="fd14e-419">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="fd14e-420">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="fd14e-420">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="fd14e-421">[Middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer) má taky možnost <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="fd14e-421">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="fd14e-422">Middleware předávaných hlaviček a middleware pro filtrování hostitele mají podobné funkce pro různé scénáře.</span><span class="sxs-lookup"><span data-stu-id="fd14e-422">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="fd14e-423">Nastavení `AllowedHosts` s předaným middlewarem hlaviček je vhodné, když se hlavička `Host` nezachová při předávání požadavků pomocí reverzního proxy server nebo nástroje pro vyrovnávání zatížení.</span><span class="sxs-lookup"><span data-stu-id="fd14e-423">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="fd14e-424">Nastavení `AllowedHosts` pomocí middlewaru pro filtrování hostitele je vhodné, pokud se Kestrel používá jako veřejný hraniční Server nebo když je hlavička `Host` přímo předána.</span><span class="sxs-lookup"><span data-stu-id="fd14e-424">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="fd14e-425">Další informace o middlewaru předávaných hlaviček najdete v tématu <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="fd14e-425">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="fd14e-426">Kestrel je [webový server pro různé platformy pro ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="fd14e-426">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="fd14e-427">Kestrel je webový server, který je ve výchozím nastavení součástí ASP.NET Core šablon projektů.</span><span class="sxs-lookup"><span data-stu-id="fd14e-427">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="fd14e-428">Kestrel podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="fd14e-428">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="fd14e-429">HTTPS</span><span class="sxs-lookup"><span data-stu-id="fd14e-429">HTTPS</span></span>
* <span data-ttu-id="fd14e-430">Neprůhledný upgrade používaný k povolení [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="fd14e-430">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="fd14e-431">Technologie UNIX Sockets s vysokým výkonem za Nginx</span><span class="sxs-lookup"><span data-stu-id="fd14e-431">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="fd14e-432">HTTP/2 (s výjimkou macOS @ no__t-0)</span><span class="sxs-lookup"><span data-stu-id="fd14e-432">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="fd14e-433">@no__t – 0HTTP/2 bude v budoucí verzi podporován v macOS.</span><span class="sxs-lookup"><span data-stu-id="fd14e-433">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="fd14e-434">Kestrel se podporuje na všech platformách a verzích, které podporuje .NET Core.</span><span class="sxs-lookup"><span data-stu-id="fd14e-434">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="fd14e-435">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fd14e-435">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="fd14e-436">Podpora HTTP/2</span><span class="sxs-lookup"><span data-stu-id="fd14e-436">HTTP/2 support</span></span>

<span data-ttu-id="fd14e-437">[Http/2](https://httpwg.org/specs/rfc7540.html) je k dispozici pro ASP.NET Core aplikace, pokud jsou splněné následující základní požadavky:</span><span class="sxs-lookup"><span data-stu-id="fd14e-437">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="fd14e-438">Operační systém @ no__t-0</span><span class="sxs-lookup"><span data-stu-id="fd14e-438">Operating system&dagger;</span></span>
  * <span data-ttu-id="fd14e-439">Windows Server 2016/Windows 10 nebo novější @ no__t-0</span><span class="sxs-lookup"><span data-stu-id="fd14e-439">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="fd14e-440">Linux s OpenSSL 1.0.2 nebo novějším (například Ubuntu 16,04 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="fd14e-440">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="fd14e-441">Cílová architektura: .NET Core 2,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="fd14e-441">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="fd14e-442">Připojení [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="fd14e-442">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="fd14e-443">Připojení TLS 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="fd14e-443">TLS 1.2 or later connection</span></span>

<span data-ttu-id="fd14e-444">@no__t – 0HTTP/2 bude v budoucí verzi podporován v macOS.</span><span class="sxs-lookup"><span data-stu-id="fd14e-444">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="fd14e-445">@no__t – 0Kestrel má omezená podpora HTTP/2 na Windows Serveru 2012 R2 a Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="fd14e-445">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="fd14e-446">Podpora je omezená, protože seznam podporovaných šifrovacích sad TLS, které jsou k dispozici v těchto operačních systémech, je omezený.</span><span class="sxs-lookup"><span data-stu-id="fd14e-446">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="fd14e-447">Pro zabezpečení připojení TLS může být vyžadován certifikát vygenerovaný pomocí algoritmu ECDSA (s připojením typu eliptická křivka).</span><span class="sxs-lookup"><span data-stu-id="fd14e-447">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="fd14e-448">Pokud je navázáno připojení HTTP/2, sestavy [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-448">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="fd14e-449">HTTP/2 je ve výchozím nastavení zakázané.</span><span class="sxs-lookup"><span data-stu-id="fd14e-449">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="fd14e-450">Další informace o konfiguraci najdete v částech [Možnosti Kestrel](#kestrel-options) a [ListenOptions. Protocols](#listenoptionsprotocols) .</span><span class="sxs-lookup"><span data-stu-id="fd14e-450">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="fd14e-451">Kdy použít Kestrel s reverzním proxy serverem</span><span class="sxs-lookup"><span data-stu-id="fd14e-451">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="fd14e-452">Kestrel může používat sám sebe nebo s *reverzními proxy server*, jako je [Internetová informační služba (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="fd14e-452">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="fd14e-453">Reverzní proxy server přijímá požadavky HTTP ze sítě a přesměruje je na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="fd14e-453">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="fd14e-454">Kestrel použitý jako hraniční webový server (internetový):</span><span class="sxs-lookup"><span data-stu-id="fd14e-454">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel komunikuje přímo s internetem bez zpětného proxy server](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="fd14e-456">Kestrel se používá v konfiguraci reverzního proxy serveru:</span><span class="sxs-lookup"><span data-stu-id="fd14e-456">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel nepřímo komunikuje s internetem prostřednictvím reverzní proxy server, jako je IIS, Nginx nebo Apache.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="fd14e-458">Buď konfigurace, s zpětným proxy server nebo bez něj, je podporovaná konfigurace hostování.</span><span class="sxs-lookup"><span data-stu-id="fd14e-458">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="fd14e-459">Kestrel používané jako hraniční Server bez reverzních proxy server nepodporuje sdílení stejné IP adresy a portu mezi více procesy.</span><span class="sxs-lookup"><span data-stu-id="fd14e-459">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="fd14e-460">Když je Kestrel nakonfigurovaný k naslouchání na portu, Kestrel zpracovává veškerý provoz tohoto portu bez ohledu na hlavičky `Host`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-460">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="fd14e-461">Reverzní proxy server, který může sdílet porty, má schopnost přesměrovat požadavky na Kestrel na jedinečné IP adresy a porty.</span><span class="sxs-lookup"><span data-stu-id="fd14e-461">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="fd14e-462">I v případě, že se zpětný proxy server nepožaduje, může být použití reverzní proxy server vhodnou volbou.</span><span class="sxs-lookup"><span data-stu-id="fd14e-462">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="fd14e-463">Reverzní proxy:</span><span class="sxs-lookup"><span data-stu-id="fd14e-463">A reverse proxy:</span></span>

* <span data-ttu-id="fd14e-464">Může omezit vystavenou veřejnou plochu v aplikacích, které hostuje.</span><span class="sxs-lookup"><span data-stu-id="fd14e-464">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="fd14e-465">Poskytněte další vrstvu konfigurace a obrany.</span><span class="sxs-lookup"><span data-stu-id="fd14e-465">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="fd14e-466">Může se lépe integrovat se stávající infrastrukturou.</span><span class="sxs-lookup"><span data-stu-id="fd14e-466">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="fd14e-467">Zjednodušte vyrovnávání zatížení a konfiguraci zabezpečené komunikace (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="fd14e-467">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="fd14e-468">Pouze reverzní proxy server vyžaduje certifikát X. 509 a tento server může komunikovat se servery aplikace v interní síti pomocí prostého protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="fd14e-468">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="fd14e-469">Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="fd14e-469">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="fd14e-470">Použití Kestrel v aplikacích ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fd14e-470">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="fd14e-471">Balíček [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) je součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="fd14e-471">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="fd14e-472">ASP.NET Core šablony projektu ve výchozím nastavení používají Kestrel.</span><span class="sxs-lookup"><span data-stu-id="fd14e-472">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="fd14e-473">V *program.cs*kód šablony volá <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, která volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> na pozadí.</span><span class="sxs-lookup"><span data-stu-id="fd14e-473">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="fd14e-474">Další informace o @no__t – 0 a vytváření hostitele najdete v části *Nastavení hostitele* v <xref:fundamentals/host/web-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="fd14e-474">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="fd14e-475">Pokud chcete po volání `CreateDefaultBuilder` zadat další konfiguraci, použijte `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="fd14e-475">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="fd14e-476">Pokud aplikace nevolá `CreateDefaultBuilder` pro nastavení hostitele, **před** voláním `ConfigureKestrel` volejte <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span><span class="sxs-lookup"><span data-stu-id="fd14e-476">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="fd14e-477">Kestrel možnosti</span><span class="sxs-lookup"><span data-stu-id="fd14e-477">Kestrel options</span></span>

<span data-ttu-id="fd14e-478">Webový server Kestrel má možnosti konfigurace omezení, které jsou zvláště užitečné v internetových nasazeních.</span><span class="sxs-lookup"><span data-stu-id="fd14e-478">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="fd14e-479">Nastavte omezení pro vlastnost <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> třídy <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="fd14e-479">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="fd14e-480">Vlastnost `Limits` obsahuje instanci třídy <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="fd14e-480">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="fd14e-481">V následujících příkladech se používá obor názvů <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:</span><span class="sxs-lookup"><span data-stu-id="fd14e-481">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="fd14e-482">Možnosti Kestrel, které jsou konfigurovány C# v kódu v následujících příkladech, lze také nastavit pomocí [poskytovatele konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="fd14e-482">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="fd14e-483">Poskytovatel konfigurace souboru může například načíst konfiguraci Kestrel z objektu *appSettings. JSON* nebo *appSettings. { Soubor Environment}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="fd14e-483">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="fd14e-484">Použijte **jeden** z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="fd14e-484">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="fd14e-485">Konfigurace Kestrel v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="fd14e-485">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="fd14e-486">Vloží instanci `IConfiguration` do třídy `Startup`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-486">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="fd14e-487">Následující příklad předpokládá, že vložená konfigurace je přiřazena vlastnosti `Configuration`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-487">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="fd14e-488">V `Startup.ConfigureServices` načtěte oddíl `Kestrel` konfigurace do konfigurace Kestrel.</span><span class="sxs-lookup"><span data-stu-id="fd14e-488">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration.</span></span>

     ```csharp
     // using Microsoft.Extensions.Configuration

     public void ConfigureServices(IServiceCollection services)
     {
         services.Configure<KestrelServerOptions>(
             Configuration.GetSection("Kestrel"));
     }
     ```

* <span data-ttu-id="fd14e-489">Nakonfigurovat Kestrel při sestavování hostitele:</span><span class="sxs-lookup"><span data-stu-id="fd14e-489">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="fd14e-490">V *program.cs*do konfigurace Kestrel načtěte oddíl `Kestrel` konfigurace:</span><span class="sxs-lookup"><span data-stu-id="fd14e-490">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="fd14e-491">Obě předchozí přístupy fungují u libovolného [poskytovatele konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="fd14e-491">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="fd14e-492">Časový limit Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="fd14e-492">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="fd14e-493">Získá nebo nastaví [časový limit zachování](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="fd14e-493">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="fd14e-494">Výchozí hodnota je 2 minuty.</span><span class="sxs-lookup"><span data-stu-id="fd14e-494">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="fd14e-495">Maximální počet připojení klientů</span><span class="sxs-lookup"><span data-stu-id="fd14e-495">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="fd14e-496">Maximální počet souběžných otevřených připojení TCP lze pro celou aplikaci nastavit pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="fd14e-496">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="fd14e-497">Existuje zvláštní omezení pro připojení, která byla upgradována z protokolu HTTP nebo HTTPS na jiný protokol (například na žádost WebSockets).</span><span class="sxs-lookup"><span data-stu-id="fd14e-497">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="fd14e-498">Po upgradu připojení se nepočítá s limitem @no__t 0.</span><span class="sxs-lookup"><span data-stu-id="fd14e-498">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="fd14e-499">Ve výchozím nastavení je maximální počet připojení neomezené (null).</span><span class="sxs-lookup"><span data-stu-id="fd14e-499">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="fd14e-500">Maximální velikost textu požadavku</span><span class="sxs-lookup"><span data-stu-id="fd14e-500">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="fd14e-501">Výchozí maximální velikost textu požadavku je 30 000 000 bajtů, což je přibližně 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="fd14e-501">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="fd14e-502">Doporučený postup pro přepsání limitu v aplikaci ASP.NET Core MVC je použití atributu <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> u metody akce:</span><span class="sxs-lookup"><span data-stu-id="fd14e-502">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="fd14e-503">Tady je příklad, který ukazuje, jak nakonfigurovat omezení pro aplikaci na každém požadavku:</span><span class="sxs-lookup"><span data-stu-id="fd14e-503">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="fd14e-504">Přepsat nastavení u konkrétního požadavku v middleware:</span><span class="sxs-lookup"><span data-stu-id="fd14e-504">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="fd14e-505">Výjimka je vyvolána, pokud aplikace po zahájení aplikace nakonfiguruje limit na žádost.</span><span class="sxs-lookup"><span data-stu-id="fd14e-505">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="fd14e-506">Existuje vlastnost `IsReadOnly`, která určuje, zda je vlastnost `MaxRequestBodySize` ve stavu jen pro čtení, což znamená, že je příliš pozdě pro konfiguraci limitu.</span><span class="sxs-lookup"><span data-stu-id="fd14e-506">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="fd14e-507">Když je aplikace spouštěna [mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za [ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module), je omezení velikosti textu požadavku Kestrel zakázané, protože služba IIS tento limit již nastavuje.</span><span class="sxs-lookup"><span data-stu-id="fd14e-507">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="fd14e-508">Minimální rychlost těla zprávy požadavku</span><span class="sxs-lookup"><span data-stu-id="fd14e-508">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="fd14e-509">Kestrel kontroluje každou sekundu, pokud data přicházejí za zadanou rychlost v bajtech za sekundu.</span><span class="sxs-lookup"><span data-stu-id="fd14e-509">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="fd14e-510">Pokud frekvence klesne pod minimální hodnotu, vypršel časový limit připojení. Doba odkladu je doba, kterou Kestrel umožňuje klientovi zvýšit rychlost odesílání až na minimum; frekvence není během této doby kontrolována.</span><span class="sxs-lookup"><span data-stu-id="fd14e-510">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="fd14e-511">Doba odkladu pomáhá vyhnout se vyřazení připojení, která při počátečním posílání dat zpomalují rychlost, kvůli pomalému spuštění protokolu TCP.</span><span class="sxs-lookup"><span data-stu-id="fd14e-511">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="fd14e-512">Výchozí minimální sazba je 240 bajtů za sekundu s obdobím 5 sekund odkladu.</span><span class="sxs-lookup"><span data-stu-id="fd14e-512">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="fd14e-513">Pro odpověď platí také minimální sazba.</span><span class="sxs-lookup"><span data-stu-id="fd14e-513">A minimum rate also applies to the response.</span></span> <span data-ttu-id="fd14e-514">Kód pro nastavení limitu požadavků a limit odpovědi je stejný s výjimkou `RequestBody` nebo `Response` v názvech vlastností a rozhraní.</span><span class="sxs-lookup"><span data-stu-id="fd14e-514">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="fd14e-515">Tady je příklad, který ukazuje, jak nakonfigurovat minimální datové tarify v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="fd14e-515">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="fd14e-516">Přepsat minimální limity přenosové rychlosti na žádost v middlewaru:</span><span class="sxs-lookup"><span data-stu-id="fd14e-516">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="fd14e-517">V předchozí ukázce není k dispozici žádná míra – funkce `HttpContext.Features` pro požadavky HTTP/2, protože Změna omezení přenosové rychlosti u jednotlivých požadavků není pro protokol HTTP/2 podporovaná kvůli podpoře protokolu pro multiplexování požadavků.</span><span class="sxs-lookup"><span data-stu-id="fd14e-517">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="fd14e-518">Omezení přenosové rychlosti pro všechny servery nakonfigurovaná přes `KestrelServerOptions.Limits` se stále vztahují na připojení HTTP/1. x i HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="fd14e-518">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="fd14e-519">Časový limit hlaviček žádostí</span><span class="sxs-lookup"><span data-stu-id="fd14e-519">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="fd14e-520">Získá nebo nastaví maximální dobu, po kterou server stráví příjem hlaviček požadavků.</span><span class="sxs-lookup"><span data-stu-id="fd14e-520">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="fd14e-521">Výchozí hodnota je 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="fd14e-521">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="fd14e-522">Maximální počet proudů na připojení</span><span class="sxs-lookup"><span data-stu-id="fd14e-522">Maximum streams per connection</span></span>

<span data-ttu-id="fd14e-523">`Http2.MaxStreamsPerConnection` omezuje počet souběžných proudů požadavků na připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="fd14e-523">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="fd14e-524">Nadbytečné proudy jsou odmítnuty.</span><span class="sxs-lookup"><span data-stu-id="fd14e-524">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="fd14e-525">Výchozí hodnota je 100.</span><span class="sxs-lookup"><span data-stu-id="fd14e-525">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="fd14e-526">Velikost tabulky hlaviček</span><span class="sxs-lookup"><span data-stu-id="fd14e-526">Header table size</span></span>

<span data-ttu-id="fd14e-527">Dekodér HPACK dekomprimuje hlavičky HTTP pro připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="fd14e-527">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="fd14e-528">`Http2.HeaderTableSize` omezuje velikost kompresní tabulky hlaviček, kterou používá dekodér HPACK.</span><span class="sxs-lookup"><span data-stu-id="fd14e-528">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="fd14e-529">Hodnota je uvedena v oktetech a musí být větší než nula (0).</span><span class="sxs-lookup"><span data-stu-id="fd14e-529">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="fd14e-530">Výchozí hodnota je 4096.</span><span class="sxs-lookup"><span data-stu-id="fd14e-530">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="fd14e-531">Maximální velikost rámce</span><span class="sxs-lookup"><span data-stu-id="fd14e-531">Maximum frame size</span></span>

<span data-ttu-id="fd14e-532">`Http2.MaxFrameSize` označuje maximální velikost datové části rámce připojení HTTP/2, která se má přijmout.</span><span class="sxs-lookup"><span data-stu-id="fd14e-532">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="fd14e-533">Hodnota je uvedena v oktetech a musí být v rozmezí 2 ^ 14 (16 384) až 2 ^ 24-1 (16 777 215).</span><span class="sxs-lookup"><span data-stu-id="fd14e-533">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="fd14e-534">Výchozí hodnota je 2 ^ 14 (16 384).</span><span class="sxs-lookup"><span data-stu-id="fd14e-534">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="fd14e-535">Maximální velikost hlavičky požadavku</span><span class="sxs-lookup"><span data-stu-id="fd14e-535">Maximum request header size</span></span>

<span data-ttu-id="fd14e-536">`Http2.MaxRequestHeaderFieldSize` označuje maximální povolenou velikost v oktetech hodnot hlaviček požadavku.</span><span class="sxs-lookup"><span data-stu-id="fd14e-536">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="fd14e-537">Toto omezení platí pro názvy i hodnoty společně v komprimovaných a nekomprimovaných reprezentujcích.</span><span class="sxs-lookup"><span data-stu-id="fd14e-537">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="fd14e-538">Hodnota musí být větší než nula (0).</span><span class="sxs-lookup"><span data-stu-id="fd14e-538">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="fd14e-539">Výchozí hodnota je 8 192.</span><span class="sxs-lookup"><span data-stu-id="fd14e-539">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="fd14e-540">Velikost okna počátečního připojení</span><span class="sxs-lookup"><span data-stu-id="fd14e-540">Initial connection window size</span></span>

<span data-ttu-id="fd14e-541">`Http2.InitialConnectionWindowSize` označuje maximální velikost textu žádosti v bajtech. vyrovnávací paměti serveru se v jednom okamžiku agreguje napříč všemi požadavky (datovými proudy) na připojení.</span><span class="sxs-lookup"><span data-stu-id="fd14e-541">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="fd14e-542">Žádosti jsou také omezené pomocí `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-542">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="fd14e-543">Hodnota musí být větší než nebo rovna 65 535 a menší než 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="fd14e-543">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="fd14e-544">Výchozí hodnota je 128 KB (131 072).</span><span class="sxs-lookup"><span data-stu-id="fd14e-544">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="fd14e-545">Počáteční velikost okna datového proudu</span><span class="sxs-lookup"><span data-stu-id="fd14e-545">Initial stream window size</span></span>

<span data-ttu-id="fd14e-546">`Http2.InitialStreamWindowSize` označuje maximální velikost textu požadavku v bajtech na jednu žádost (Stream) na jednom místě.</span><span class="sxs-lookup"><span data-stu-id="fd14e-546">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="fd14e-547">Žádosti jsou také omezené pomocí `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-547">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="fd14e-548">Hodnota musí být větší než nebo rovna 65 535 a menší než 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="fd14e-548">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="fd14e-549">Výchozí hodnota je 96 KB (98 304).</span><span class="sxs-lookup"><span data-stu-id="fd14e-549">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="fd14e-550">Synchronní v/v</span><span class="sxs-lookup"><span data-stu-id="fd14e-550">Synchronous IO</span></span>

<span data-ttu-id="fd14e-551"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> řídí, zda je pro požadavek a odpověď povolena synchronní v/v.</span><span class="sxs-lookup"><span data-stu-id="fd14e-551"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="fd14e-552">Výchozí hodnota je `true`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-552">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="fd14e-553">Velký počet blokování synchronních vstupně-výstupních operací může vést ke vyčerpání fondu vláken, což aplikaci nereaguje.</span><span class="sxs-lookup"><span data-stu-id="fd14e-553">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="fd14e-554">Při použití knihovny, která nepodporuje asynchronní vstupně-výstupní operace, povolte pouze `AllowSynchronousIO`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-554">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="fd14e-555">Následující příklad povoluje synchronní vstupně-výstupní operace:</span><span class="sxs-lookup"><span data-stu-id="fd14e-555">The following example enables synchronous IO:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="fd14e-556">Informace o dalších možnostech a omezeních Kestrel najdete v tématech:</span><span class="sxs-lookup"><span data-stu-id="fd14e-556">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="fd14e-557">Konfigurace koncového bodu</span><span class="sxs-lookup"><span data-stu-id="fd14e-557">Endpoint configuration</span></span>

<span data-ttu-id="fd14e-558">Ve výchozím nastavení ASP.NET Core váže k:</span><span class="sxs-lookup"><span data-stu-id="fd14e-558">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="fd14e-559">`https://localhost:5001` (Pokud je k dispozici místní vývojový certifikát)</span><span class="sxs-lookup"><span data-stu-id="fd14e-559">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="fd14e-560">Zadejte adresy URL pomocí:</span><span class="sxs-lookup"><span data-stu-id="fd14e-560">Specify URLs using the:</span></span>

* <span data-ttu-id="fd14e-561">Proměnná prostředí `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="fd14e-561">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="fd14e-562">argument příkazového řádku `--urls`</span><span class="sxs-lookup"><span data-stu-id="fd14e-562">`--urls` command-line argument.</span></span>
* <span data-ttu-id="fd14e-563">`urls` konfigurační klíč hostitele.</span><span class="sxs-lookup"><span data-stu-id="fd14e-563">`urls` host configuration key.</span></span>
* <span data-ttu-id="fd14e-564">Metoda rozšíření `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-564">`UseUrls` extension method.</span></span>

<span data-ttu-id="fd14e-565">Hodnota zadaná pomocí těchto přístupů může být jeden nebo více koncových bodů HTTP a HTTPS (HTTPS, pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="fd14e-565">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="fd14e-566">Nakonfigurujte hodnotu jako seznam oddělený středníkem (například `"Urls": "http://localhost:8000; http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="fd14e-566">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="fd14e-567">Další informace o těchto přístupůch najdete v tématu [adresy URL serveru](xref:fundamentals/host/web-host#server-urls) a [potlačení konfigurace](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="fd14e-567">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="fd14e-568">Vytvoří se vývojový certifikát:</span><span class="sxs-lookup"><span data-stu-id="fd14e-568">A development certificate is created:</span></span>

* <span data-ttu-id="fd14e-569">Po instalaci [.NET Core SDK](/dotnet/core/sdk) .</span><span class="sxs-lookup"><span data-stu-id="fd14e-569">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="fd14e-570">[Nástroj pro vývoj a certifikáty](xref:aspnetcore-2.1#https) slouží k vytvoření certifikátu.</span><span class="sxs-lookup"><span data-stu-id="fd14e-570">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="fd14e-571">Některé prohlížeče vyžadují udělení explicitního oprávnění pro důvěřování místního vývojového certifikátu.</span><span class="sxs-lookup"><span data-stu-id="fd14e-571">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="fd14e-572">Šablony projektů konfigurují aplikace tak, aby běžely ve výchozím nastavení HTTPS a zahrnovaly [přesměrování https a podporu HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="fd14e-572">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="fd14e-573">Pokud chcete nakonfigurovat předpony a porty adres URL pro Kestrel, volejte <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> nebo <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> na <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="fd14e-573">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="fd14e-574">`UseUrls`, argument příkazového řádku `--urls`, konfigurační klíč hostitele `urls` a proměnná prostředí `ASPNETCORE_URLS` fungují, ale mají omezení zaznamenaná později v této části (pro konfiguraci koncového bodu HTTPS musí být k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="fd14e-574">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="fd14e-575">Konfigurace `KestrelServerOptions`:</span><span class="sxs-lookup"><span data-stu-id="fd14e-575">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="fd14e-576">ConfigureEndpointDefaults (akce @ no__t-0ListenOptions >)</span><span class="sxs-lookup"><span data-stu-id="fd14e-576">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="fd14e-577">Určuje konfiguraci `Action`, která se má spustit pro každý zadaný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="fd14e-577">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="fd14e-578">Volání `ConfigureEndpointDefaults` několikrát nahrazuje předchozí `Action` s určeným posledním `Action`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-578">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="fd14e-579">ConfigureHttpsDefaults (akce @ no__t-0HttpsConnectionAdapterOptions >)</span><span class="sxs-lookup"><span data-stu-id="fd14e-579">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="fd14e-580">Určuje konfiguraci `Action` pro spuštění každého koncového bodu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="fd14e-580">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="fd14e-581">Volání `ConfigureHttpsDefaults` několikrát nahrazuje předchozí `Action` s určeným posledním `Action`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-581">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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

### <a name="configureiconfiguration"></a><span data-ttu-id="fd14e-582">Konfigurace (IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="fd14e-582">Configure(IConfiguration)</span></span>

<span data-ttu-id="fd14e-583">Vytvoří zavaděč konfigurace pro nastavení Kestrel, který jako vstup převezme <xref:Microsoft.Extensions.Configuration.IConfiguration>.</span><span class="sxs-lookup"><span data-stu-id="fd14e-583">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="fd14e-584">Konfigurace musí být vymezená na konfigurační oddíl pro Kestrel.</span><span class="sxs-lookup"><span data-stu-id="fd14e-584">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="fd14e-585">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="fd14e-585">ListenOptions.UseHttps</span></span>

<span data-ttu-id="fd14e-586">Nakonfigurujte Kestrel tak, aby používal protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="fd14e-586">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="fd14e-587">rozšíření `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="fd14e-587">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="fd14e-588">`UseHttps` &ndash; nakonfigurujte Kestrel tak, aby používal HTTPS s výchozím certifikátem.</span><span class="sxs-lookup"><span data-stu-id="fd14e-588">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="fd14e-589">Vyvolá výjimku, pokud není nakonfigurován žádný výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="fd14e-589">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="fd14e-590">parametry `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="fd14e-590">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="fd14e-591">`filename` je cesta a název souboru certifikátu vzhledem k adresáři, který obsahuje soubory obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="fd14e-591">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="fd14e-592">`password` je heslo vyžadované pro přístup k datům certifikátu X. 509.</span><span class="sxs-lookup"><span data-stu-id="fd14e-592">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="fd14e-593">`configureOptions` je `Action` ke konfiguraci `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-593">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="fd14e-594">Vrátí `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-594">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="fd14e-595">`storeName` je úložiště certifikátů, ze kterého se má certifikát načíst.</span><span class="sxs-lookup"><span data-stu-id="fd14e-595">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="fd14e-596">`subject` je název subjektu certifikátu.</span><span class="sxs-lookup"><span data-stu-id="fd14e-596">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="fd14e-597">`allowInvalid` označuje, zda by měly být zváženy neplatné certifikáty, například certifikáty podepsané svým držitelem.</span><span class="sxs-lookup"><span data-stu-id="fd14e-597">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="fd14e-598">`location` je umístění úložiště, ze kterého se má načíst certifikát.</span><span class="sxs-lookup"><span data-stu-id="fd14e-598">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="fd14e-599">`serverCertificate` je certifikát X. 509.</span><span class="sxs-lookup"><span data-stu-id="fd14e-599">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="fd14e-600">V produkčním prostředí musí být HTTPS explicitně nakonfigurovaný.</span><span class="sxs-lookup"><span data-stu-id="fd14e-600">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="fd14e-601">Musí být zadán minimálně výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="fd14e-601">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="fd14e-602">Podporované konfigurace popsané dál:</span><span class="sxs-lookup"><span data-stu-id="fd14e-602">Supported configurations described next:</span></span>

* <span data-ttu-id="fd14e-603">Žádná konfigurace</span><span class="sxs-lookup"><span data-stu-id="fd14e-603">No configuration</span></span>
* <span data-ttu-id="fd14e-604">Nahradit výchozí certifikát z konfigurace</span><span class="sxs-lookup"><span data-stu-id="fd14e-604">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="fd14e-605">Změna výchozích hodnot v kódu</span><span class="sxs-lookup"><span data-stu-id="fd14e-605">Change the defaults in code</span></span>

<span data-ttu-id="fd14e-606">*Žádná konfigurace*</span><span class="sxs-lookup"><span data-stu-id="fd14e-606">*No configuration*</span></span>

<span data-ttu-id="fd14e-607">Kestrel naslouchá na `http://localhost:5000` a `https://localhost:5001` (Pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="fd14e-607">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="fd14e-608">*Nahradit výchozí certifikát z konfigurace*</span><span class="sxs-lookup"><span data-stu-id="fd14e-608">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="fd14e-609">`CreateDefaultBuilder` volá ve výchozím nastavení `Configure(context.Configuration.GetSection("Kestrel"))`, aby se načetla konfigurace Kestrel.</span><span class="sxs-lookup"><span data-stu-id="fd14e-609">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="fd14e-610">Pro Kestrel je k dispozici výchozí schéma konfigurace nastavení aplikace HTTPS.</span><span class="sxs-lookup"><span data-stu-id="fd14e-610">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="fd14e-611">Nakonfigurujte několik koncových bodů, včetně adres URL a certifikátů, které se mají použít, buď ze souboru na disku, nebo z úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="fd14e-611">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="fd14e-612">V následujícím příkladu *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="fd14e-612">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="fd14e-613">Nastavte **AllowInvalid** na `true`, pokud chcete povolit použití neplatných certifikátů (například certifikátů podepsaných svým držitelem).</span><span class="sxs-lookup"><span data-stu-id="fd14e-613">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="fd14e-614">Libovolný koncový bod HTTPS, který neurčuje certifikát (**HttpsDefaultCert** v následujícím příkladu), se vrátí k certifikátu definovanému v části **certifikáty** > **výchozí** nebo vývojový certifikát.</span><span class="sxs-lookup"><span data-stu-id="fd14e-614">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="fd14e-615">Alternativou k použití **cesty** a **hesla** pro libovolný uzel certifikátu je zadání certifikátu pomocí polí úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="fd14e-615">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="fd14e-616">Například **certifikáty** > **výchozí** certifikát lze zadat jako:</span><span class="sxs-lookup"><span data-stu-id="fd14e-616">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="fd14e-617">Poznámky ke schématu:</span><span class="sxs-lookup"><span data-stu-id="fd14e-617">Schema notes:</span></span>

* <span data-ttu-id="fd14e-618">V názvech koncových bodů se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="fd14e-618">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="fd14e-619">Například `HTTPS` a `Https` jsou platné.</span><span class="sxs-lookup"><span data-stu-id="fd14e-619">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="fd14e-620">U každého koncového bodu je vyžadován parametr `Url`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-620">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="fd14e-621">Formát tohoto parametru je stejný jako konfigurační parametr `Urls` nejvyšší úrovně s tím rozdílem, že je omezen na jedinou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="fd14e-621">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="fd14e-622">Tyto koncové body nahrazují hodnoty definované v konfiguraci `Urls` na nejvyšší úrovni místo jejich přidávání do těchto koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="fd14e-622">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="fd14e-623">Koncové body definované v kódu prostřednictvím `Listen` jsou kumulativní s koncovými body definovanými v konfiguračním oddílu.</span><span class="sxs-lookup"><span data-stu-id="fd14e-623">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="fd14e-624">Oddíl `Certificate` je nepovinný.</span><span class="sxs-lookup"><span data-stu-id="fd14e-624">The `Certificate` section is optional.</span></span> <span data-ttu-id="fd14e-625">Pokud se nezadá oddíl `Certificate`, použijí se výchozí hodnoty definované v dřívějších scénářích.</span><span class="sxs-lookup"><span data-stu-id="fd14e-625">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="fd14e-626">Pokud nejsou k dispozici žádné výchozí hodnoty, Server vyvolá výjimku a nemůže se spustit.</span><span class="sxs-lookup"><span data-stu-id="fd14e-626">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="fd14e-627">Oddíl `Certificate` podporuje jak**heslo** &ndash; **, tak**i **certifikát @no__t-** 5**Store** .</span><span class="sxs-lookup"><span data-stu-id="fd14e-627">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="fd14e-628">V takovém případě může být definován libovolný počet koncových bodů, pokud to nezpůsobí konflikty portů.</span><span class="sxs-lookup"><span data-stu-id="fd14e-628">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="fd14e-629">`options.Configure(context.Configuration.GetSection("{SECTION}"))` vrátí `KestrelConfigurationLoader` s metodou `.Endpoint(string name, listenOptions => { })`, která se dá použít k doplnění nastavení nakonfigurovaného koncového bodu:</span><span class="sxs-lookup"><span data-stu-id="fd14e-629">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="fd14e-630">`KestrelServerOptions.ConfigurationLoader` se dá získat přímo k pokračování v iteraci u stávajícího zavaděče, jako je třeba ten, který poskytuje <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="fd14e-630">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="fd14e-631">Konfigurační oddíl pro každý koncový bod je k dispozici na možnostech v metodě @no__t 0, aby bylo možné číst vlastní nastavení.</span><span class="sxs-lookup"><span data-stu-id="fd14e-631">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="fd14e-632">Více konfigurací může být načteno voláním `options.Configure(context.Configuration.GetSection("{SECTION}"))` znovu s jiným oddílem.</span><span class="sxs-lookup"><span data-stu-id="fd14e-632">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="fd14e-633">Je použita pouze poslední konfigurace, pokud není metoda `Load` explicitně volána pro předchozí instance.</span><span class="sxs-lookup"><span data-stu-id="fd14e-633">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="fd14e-634">Metapackage nevolá `Load`, takže jeho výchozí konfigurační oddíl může být nahrazen.</span><span class="sxs-lookup"><span data-stu-id="fd14e-634">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="fd14e-635">`KestrelConfigurationLoader` zrcadlí `Listen` rodinu rozhraní API od `KestrelServerOptions` jako přetížení `Endpoint`, takže koncové body kódu a konfigurace lze konfigurovat na stejném místě.</span><span class="sxs-lookup"><span data-stu-id="fd14e-635">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="fd14e-636">Tato přetížení nepoužívají názvy a využívají pouze výchozí nastavení z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="fd14e-636">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="fd14e-637">*Změna výchozích hodnot v kódu*</span><span class="sxs-lookup"><span data-stu-id="fd14e-637">*Change the defaults in code*</span></span>

<span data-ttu-id="fd14e-638">`ConfigureEndpointDefaults` a `ConfigureHttpsDefaults` lze použít ke změně výchozího nastavení pro `ListenOptions` a `HttpsConnectionAdapterOptions`, včetně přepsání výchozího certifikátu zadaného v předchozím scénáři.</span><span class="sxs-lookup"><span data-stu-id="fd14e-638">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="fd14e-639">`ConfigureEndpointDefaults` a `ConfigureHttpsDefaults` by měly být volány před konfigurací koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="fd14e-639">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="fd14e-640">*Podpora Kestrel pro SNI*</span><span class="sxs-lookup"><span data-stu-id="fd14e-640">*Kestrel support for SNI*</span></span>

<span data-ttu-id="fd14e-641">[Indikace názvu serveru (SNI)](https://tools.ietf.org/html/rfc6066#section-3) lze použít k hostování více domén na stejné IP adrese a portu.</span><span class="sxs-lookup"><span data-stu-id="fd14e-641">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="fd14e-642">Aby SNI fungovalo, klient pošle název hostitele pro zabezpečenou relaci serveru během metody handshake TLS, aby server mohl poskytovat správný certifikát.</span><span class="sxs-lookup"><span data-stu-id="fd14e-642">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="fd14e-643">Klient používá poskytnutý certifikát pro šifrovanou komunikaci se serverem během zabezpečené relace, která následuje za metodou handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="fd14e-643">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="fd14e-644">Kestrel podporuje SNI prostřednictvím zpětného volání `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-644">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="fd14e-645">Zpětné volání je vyvoláno jednou za připojení, které aplikaci umožní zkontrolovat název hostitele a vybrat příslušný certifikát.</span><span class="sxs-lookup"><span data-stu-id="fd14e-645">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="fd14e-646">Podpora SNI vyžaduje:</span><span class="sxs-lookup"><span data-stu-id="fd14e-646">SNI support requires:</span></span>

* <span data-ttu-id="fd14e-647">Spuštění v cílovém rozhraní `netcoreapp2.1` nebo novější.</span><span class="sxs-lookup"><span data-stu-id="fd14e-647">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="fd14e-648">V `net461` nebo novějším je zpětné volání vyvoláno, ale `name` je vždy `null`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-648">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="fd14e-649">@No__t-0 je také `null`, pokud klient neposkytne parametr název hostitele v handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="fd14e-649">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="fd14e-650">Všechny weby běží na stejné instanci Kestrel.</span><span class="sxs-lookup"><span data-stu-id="fd14e-650">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="fd14e-651">Kestrel nepodporuje sdílení IP adresy a portu napříč několika instancemi bez reverzního proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="fd14e-651">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="fd14e-652">Vytvoření vazby na soket TCP</span><span class="sxs-lookup"><span data-stu-id="fd14e-652">Bind to a TCP socket</span></span>

<span data-ttu-id="fd14e-653">Metoda <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> se váže k soketu TCP a obslužná rutina options umožňuje konfiguraci certifikátu X. 509:</span><span class="sxs-lookup"><span data-stu-id="fd14e-653">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="fd14e-654">Příklad konfiguruje HTTPS pro koncový bod s <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="fd14e-654">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="fd14e-655">Ke konfiguraci dalších nastavení Kestrel pro konkrétní koncové body použijte stejné rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="fd14e-655">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="fd14e-656">Vytvoření vazby na soket UNIX</span><span class="sxs-lookup"><span data-stu-id="fd14e-656">Bind to a Unix socket</span></span>

<span data-ttu-id="fd14e-657">Naslouchat na soketu se systémem UNIX pomocí <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> pro zlepšení výkonu pomocí Nginx, jak je znázorněno v tomto příkladu:</span><span class="sxs-lookup"><span data-stu-id="fd14e-657">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

### <a name="port-0"></a><span data-ttu-id="fd14e-658">Port 0</span><span class="sxs-lookup"><span data-stu-id="fd14e-658">Port 0</span></span>

<span data-ttu-id="fd14e-659">Když je zadané číslo portu `0`, dynamicky se váže k dostupnému portu.</span><span class="sxs-lookup"><span data-stu-id="fd14e-659">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="fd14e-660">Následující příklad ukazuje, jak určit, který port Kestrel je skutečně vázaný za běhu:</span><span class="sxs-lookup"><span data-stu-id="fd14e-660">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="fd14e-661">Když se aplikace spustí, výstup okna konzoly indikuje dynamický port, na který se aplikace dá získat:</span><span class="sxs-lookup"><span data-stu-id="fd14e-661">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="fd14e-662">Omezení</span><span class="sxs-lookup"><span data-stu-id="fd14e-662">Limitations</span></span>

<span data-ttu-id="fd14e-663">Konfigurace koncových bodů pomocí následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="fd14e-663">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="fd14e-664">argument příkazového řádku `--urls`</span><span class="sxs-lookup"><span data-stu-id="fd14e-664">`--urls` command-line argument</span></span>
* <span data-ttu-id="fd14e-665">konfigurační klíč hostitele `urls`</span><span class="sxs-lookup"><span data-stu-id="fd14e-665">`urls` host configuration key</span></span>
* <span data-ttu-id="fd14e-666">Proměnná prostředí `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="fd14e-666">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="fd14e-667">Tyto metody jsou užitečné, pokud chcete, aby kód pracoval na jiných serverech než Kestrel.</span><span class="sxs-lookup"><span data-stu-id="fd14e-667">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="fd14e-668">Mějte ale na paměti následující omezení:</span><span class="sxs-lookup"><span data-stu-id="fd14e-668">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="fd14e-669">Protokol HTTPS nelze s těmito přístupy použít, pokud není k dispozici výchozí certifikát v konfiguraci koncového bodu HTTPS (například pomocí konfigurace `KestrelServerOptions` nebo konfiguračního souboru, jak je uvedeno výše v tomto tématu).</span><span class="sxs-lookup"><span data-stu-id="fd14e-669">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="fd14e-670">Při současném použití `Listen` i `UseUrls` jsou koncové body `Listen` popsány v koncových bodech `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-670">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="fd14e-671">Konfigurace koncového bodu služby IIS</span><span class="sxs-lookup"><span data-stu-id="fd14e-671">IIS endpoint configuration</span></span>

<span data-ttu-id="fd14e-672">Při použití služby IIS jsou vazby adresy URL pro přepsání vazby služby IIS nastaveny buď pomocí `Listen`, nebo `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-672">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="fd14e-673">Další informace najdete v tématu [ASP.NET Core modulu](xref:host-and-deploy/aspnet-core-module) .</span><span class="sxs-lookup"><span data-stu-id="fd14e-673">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="fd14e-674">ListenOptions. Protocols</span><span class="sxs-lookup"><span data-stu-id="fd14e-674">ListenOptions.Protocols</span></span>

<span data-ttu-id="fd14e-675">Vlastnost `Protocols` vytváří protokoly HTTP (`HttpProtocols`) povolené na koncovém bodu připojení nebo pro server.</span><span class="sxs-lookup"><span data-stu-id="fd14e-675">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="fd14e-676">Přiřaďte hodnotu vlastnosti `Protocols` ze výčtu `HttpProtocols`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-676">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="fd14e-677">hodnota výčtu `HttpProtocols`</span><span class="sxs-lookup"><span data-stu-id="fd14e-677">`HttpProtocols` enum value</span></span> | <span data-ttu-id="fd14e-678">Povolený protokol připojení</span><span class="sxs-lookup"><span data-stu-id="fd14e-678">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="fd14e-679">Pouze HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="fd14e-679">HTTP/1.1 only.</span></span> <span data-ttu-id="fd14e-680">Dá se použít s TLS nebo bez něj.</span><span class="sxs-lookup"><span data-stu-id="fd14e-680">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="fd14e-681">Pouze HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="fd14e-681">HTTP/2 only.</span></span> <span data-ttu-id="fd14e-682">Dá se použít bez TLS jenom v případě, že klient podporuje [předchozí režim znalostní báze](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="fd14e-682">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="fd14e-683">HTTP/1.1 a HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="fd14e-683">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="fd14e-684">HTTP/2 vyžaduje připojení TLS a [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) ; v opačném případě se připojení nastaví jako výchozí HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="fd14e-684">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="fd14e-685">Výchozí protokol HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="fd14e-685">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="fd14e-686">Omezení TLS pro HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="fd14e-686">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="fd14e-687">TLS verze 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="fd14e-687">TLS version 1.2 or later</span></span>
* <span data-ttu-id="fd14e-688">Opětovné vyjednávání je zakázané.</span><span class="sxs-lookup"><span data-stu-id="fd14e-688">Renegotiation disabled</span></span>
* <span data-ttu-id="fd14e-689">Komprese vypnuta</span><span class="sxs-lookup"><span data-stu-id="fd14e-689">Compression disabled</span></span>
* <span data-ttu-id="fd14e-690">Minimální velikosti dočasného výměny klíčů:</span><span class="sxs-lookup"><span data-stu-id="fd14e-690">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="fd14e-691">Eliptická křivka Diffie-Hellman (ECDH) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bity minima</span><span class="sxs-lookup"><span data-stu-id="fd14e-691">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum</span></span>
  * <span data-ttu-id="fd14e-692">Omezené pole Diffie-Hellman (DHE) &lbrack; @ no__t-1 @ no__t-2 &ndash; 2048 bity minima</span><span class="sxs-lookup"><span data-stu-id="fd14e-692">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bits minimum</span></span>
* <span data-ttu-id="fd14e-693">Šifrovací sada není zakázaná.</span><span class="sxs-lookup"><span data-stu-id="fd14e-693">Cipher suite not blacklisted</span></span>

<span data-ttu-id="fd14e-694">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack; @ no__t-2 @ no__t-3 s eliptickou křivkou P-256, &lbrack; @ no__t-5 @ no__t-6 je ve výchozím nastavení podporováno.</span><span class="sxs-lookup"><span data-stu-id="fd14e-694">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="fd14e-695">Následující příklad povoluje připojení HTTP/1.1 a HTTP/2 na portu 8000.</span><span class="sxs-lookup"><span data-stu-id="fd14e-695">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="fd14e-696">Připojení TLS zabezpečuje pomocí zadaného certifikátu:</span><span class="sxs-lookup"><span data-stu-id="fd14e-696">Connections are secured by TLS with a supplied certificate:</span></span>

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

<span data-ttu-id="fd14e-697">Volitelně můžete vytvořit implementaci `IConnectionAdapter` pro filtrování ověřování TLS na základě jednotlivých připojení pro konkrétní šifry:</span><span class="sxs-lookup"><span data-stu-id="fd14e-697">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

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

<span data-ttu-id="fd14e-698">*Nastavit protokol z konfigurace*</span><span class="sxs-lookup"><span data-stu-id="fd14e-698">*Set the protocol from configuration*</span></span>

<span data-ttu-id="fd14e-699"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> volá ve výchozím nastavení `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))`, aby se načetla konfigurace Kestrel.</span><span class="sxs-lookup"><span data-stu-id="fd14e-699"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="fd14e-700">V následujícím příkladu *appSettings. JSON* se pro všechny koncové body Kestrel vytvoří výchozí protokol připojení (http/1.1 a HTTP/2):</span><span class="sxs-lookup"><span data-stu-id="fd14e-700">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="fd14e-701">Následující příklad konfiguračního souboru vytvoří protokol připojení pro konkrétní koncový bod:</span><span class="sxs-lookup"><span data-stu-id="fd14e-701">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="fd14e-702">Protokoly zadané v hodnotách přepisu kódu nastavených podle konfigurace</span><span class="sxs-lookup"><span data-stu-id="fd14e-702">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="fd14e-703">Konfigurace přenosu</span><span class="sxs-lookup"><span data-stu-id="fd14e-703">Transport configuration</span></span>

<span data-ttu-id="fd14e-704">S vydáním ASP.NET Core 2,1 není výchozí přenos Kestrel založen na Libuv, ale na základě spravovaných soketů.</span><span class="sxs-lookup"><span data-stu-id="fd14e-704">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="fd14e-705">Jedná se o zásadní změnu pro aplikace ASP.NET Core 2,0 upgradované na 2,1, která volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> a závisí na jednom z následujících balíčků:</span><span class="sxs-lookup"><span data-stu-id="fd14e-705">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="fd14e-706">[Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (přímý odkaz na balíček)</span><span class="sxs-lookup"><span data-stu-id="fd14e-706">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="fd14e-707">Microsoft. AspNetCore. app</span><span class="sxs-lookup"><span data-stu-id="fd14e-707">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="fd14e-708">Pro projekty, které vyžadují použití Libuv:</span><span class="sxs-lookup"><span data-stu-id="fd14e-708">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="fd14e-709">Přidejte do souboru projektu aplikace závislost pro balíček [Microsoft. AspNetCore. Server. Kestrel. Transport. Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) :</span><span class="sxs-lookup"><span data-stu-id="fd14e-709">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="fd14e-710">Volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span><span class="sxs-lookup"><span data-stu-id="fd14e-710">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="fd14e-711">Předpony adresy URL</span><span class="sxs-lookup"><span data-stu-id="fd14e-711">URL prefixes</span></span>

<span data-ttu-id="fd14e-712">Při použití argumentu příkazového řádku `UseUrls`, `--urls`, klíče konfigurace hostitele `urls` nebo proměnné prostředí `ASPNETCORE_URLS` můžou být předpony adresy URL v některém z následujících formátů.</span><span class="sxs-lookup"><span data-stu-id="fd14e-712">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="fd14e-713">Platné jsou pouze předpony adresy URL protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="fd14e-713">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="fd14e-714">Kestrel nepodporuje HTTPS při konfiguraci vazeb URL pomocí `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-714">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="fd14e-715">Adresa IPv4 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="fd14e-715">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="fd14e-716">`0.0.0.0` je zvláštní případ, který se váže ke všem adresám IPv4.</span><span class="sxs-lookup"><span data-stu-id="fd14e-716">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="fd14e-717">Adresa IPv6 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="fd14e-717">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="fd14e-718">`[::]` je ekvivalent protokolu IPv4 `0.0.0.0` protokolu IPv6.</span><span class="sxs-lookup"><span data-stu-id="fd14e-718">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="fd14e-719">Název hostitele s číslem portu</span><span class="sxs-lookup"><span data-stu-id="fd14e-719">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="fd14e-720">Názvy hostitelů `*` a `+` nejsou speciální.</span><span class="sxs-lookup"><span data-stu-id="fd14e-720">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="fd14e-721">Cokoli se nerozpoznalo jako platná IP adresa nebo `localhost` váže ke všem IP adresám IPv4 a IPv6.</span><span class="sxs-lookup"><span data-stu-id="fd14e-721">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="fd14e-722">Pokud chcete navazovat různé názvy hostitelů na různé ASP.NET Core aplikace na stejném portu, použijte [http. sys](xref:fundamentals/servers/httpsys) nebo reverzní proxy server, třeba IIS, Nginx nebo Apache.</span><span class="sxs-lookup"><span data-stu-id="fd14e-722">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="fd14e-723">Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="fd14e-723">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="fd14e-724">Hostitel @no__t – 0 název s číslem portu nebo IP adresou zpětné smyčky s číslem portu</span><span class="sxs-lookup"><span data-stu-id="fd14e-724">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="fd14e-725">Pokud je zadána hodnota `localhost`, pokusí se Kestrel vytvořit propojení s rozhraními zpětné smyčky IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="fd14e-725">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="fd14e-726">Pokud je požadovaný port používán jinou službou na rozhraní zpětné smyčky, spuštění Kestrel se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="fd14e-726">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="fd14e-727">Pokud je rozhraní zpětné smyčky v žádném jiném případě nedostupné (většinou kvůli tomu, že protokol IPv6 není podporovaný), Kestrel zaznamená upozornění.</span><span class="sxs-lookup"><span data-stu-id="fd14e-727">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="fd14e-728">Filtrování hostitele</span><span class="sxs-lookup"><span data-stu-id="fd14e-728">Host filtering</span></span>

<span data-ttu-id="fd14e-729">I když Kestrel podporuje konfiguraci na základě prefixů, jako je například `http://example.com:5000`, Kestrel převážně ignoruje název hostitele.</span><span class="sxs-lookup"><span data-stu-id="fd14e-729">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="fd14e-730">Hostitel @no__t – 0 je zvláštní případ, který se používá pro vazbu na adresy zpětné smyčky.</span><span class="sxs-lookup"><span data-stu-id="fd14e-730">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="fd14e-731">Každý hostitel, který je jiný než explicitní IP adresa, se váže na všechny veřejné IP adresy.</span><span class="sxs-lookup"><span data-stu-id="fd14e-731">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="fd14e-732">hlavičky `Host` nejsou ověřeny.</span><span class="sxs-lookup"><span data-stu-id="fd14e-732">`Host` headers aren't validated.</span></span>

<span data-ttu-id="fd14e-733">Jako alternativní řešení použijte middleware pro filtrování hostitele.</span><span class="sxs-lookup"><span data-stu-id="fd14e-733">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="fd14e-734">Middleware pro filtrování hostitele poskytuje balíček [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , který je součástí [Microsoft. AspNetCore. App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 nebo 2,2).</span><span class="sxs-lookup"><span data-stu-id="fd14e-734">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="fd14e-735">Middleware se přidá pomocí <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, která volá <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="fd14e-735">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="fd14e-736">Middleware pro filtrování hostitele je ve výchozím nastavení zakázáno.</span><span class="sxs-lookup"><span data-stu-id="fd14e-736">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="fd14e-737">Chcete-li povolit middleware, definujte @no__t klíč-0 v souboru *appSettings. json*/*appSettings. \<EnvironmentName >. JSON*.</span><span class="sxs-lookup"><span data-stu-id="fd14e-737">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="fd14e-738">Hodnota je seznam názvů hostitelů oddělených středníkem bez čísel portů:</span><span class="sxs-lookup"><span data-stu-id="fd14e-738">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="fd14e-739">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="fd14e-739">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="fd14e-740">[Middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer) má taky možnost <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="fd14e-740">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="fd14e-741">Middleware předávaných hlaviček a middleware pro filtrování hostitele mají podobné funkce pro různé scénáře.</span><span class="sxs-lookup"><span data-stu-id="fd14e-741">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="fd14e-742">Nastavení `AllowedHosts` s předaným middlewarem hlaviček je vhodné, když se hlavička `Host` nezachová při předávání požadavků pomocí reverzního proxy server nebo nástroje pro vyrovnávání zatížení.</span><span class="sxs-lookup"><span data-stu-id="fd14e-742">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="fd14e-743">Nastavení `AllowedHosts` pomocí middlewaru pro filtrování hostitele je vhodné, pokud se Kestrel používá jako veřejný hraniční Server nebo když je hlavička `Host` přímo předána.</span><span class="sxs-lookup"><span data-stu-id="fd14e-743">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="fd14e-744">Další informace o middlewaru předávaných hlaviček najdete v tématu <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="fd14e-744">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="fd14e-745">Kestrel je [webový server pro různé platformy pro ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="fd14e-745">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="fd14e-746">Kestrel je webový server, který je ve výchozím nastavení součástí ASP.NET Core šablon projektů.</span><span class="sxs-lookup"><span data-stu-id="fd14e-746">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="fd14e-747">Kestrel podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="fd14e-747">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="fd14e-748">HTTPS</span><span class="sxs-lookup"><span data-stu-id="fd14e-748">HTTPS</span></span>
* <span data-ttu-id="fd14e-749">Neprůhledný upgrade používaný k povolení [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="fd14e-749">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="fd14e-750">Technologie UNIX Sockets s vysokým výkonem za Nginx</span><span class="sxs-lookup"><span data-stu-id="fd14e-750">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="fd14e-751">Kestrel se podporuje na všech platformách a verzích, které podporuje .NET Core.</span><span class="sxs-lookup"><span data-stu-id="fd14e-751">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="fd14e-752">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="fd14e-752">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="fd14e-753">Kdy použít Kestrel s reverzním proxy serverem</span><span class="sxs-lookup"><span data-stu-id="fd14e-753">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="fd14e-754">Kestrel může používat sám sebe nebo s *reverzními proxy server*, jako je [Internetová informační služba (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="fd14e-754">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="fd14e-755">Reverzní proxy server přijímá požadavky HTTP ze sítě a přesměruje je na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="fd14e-755">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="fd14e-756">Kestrel použitý jako hraniční webový server (internetový):</span><span class="sxs-lookup"><span data-stu-id="fd14e-756">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel komunikuje přímo s internetem bez zpětného proxy server](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="fd14e-758">Kestrel se používá v konfiguraci reverzního proxy serveru:</span><span class="sxs-lookup"><span data-stu-id="fd14e-758">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel nepřímo komunikuje s internetem prostřednictvím reverzní proxy server, jako je IIS, Nginx nebo Apache.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="fd14e-760">Buď konfigurace, s zpětným proxy server nebo bez něj, je podporovaná konfigurace hostování.</span><span class="sxs-lookup"><span data-stu-id="fd14e-760">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="fd14e-761">Kestrel používané jako hraniční Server bez reverzních proxy server nepodporuje sdílení stejné IP adresy a portu mezi více procesy.</span><span class="sxs-lookup"><span data-stu-id="fd14e-761">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="fd14e-762">Když je Kestrel nakonfigurovaný k naslouchání na portu, Kestrel zpracovává veškerý provoz tohoto portu bez ohledu na hlavičky `Host`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-762">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="fd14e-763">Reverzní proxy server, který může sdílet porty, má schopnost přesměrovat požadavky na Kestrel na jedinečné IP adresy a porty.</span><span class="sxs-lookup"><span data-stu-id="fd14e-763">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="fd14e-764">I v případě, že se zpětný proxy server nepožaduje, může být použití reverzní proxy server vhodnou volbou.</span><span class="sxs-lookup"><span data-stu-id="fd14e-764">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="fd14e-765">Reverzní proxy:</span><span class="sxs-lookup"><span data-stu-id="fd14e-765">A reverse proxy:</span></span>

* <span data-ttu-id="fd14e-766">Může omezit vystavenou veřejnou plochu v aplikacích, které hostuje.</span><span class="sxs-lookup"><span data-stu-id="fd14e-766">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="fd14e-767">Poskytněte další vrstvu konfigurace a obrany.</span><span class="sxs-lookup"><span data-stu-id="fd14e-767">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="fd14e-768">Může se lépe integrovat se stávající infrastrukturou.</span><span class="sxs-lookup"><span data-stu-id="fd14e-768">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="fd14e-769">Zjednodušte vyrovnávání zatížení a konfiguraci zabezpečené komunikace (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="fd14e-769">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="fd14e-770">Pouze reverzní proxy server vyžaduje certifikát X. 509 a tento server může komunikovat se servery aplikace v interní síti pomocí prostého protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="fd14e-770">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="fd14e-771">Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="fd14e-771">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="fd14e-772">Použití Kestrel v aplikacích ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="fd14e-772">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="fd14e-773">Balíček [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) je součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="fd14e-773">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="fd14e-774">ASP.NET Core šablony projektu ve výchozím nastavení používají Kestrel.</span><span class="sxs-lookup"><span data-stu-id="fd14e-774">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="fd14e-775">V *program.cs*kód šablony volá <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, která volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> na pozadí.</span><span class="sxs-lookup"><span data-stu-id="fd14e-775">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="fd14e-776">Pro poskytnutí další konfigurace po volání `CreateDefaultBuilder` volejte <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span><span class="sxs-lookup"><span data-stu-id="fd14e-776">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="fd14e-777">Další informace o @no__t – 0 a vytváření hostitele najdete v části *Nastavení hostitele* v <xref:fundamentals/host/web-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="fd14e-777">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="fd14e-778">Kestrel možnosti</span><span class="sxs-lookup"><span data-stu-id="fd14e-778">Kestrel options</span></span>

<span data-ttu-id="fd14e-779">Webový server Kestrel má možnosti konfigurace omezení, které jsou zvláště užitečné v internetových nasazeních.</span><span class="sxs-lookup"><span data-stu-id="fd14e-779">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="fd14e-780">Nastavte omezení pro vlastnost <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> třídy <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="fd14e-780">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="fd14e-781">Vlastnost `Limits` obsahuje instanci třídy <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="fd14e-781">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="fd14e-782">V následujících příkladech se používá obor názvů <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:</span><span class="sxs-lookup"><span data-stu-id="fd14e-782">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="fd14e-783">Možnosti Kestrel, které jsou konfigurovány C# v kódu v následujících příkladech, lze také nastavit pomocí [poskytovatele konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="fd14e-783">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="fd14e-784">Poskytovatel konfigurace souboru může například načíst konfiguraci Kestrel z objektu *appSettings. JSON* nebo *appSettings. { Soubor Environment}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="fd14e-784">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="fd14e-785">Použijte **jeden** z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="fd14e-785">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="fd14e-786">Konfigurace Kestrel v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="fd14e-786">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="fd14e-787">Vloží instanci `IConfiguration` do třídy `Startup`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-787">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="fd14e-788">Následující příklad předpokládá, že vložená konfigurace je přiřazena vlastnosti `Configuration`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-788">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="fd14e-789">V `Startup.ConfigureServices` načtěte oddíl `Kestrel` konfigurace do konfigurace Kestrel.</span><span class="sxs-lookup"><span data-stu-id="fd14e-789">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration.</span></span>

     ```csharp
     // using Microsoft.Extensions.Configuration

     public void ConfigureServices(IServiceCollection services)
     {
         services.Configure<KestrelServerOptions>(
             Configuration.GetSection("Kestrel"));
     }
     ```

* <span data-ttu-id="fd14e-790">Nakonfigurovat Kestrel při sestavování hostitele:</span><span class="sxs-lookup"><span data-stu-id="fd14e-790">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="fd14e-791">V *program.cs*do konfigurace Kestrel načtěte oddíl `Kestrel` konfigurace:</span><span class="sxs-lookup"><span data-stu-id="fd14e-791">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="fd14e-792">Obě předchozí přístupy fungují u libovolného [poskytovatele konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="fd14e-792">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="fd14e-793">Časový limit Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="fd14e-793">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="fd14e-794">Získá nebo nastaví [časový limit zachování](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="fd14e-794">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="fd14e-795">Výchozí hodnota je 2 minuty.</span><span class="sxs-lookup"><span data-stu-id="fd14e-795">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="fd14e-796">Maximální počet připojení klientů</span><span class="sxs-lookup"><span data-stu-id="fd14e-796">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="fd14e-797">Maximální počet souběžných otevřených připojení TCP lze pro celou aplikaci nastavit pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="fd14e-797">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="fd14e-798">Existuje zvláštní omezení pro připojení, která byla upgradována z protokolu HTTP nebo HTTPS na jiný protokol (například na žádost WebSockets).</span><span class="sxs-lookup"><span data-stu-id="fd14e-798">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="fd14e-799">Po upgradu připojení se nepočítá s limitem @no__t 0.</span><span class="sxs-lookup"><span data-stu-id="fd14e-799">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="fd14e-800">Ve výchozím nastavení je maximální počet připojení neomezené (null).</span><span class="sxs-lookup"><span data-stu-id="fd14e-800">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="fd14e-801">Maximální velikost textu požadavku</span><span class="sxs-lookup"><span data-stu-id="fd14e-801">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="fd14e-802">Výchozí maximální velikost textu požadavku je 30 000 000 bajtů, což je přibližně 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="fd14e-802">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="fd14e-803">Doporučený postup pro přepsání limitu v aplikaci ASP.NET Core MVC je použití atributu <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> u metody akce:</span><span class="sxs-lookup"><span data-stu-id="fd14e-803">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="fd14e-804">Tady je příklad, který ukazuje, jak nakonfigurovat omezení pro aplikaci na každém požadavku:</span><span class="sxs-lookup"><span data-stu-id="fd14e-804">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="fd14e-805">Přepsat nastavení u konkrétního požadavku v middleware:</span><span class="sxs-lookup"><span data-stu-id="fd14e-805">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="fd14e-806">Výjimka je vyvolána, pokud aplikace po zahájení aplikace nakonfiguruje limit na žádost.</span><span class="sxs-lookup"><span data-stu-id="fd14e-806">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="fd14e-807">Existuje vlastnost `IsReadOnly`, která určuje, zda je vlastnost `MaxRequestBodySize` ve stavu jen pro čtení, což znamená, že je příliš pozdě pro konfiguraci limitu.</span><span class="sxs-lookup"><span data-stu-id="fd14e-807">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="fd14e-808">Když je aplikace spouštěna [mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za [ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module), je omezení velikosti textu požadavku Kestrel zakázané, protože služba IIS tento limit již nastavuje.</span><span class="sxs-lookup"><span data-stu-id="fd14e-808">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="fd14e-809">Minimální rychlost těla zprávy požadavku</span><span class="sxs-lookup"><span data-stu-id="fd14e-809">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="fd14e-810">Kestrel kontroluje každou sekundu, pokud data přicházejí za zadanou rychlost v bajtech za sekundu.</span><span class="sxs-lookup"><span data-stu-id="fd14e-810">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="fd14e-811">Pokud frekvence klesne pod minimální hodnotu, vypršel časový limit připojení. Doba odkladu je doba, kterou Kestrel umožňuje klientovi zvýšit rychlost odesílání až na minimum; frekvence není během této doby kontrolována.</span><span class="sxs-lookup"><span data-stu-id="fd14e-811">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="fd14e-812">Doba odkladu pomáhá vyhnout se vyřazení připojení, která při počátečním posílání dat zpomalují rychlost, kvůli pomalému spuštění protokolu TCP.</span><span class="sxs-lookup"><span data-stu-id="fd14e-812">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="fd14e-813">Výchozí minimální sazba je 240 bajtů za sekundu s obdobím 5 sekund odkladu.</span><span class="sxs-lookup"><span data-stu-id="fd14e-813">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="fd14e-814">Pro odpověď platí také minimální sazba.</span><span class="sxs-lookup"><span data-stu-id="fd14e-814">A minimum rate also applies to the response.</span></span> <span data-ttu-id="fd14e-815">Kód pro nastavení limitu požadavků a limit odpovědi je stejný s výjimkou `RequestBody` nebo `Response` v názvech vlastností a rozhraní.</span><span class="sxs-lookup"><span data-stu-id="fd14e-815">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="fd14e-816">Tady je příklad, který ukazuje, jak nakonfigurovat minimální datové tarify v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="fd14e-816">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

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

### <a name="request-headers-timeout"></a><span data-ttu-id="fd14e-817">Časový limit hlaviček žádostí</span><span class="sxs-lookup"><span data-stu-id="fd14e-817">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="fd14e-818">Získá nebo nastaví maximální dobu, po kterou server stráví příjem hlaviček požadavků.</span><span class="sxs-lookup"><span data-stu-id="fd14e-818">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="fd14e-819">Výchozí hodnota je 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="fd14e-819">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="fd14e-820">Synchronní v/v</span><span class="sxs-lookup"><span data-stu-id="fd14e-820">Synchronous IO</span></span>

<span data-ttu-id="fd14e-821"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> řídí, zda je pro požadavek a odpověď povolena synchronní v/v.</span><span class="sxs-lookup"><span data-stu-id="fd14e-821"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="fd14e-822">Výchozí hodnota je `true`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-822">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="fd14e-823">Velký počet blokování synchronních vstupně-výstupních operací může vést ke vyčerpání fondu vláken, což aplikaci nereaguje.</span><span class="sxs-lookup"><span data-stu-id="fd14e-823">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="fd14e-824">Při použití knihovny, která nepodporuje asynchronní vstupně-výstupní operace, povolte pouze `AllowSynchronousIO`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-824">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="fd14e-825">Následující příklad zakáže synchronní v/v:</span><span class="sxs-lookup"><span data-stu-id="fd14e-825">The following example disables synchronous IO:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="fd14e-826">Informace o dalších možnostech a omezeních Kestrel najdete v tématech:</span><span class="sxs-lookup"><span data-stu-id="fd14e-826">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="fd14e-827">Konfigurace koncového bodu</span><span class="sxs-lookup"><span data-stu-id="fd14e-827">Endpoint configuration</span></span>

<span data-ttu-id="fd14e-828">Ve výchozím nastavení ASP.NET Core váže k:</span><span class="sxs-lookup"><span data-stu-id="fd14e-828">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="fd14e-829">`https://localhost:5001` (Pokud je k dispozici místní vývojový certifikát)</span><span class="sxs-lookup"><span data-stu-id="fd14e-829">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="fd14e-830">Zadejte adresy URL pomocí:</span><span class="sxs-lookup"><span data-stu-id="fd14e-830">Specify URLs using the:</span></span>

* <span data-ttu-id="fd14e-831">Proměnná prostředí `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="fd14e-831">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="fd14e-832">argument příkazového řádku `--urls`</span><span class="sxs-lookup"><span data-stu-id="fd14e-832">`--urls` command-line argument.</span></span>
* <span data-ttu-id="fd14e-833">`urls` konfigurační klíč hostitele.</span><span class="sxs-lookup"><span data-stu-id="fd14e-833">`urls` host configuration key.</span></span>
* <span data-ttu-id="fd14e-834">Metoda rozšíření `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-834">`UseUrls` extension method.</span></span>

<span data-ttu-id="fd14e-835">Hodnota zadaná pomocí těchto přístupů může být jeden nebo více koncových bodů HTTP a HTTPS (HTTPS, pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="fd14e-835">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="fd14e-836">Nakonfigurujte hodnotu jako seznam oddělený středníkem (například `"Urls": "http://localhost:8000; http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="fd14e-836">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="fd14e-837">Další informace o těchto přístupůch najdete v tématu [adresy URL serveru](xref:fundamentals/host/web-host#server-urls) a [potlačení konfigurace](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="fd14e-837">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="fd14e-838">Vytvoří se vývojový certifikát:</span><span class="sxs-lookup"><span data-stu-id="fd14e-838">A development certificate is created:</span></span>

* <span data-ttu-id="fd14e-839">Po instalaci [.NET Core SDK](/dotnet/core/sdk) .</span><span class="sxs-lookup"><span data-stu-id="fd14e-839">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="fd14e-840">[Nástroj pro vývoj a certifikáty](xref:aspnetcore-2.1#https) slouží k vytvoření certifikátu.</span><span class="sxs-lookup"><span data-stu-id="fd14e-840">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="fd14e-841">Některé prohlížeče vyžadují udělení explicitního oprávnění pro důvěřování místního vývojového certifikátu.</span><span class="sxs-lookup"><span data-stu-id="fd14e-841">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="fd14e-842">Šablony projektů konfigurují aplikace tak, aby běžely ve výchozím nastavení HTTPS a zahrnovaly [přesměrování https a podporu HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="fd14e-842">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="fd14e-843">Pokud chcete nakonfigurovat předpony a porty adres URL pro Kestrel, volejte <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> nebo <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> na <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="fd14e-843">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="fd14e-844">`UseUrls`, argument příkazového řádku `--urls`, konfigurační klíč hostitele `urls` a proměnná prostředí `ASPNETCORE_URLS` fungují, ale mají omezení zaznamenaná později v této části (pro konfiguraci koncového bodu HTTPS musí být k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="fd14e-844">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="fd14e-845">Konfigurace `KestrelServerOptions`:</span><span class="sxs-lookup"><span data-stu-id="fd14e-845">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="fd14e-846">ConfigureEndpointDefaults (akce @ no__t-0ListenOptions >)</span><span class="sxs-lookup"><span data-stu-id="fd14e-846">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="fd14e-847">Určuje konfiguraci `Action`, která se má spustit pro každý zadaný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="fd14e-847">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="fd14e-848">Volání `ConfigureEndpointDefaults` několikrát nahrazuje předchozí `Action` s určeným posledním `Action`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-848">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="fd14e-849">ConfigureHttpsDefaults (akce @ no__t-0HttpsConnectionAdapterOptions >)</span><span class="sxs-lookup"><span data-stu-id="fd14e-849">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="fd14e-850">Určuje konfiguraci `Action` pro spuštění každého koncového bodu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="fd14e-850">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="fd14e-851">Volání `ConfigureHttpsDefaults` několikrát nahrazuje předchozí `Action` s určeným posledním `Action`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-851">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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

### <a name="configureiconfiguration"></a><span data-ttu-id="fd14e-852">Konfigurace (IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="fd14e-852">Configure(IConfiguration)</span></span>

<span data-ttu-id="fd14e-853">Vytvoří zavaděč konfigurace pro nastavení Kestrel, který jako vstup převezme <xref:Microsoft.Extensions.Configuration.IConfiguration>.</span><span class="sxs-lookup"><span data-stu-id="fd14e-853">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="fd14e-854">Konfigurace musí být vymezená na konfigurační oddíl pro Kestrel.</span><span class="sxs-lookup"><span data-stu-id="fd14e-854">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="fd14e-855">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="fd14e-855">ListenOptions.UseHttps</span></span>

<span data-ttu-id="fd14e-856">Nakonfigurujte Kestrel tak, aby používal protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="fd14e-856">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="fd14e-857">rozšíření `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="fd14e-857">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="fd14e-858">`UseHttps` &ndash; nakonfigurujte Kestrel tak, aby používal HTTPS s výchozím certifikátem.</span><span class="sxs-lookup"><span data-stu-id="fd14e-858">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="fd14e-859">Vyvolá výjimku, pokud není nakonfigurován žádný výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="fd14e-859">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="fd14e-860">parametry `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="fd14e-860">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="fd14e-861">`filename` je cesta a název souboru certifikátu vzhledem k adresáři, který obsahuje soubory obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="fd14e-861">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="fd14e-862">`password` je heslo vyžadované pro přístup k datům certifikátu X. 509.</span><span class="sxs-lookup"><span data-stu-id="fd14e-862">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="fd14e-863">`configureOptions` je `Action` ke konfiguraci `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-863">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="fd14e-864">Vrátí `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-864">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="fd14e-865">`storeName` je úložiště certifikátů, ze kterého se má certifikát načíst.</span><span class="sxs-lookup"><span data-stu-id="fd14e-865">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="fd14e-866">`subject` je název subjektu certifikátu.</span><span class="sxs-lookup"><span data-stu-id="fd14e-866">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="fd14e-867">`allowInvalid` označuje, zda by měly být zváženy neplatné certifikáty, například certifikáty podepsané svým držitelem.</span><span class="sxs-lookup"><span data-stu-id="fd14e-867">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="fd14e-868">`location` je umístění úložiště, ze kterého se má načíst certifikát.</span><span class="sxs-lookup"><span data-stu-id="fd14e-868">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="fd14e-869">`serverCertificate` je certifikát X. 509.</span><span class="sxs-lookup"><span data-stu-id="fd14e-869">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="fd14e-870">V produkčním prostředí musí být HTTPS explicitně nakonfigurovaný.</span><span class="sxs-lookup"><span data-stu-id="fd14e-870">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="fd14e-871">Musí být zadán minimálně výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="fd14e-871">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="fd14e-872">Podporované konfigurace popsané dál:</span><span class="sxs-lookup"><span data-stu-id="fd14e-872">Supported configurations described next:</span></span>

* <span data-ttu-id="fd14e-873">Žádná konfigurace</span><span class="sxs-lookup"><span data-stu-id="fd14e-873">No configuration</span></span>
* <span data-ttu-id="fd14e-874">Nahradit výchozí certifikát z konfigurace</span><span class="sxs-lookup"><span data-stu-id="fd14e-874">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="fd14e-875">Změna výchozích hodnot v kódu</span><span class="sxs-lookup"><span data-stu-id="fd14e-875">Change the defaults in code</span></span>

<span data-ttu-id="fd14e-876">*Žádná konfigurace*</span><span class="sxs-lookup"><span data-stu-id="fd14e-876">*No configuration*</span></span>

<span data-ttu-id="fd14e-877">Kestrel naslouchá na `http://localhost:5000` a `https://localhost:5001` (Pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="fd14e-877">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="fd14e-878">*Nahradit výchozí certifikát z konfigurace*</span><span class="sxs-lookup"><span data-stu-id="fd14e-878">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="fd14e-879">`CreateDefaultBuilder` volá ve výchozím nastavení `Configure(context.Configuration.GetSection("Kestrel"))`, aby se načetla konfigurace Kestrel.</span><span class="sxs-lookup"><span data-stu-id="fd14e-879">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="fd14e-880">Pro Kestrel je k dispozici výchozí schéma konfigurace nastavení aplikace HTTPS.</span><span class="sxs-lookup"><span data-stu-id="fd14e-880">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="fd14e-881">Nakonfigurujte několik koncových bodů, včetně adres URL a certifikátů, které se mají použít, buď ze souboru na disku, nebo z úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="fd14e-881">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="fd14e-882">V následujícím příkladu *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="fd14e-882">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="fd14e-883">Nastavte **AllowInvalid** na `true`, pokud chcete povolit použití neplatných certifikátů (například certifikátů podepsaných svým držitelem).</span><span class="sxs-lookup"><span data-stu-id="fd14e-883">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="fd14e-884">Libovolný koncový bod HTTPS, který neurčuje certifikát (**HttpsDefaultCert** v následujícím příkladu), se vrátí k certifikátu definovanému v části **certifikáty** > **výchozí** nebo vývojový certifikát.</span><span class="sxs-lookup"><span data-stu-id="fd14e-884">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="fd14e-885">Alternativou k použití **cesty** a **hesla** pro libovolný uzel certifikátu je zadání certifikátu pomocí polí úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="fd14e-885">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="fd14e-886">Například **certifikáty** > **výchozí** certifikát lze zadat jako:</span><span class="sxs-lookup"><span data-stu-id="fd14e-886">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="fd14e-887">Poznámky ke schématu:</span><span class="sxs-lookup"><span data-stu-id="fd14e-887">Schema notes:</span></span>

* <span data-ttu-id="fd14e-888">V názvech koncových bodů se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="fd14e-888">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="fd14e-889">Například `HTTPS` a `Https` jsou platné.</span><span class="sxs-lookup"><span data-stu-id="fd14e-889">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="fd14e-890">U každého koncového bodu je vyžadován parametr `Url`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-890">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="fd14e-891">Formát tohoto parametru je stejný jako konfigurační parametr `Urls` nejvyšší úrovně s tím rozdílem, že je omezen na jedinou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="fd14e-891">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="fd14e-892">Tyto koncové body nahrazují hodnoty definované v konfiguraci `Urls` na nejvyšší úrovni místo jejich přidávání do těchto koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="fd14e-892">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="fd14e-893">Koncové body definované v kódu prostřednictvím `Listen` jsou kumulativní s koncovými body definovanými v konfiguračním oddílu.</span><span class="sxs-lookup"><span data-stu-id="fd14e-893">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="fd14e-894">Oddíl `Certificate` je nepovinný.</span><span class="sxs-lookup"><span data-stu-id="fd14e-894">The `Certificate` section is optional.</span></span> <span data-ttu-id="fd14e-895">Pokud se nezadá oddíl `Certificate`, použijí se výchozí hodnoty definované v dřívějších scénářích.</span><span class="sxs-lookup"><span data-stu-id="fd14e-895">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="fd14e-896">Pokud nejsou k dispozici žádné výchozí hodnoty, Server vyvolá výjimku a nemůže se spustit.</span><span class="sxs-lookup"><span data-stu-id="fd14e-896">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="fd14e-897">Oddíl `Certificate` podporuje jak**heslo** &ndash; **, tak**i **certifikát @no__t-** 5**Store** .</span><span class="sxs-lookup"><span data-stu-id="fd14e-897">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="fd14e-898">V takovém případě může být definován libovolný počet koncových bodů, pokud to nezpůsobí konflikty portů.</span><span class="sxs-lookup"><span data-stu-id="fd14e-898">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="fd14e-899">`options.Configure(context.Configuration.GetSection("{SECTION}"))` vrátí `KestrelConfigurationLoader` s metodou `.Endpoint(string name, listenOptions => { })`, která se dá použít k doplnění nastavení nakonfigurovaného koncového bodu:</span><span class="sxs-lookup"><span data-stu-id="fd14e-899">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="fd14e-900">`KestrelServerOptions.ConfigurationLoader` se dá získat přímo k pokračování v iteraci u stávajícího zavaděče, jako je třeba ten, který poskytuje <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="fd14e-900">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="fd14e-901">Konfigurační oddíl pro každý koncový bod je k dispozici na možnostech v metodě @no__t 0, aby bylo možné číst vlastní nastavení.</span><span class="sxs-lookup"><span data-stu-id="fd14e-901">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="fd14e-902">Více konfigurací může být načteno voláním `options.Configure(context.Configuration.GetSection("{SECTION}"))` znovu s jiným oddílem.</span><span class="sxs-lookup"><span data-stu-id="fd14e-902">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="fd14e-903">Je použita pouze poslední konfigurace, pokud není metoda `Load` explicitně volána pro předchozí instance.</span><span class="sxs-lookup"><span data-stu-id="fd14e-903">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="fd14e-904">Metapackage nevolá `Load`, takže jeho výchozí konfigurační oddíl může být nahrazen.</span><span class="sxs-lookup"><span data-stu-id="fd14e-904">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="fd14e-905">`KestrelConfigurationLoader` zrcadlí `Listen` rodinu rozhraní API od `KestrelServerOptions` jako přetížení `Endpoint`, takže koncové body kódu a konfigurace lze konfigurovat na stejném místě.</span><span class="sxs-lookup"><span data-stu-id="fd14e-905">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="fd14e-906">Tato přetížení nepoužívají názvy a využívají pouze výchozí nastavení z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="fd14e-906">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="fd14e-907">*Změna výchozích hodnot v kódu*</span><span class="sxs-lookup"><span data-stu-id="fd14e-907">*Change the defaults in code*</span></span>

<span data-ttu-id="fd14e-908">`ConfigureEndpointDefaults` a `ConfigureHttpsDefaults` lze použít ke změně výchozího nastavení pro `ListenOptions` a `HttpsConnectionAdapterOptions`, včetně přepsání výchozího certifikátu zadaného v předchozím scénáři.</span><span class="sxs-lookup"><span data-stu-id="fd14e-908">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="fd14e-909">`ConfigureEndpointDefaults` a `ConfigureHttpsDefaults` by měly být volány před konfigurací koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="fd14e-909">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="fd14e-910">*Podpora Kestrel pro SNI*</span><span class="sxs-lookup"><span data-stu-id="fd14e-910">*Kestrel support for SNI*</span></span>

<span data-ttu-id="fd14e-911">[Indikace názvu serveru (SNI)](https://tools.ietf.org/html/rfc6066#section-3) lze použít k hostování více domén na stejné IP adrese a portu.</span><span class="sxs-lookup"><span data-stu-id="fd14e-911">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="fd14e-912">Aby SNI fungovalo, klient pošle název hostitele pro zabezpečenou relaci serveru během metody handshake TLS, aby server mohl poskytovat správný certifikát.</span><span class="sxs-lookup"><span data-stu-id="fd14e-912">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="fd14e-913">Klient používá poskytnutý certifikát pro šifrovanou komunikaci se serverem během zabezpečené relace, která následuje za metodou handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="fd14e-913">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="fd14e-914">Kestrel podporuje SNI prostřednictvím zpětného volání `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-914">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="fd14e-915">Zpětné volání je vyvoláno jednou za připojení, které aplikaci umožní zkontrolovat název hostitele a vybrat příslušný certifikát.</span><span class="sxs-lookup"><span data-stu-id="fd14e-915">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="fd14e-916">Podpora SNI vyžaduje:</span><span class="sxs-lookup"><span data-stu-id="fd14e-916">SNI support requires:</span></span>

* <span data-ttu-id="fd14e-917">Spuštění v cílovém rozhraní `netcoreapp2.1` nebo novější.</span><span class="sxs-lookup"><span data-stu-id="fd14e-917">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="fd14e-918">V `net461` nebo novějším je zpětné volání vyvoláno, ale `name` je vždy `null`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-918">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="fd14e-919">@No__t-0 je také `null`, pokud klient neposkytne parametr název hostitele v handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="fd14e-919">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="fd14e-920">Všechny weby běží na stejné instanci Kestrel.</span><span class="sxs-lookup"><span data-stu-id="fd14e-920">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="fd14e-921">Kestrel nepodporuje sdílení IP adresy a portu napříč několika instancemi bez reverzního proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="fd14e-921">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="fd14e-922">Vytvoření vazby na soket TCP</span><span class="sxs-lookup"><span data-stu-id="fd14e-922">Bind to a TCP socket</span></span>

<span data-ttu-id="fd14e-923">Metoda <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> se váže k soketu TCP a obslužná rutina options umožňuje konfiguraci certifikátu X. 509:</span><span class="sxs-lookup"><span data-stu-id="fd14e-923">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

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

<span data-ttu-id="fd14e-924">Příklad konfiguruje HTTPS pro koncový bod s <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="fd14e-924">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="fd14e-925">Ke konfiguraci dalších nastavení Kestrel pro konkrétní koncové body použijte stejné rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="fd14e-925">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="fd14e-926">Vytvoření vazby na soket UNIX</span><span class="sxs-lookup"><span data-stu-id="fd14e-926">Bind to a Unix socket</span></span>

<span data-ttu-id="fd14e-927">Naslouchat na soketu se systémem UNIX pomocí <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> pro zlepšení výkonu pomocí Nginx, jak je znázorněno v tomto příkladu:</span><span class="sxs-lookup"><span data-stu-id="fd14e-927">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

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

### <a name="port-0"></a><span data-ttu-id="fd14e-928">Port 0</span><span class="sxs-lookup"><span data-stu-id="fd14e-928">Port 0</span></span>

<span data-ttu-id="fd14e-929">Když je zadané číslo portu `0`, dynamicky se váže k dostupnému portu.</span><span class="sxs-lookup"><span data-stu-id="fd14e-929">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="fd14e-930">Následující příklad ukazuje, jak určit, který port Kestrel je skutečně vázaný za běhu:</span><span class="sxs-lookup"><span data-stu-id="fd14e-930">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="fd14e-931">Když se aplikace spustí, výstup okna konzoly indikuje dynamický port, na který se aplikace dá získat:</span><span class="sxs-lookup"><span data-stu-id="fd14e-931">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="fd14e-932">Omezení</span><span class="sxs-lookup"><span data-stu-id="fd14e-932">Limitations</span></span>

<span data-ttu-id="fd14e-933">Konfigurace koncových bodů pomocí následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="fd14e-933">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="fd14e-934">argument příkazového řádku `--urls`</span><span class="sxs-lookup"><span data-stu-id="fd14e-934">`--urls` command-line argument</span></span>
* <span data-ttu-id="fd14e-935">konfigurační klíč hostitele `urls`</span><span class="sxs-lookup"><span data-stu-id="fd14e-935">`urls` host configuration key</span></span>
* <span data-ttu-id="fd14e-936">Proměnná prostředí `ASPNETCORE_URLS`</span><span class="sxs-lookup"><span data-stu-id="fd14e-936">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="fd14e-937">Tyto metody jsou užitečné, pokud chcete, aby kód pracoval na jiných serverech než Kestrel.</span><span class="sxs-lookup"><span data-stu-id="fd14e-937">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="fd14e-938">Mějte ale na paměti následující omezení:</span><span class="sxs-lookup"><span data-stu-id="fd14e-938">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="fd14e-939">Protokol HTTPS nelze s těmito přístupy použít, pokud není k dispozici výchozí certifikát v konfiguraci koncového bodu HTTPS (například pomocí konfigurace `KestrelServerOptions` nebo konfiguračního souboru, jak je uvedeno výše v tomto tématu).</span><span class="sxs-lookup"><span data-stu-id="fd14e-939">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="fd14e-940">Při současném použití `Listen` i `UseUrls` jsou koncové body `Listen` popsány v koncových bodech `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-940">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="fd14e-941">Konfigurace koncového bodu služby IIS</span><span class="sxs-lookup"><span data-stu-id="fd14e-941">IIS endpoint configuration</span></span>

<span data-ttu-id="fd14e-942">Při použití služby IIS jsou vazby adresy URL pro přepsání vazby služby IIS nastaveny buď pomocí `Listen`, nebo `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-942">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="fd14e-943">Další informace najdete v tématu [ASP.NET Core modulu](xref:host-and-deploy/aspnet-core-module) .</span><span class="sxs-lookup"><span data-stu-id="fd14e-943">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="fd14e-944">Konfigurace přenosu</span><span class="sxs-lookup"><span data-stu-id="fd14e-944">Transport configuration</span></span>

<span data-ttu-id="fd14e-945">S vydáním ASP.NET Core 2,1 není výchozí přenos Kestrel založen na Libuv, ale na základě spravovaných soketů.</span><span class="sxs-lookup"><span data-stu-id="fd14e-945">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="fd14e-946">Jedná se o zásadní změnu pro aplikace ASP.NET Core 2,0 upgradované na 2,1, která volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> a závisí na jednom z následujících balíčků:</span><span class="sxs-lookup"><span data-stu-id="fd14e-946">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="fd14e-947">[Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (přímý odkaz na balíček)</span><span class="sxs-lookup"><span data-stu-id="fd14e-947">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="fd14e-948">Microsoft. AspNetCore. app</span><span class="sxs-lookup"><span data-stu-id="fd14e-948">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="fd14e-949">Pro projekty, které vyžadují použití Libuv:</span><span class="sxs-lookup"><span data-stu-id="fd14e-949">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="fd14e-950">Přidejte do souboru projektu aplikace závislost pro balíček [Microsoft. AspNetCore. Server. Kestrel. Transport. Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) :</span><span class="sxs-lookup"><span data-stu-id="fd14e-950">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="fd14e-951">Volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span><span class="sxs-lookup"><span data-stu-id="fd14e-951">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="fd14e-952">Předpony adresy URL</span><span class="sxs-lookup"><span data-stu-id="fd14e-952">URL prefixes</span></span>

<span data-ttu-id="fd14e-953">Při použití argumentu příkazového řádku `UseUrls`, `--urls`, klíče konfigurace hostitele `urls` nebo proměnné prostředí `ASPNETCORE_URLS` můžou být předpony adresy URL v některém z následujících formátů.</span><span class="sxs-lookup"><span data-stu-id="fd14e-953">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="fd14e-954">Platné jsou pouze předpony adresy URL protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="fd14e-954">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="fd14e-955">Kestrel nepodporuje HTTPS při konfiguraci vazeb URL pomocí `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="fd14e-955">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="fd14e-956">Adresa IPv4 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="fd14e-956">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="fd14e-957">`0.0.0.0` je zvláštní případ, který se váže ke všem adresám IPv4.</span><span class="sxs-lookup"><span data-stu-id="fd14e-957">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="fd14e-958">Adresa IPv6 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="fd14e-958">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="fd14e-959">`[::]` je ekvivalent protokolu IPv4 `0.0.0.0` protokolu IPv6.</span><span class="sxs-lookup"><span data-stu-id="fd14e-959">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="fd14e-960">Název hostitele s číslem portu</span><span class="sxs-lookup"><span data-stu-id="fd14e-960">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="fd14e-961">Názvy hostitelů `*` a `+` nejsou speciální.</span><span class="sxs-lookup"><span data-stu-id="fd14e-961">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="fd14e-962">Cokoli se nerozpoznalo jako platná IP adresa nebo `localhost` váže ke všem IP adresám IPv4 a IPv6.</span><span class="sxs-lookup"><span data-stu-id="fd14e-962">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="fd14e-963">Pokud chcete navazovat různé názvy hostitelů na různé ASP.NET Core aplikace na stejném portu, použijte [http. sys](xref:fundamentals/servers/httpsys) nebo reverzní proxy server, třeba IIS, Nginx nebo Apache.</span><span class="sxs-lookup"><span data-stu-id="fd14e-963">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="fd14e-964">Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="fd14e-964">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="fd14e-965">Hostitel @no__t – 0 název s číslem portu nebo IP adresou zpětné smyčky s číslem portu</span><span class="sxs-lookup"><span data-stu-id="fd14e-965">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="fd14e-966">Pokud je zadána hodnota `localhost`, pokusí se Kestrel vytvořit propojení s rozhraními zpětné smyčky IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="fd14e-966">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="fd14e-967">Pokud je požadovaný port používán jinou službou na rozhraní zpětné smyčky, spuštění Kestrel se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="fd14e-967">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="fd14e-968">Pokud je rozhraní zpětné smyčky v žádném jiném případě nedostupné (většinou kvůli tomu, že protokol IPv6 není podporovaný), Kestrel zaznamená upozornění.</span><span class="sxs-lookup"><span data-stu-id="fd14e-968">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="fd14e-969">Filtrování hostitele</span><span class="sxs-lookup"><span data-stu-id="fd14e-969">Host filtering</span></span>

<span data-ttu-id="fd14e-970">I když Kestrel podporuje konfiguraci na základě prefixů, jako je například `http://example.com:5000`, Kestrel převážně ignoruje název hostitele.</span><span class="sxs-lookup"><span data-stu-id="fd14e-970">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="fd14e-971">Hostitel @no__t – 0 je zvláštní případ, který se používá pro vazbu na adresy zpětné smyčky.</span><span class="sxs-lookup"><span data-stu-id="fd14e-971">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="fd14e-972">Každý hostitel, který je jiný než explicitní IP adresa, se váže na všechny veřejné IP adresy.</span><span class="sxs-lookup"><span data-stu-id="fd14e-972">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="fd14e-973">hlavičky `Host` nejsou ověřeny.</span><span class="sxs-lookup"><span data-stu-id="fd14e-973">`Host` headers aren't validated.</span></span>

<span data-ttu-id="fd14e-974">Jako alternativní řešení použijte middleware pro filtrování hostitele.</span><span class="sxs-lookup"><span data-stu-id="fd14e-974">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="fd14e-975">Middleware pro filtrování hostitele poskytuje balíček [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , který je součástí [Microsoft. AspNetCore. App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 nebo 2,2).</span><span class="sxs-lookup"><span data-stu-id="fd14e-975">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="fd14e-976">Middleware se přidá pomocí <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, která volá <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="fd14e-976">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="fd14e-977">Middleware pro filtrování hostitele je ve výchozím nastavení zakázáno.</span><span class="sxs-lookup"><span data-stu-id="fd14e-977">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="fd14e-978">Chcete-li povolit middleware, definujte @no__t klíč-0 v souboru *appSettings. json*/*appSettings. \<EnvironmentName >. JSON*.</span><span class="sxs-lookup"><span data-stu-id="fd14e-978">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="fd14e-979">Hodnota je seznam názvů hostitelů oddělených středníkem bez čísel portů:</span><span class="sxs-lookup"><span data-stu-id="fd14e-979">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="fd14e-980">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="fd14e-980">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="fd14e-981">[Middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer) má taky možnost <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="fd14e-981">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="fd14e-982">Middleware předávaných hlaviček a middleware pro filtrování hostitele mají podobné funkce pro různé scénáře.</span><span class="sxs-lookup"><span data-stu-id="fd14e-982">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="fd14e-983">Nastavení `AllowedHosts` s předaným middlewarem hlaviček je vhodné, když se hlavička `Host` nezachová při předávání požadavků pomocí reverzního proxy server nebo nástroje pro vyrovnávání zatížení.</span><span class="sxs-lookup"><span data-stu-id="fd14e-983">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="fd14e-984">Nastavení `AllowedHosts` pomocí middlewaru pro filtrování hostitele je vhodné, pokud se Kestrel používá jako veřejný hraniční Server nebo když je hlavička `Host` přímo předána.</span><span class="sxs-lookup"><span data-stu-id="fd14e-984">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="fd14e-985">Další informace o middlewaru předávaných hlaviček najdete v tématu <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="fd14e-985">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="fd14e-986">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="fd14e-986">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="fd14e-987">RFC 7230: syntaxe zprávy a směrování (oddíl 5,4: hostitel)</span><span class="sxs-lookup"><span data-stu-id="fd14e-987">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
