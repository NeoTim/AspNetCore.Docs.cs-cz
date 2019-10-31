---
title: Implementace webového serveru Kestrel v ASP.NET Core
author: guardrex
description: Přečtěte si o Kestrel, webovém serveru pro různé platformy pro ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/29/2019
uid: fundamentals/servers/kestrel
ms.openlocfilehash: beaf6ac49359adfdc2dc24221eab04cc853646a9
ms.sourcegitcommit: de0fc77487a4d342bcc30965ec5c142d10d22c03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73143441"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="47d51-103">Implementace webového serveru Kestrel v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="47d51-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="47d51-104">Tím, že [Dykstra](https://github.com/tdykstra), [Chris Rossův](https://github.com/Tratcher), [Stephen zastavili](https://twitter.com/halter73)a [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="47d51-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), [Stephen Halter](https://twitter.com/halter73), and [Luke Latham](https://github.com/guardrex)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="47d51-105">Kestrel je [webový server pro různé platformy pro ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="47d51-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="47d51-106">Kestrel je webový server, který je ve výchozím nastavení součástí ASP.NET Core šablon projektů.</span><span class="sxs-lookup"><span data-stu-id="47d51-106">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="47d51-107">Kestrel podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="47d51-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="47d51-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="47d51-108">HTTPS</span></span>
* <span data-ttu-id="47d51-109">Neprůhledný upgrade používaný k povolení [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="47d51-109">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="47d51-110">Technologie UNIX Sockets s vysokým výkonem za Nginx</span><span class="sxs-lookup"><span data-stu-id="47d51-110">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="47d51-111">HTTP/2 (kromě macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="47d51-111">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="47d51-112">&dagger;HTTP/2 bude v budoucí verzi podporován v macOS.</span><span class="sxs-lookup"><span data-stu-id="47d51-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="47d51-113">Kestrel se podporuje na všech platformách a verzích, které podporuje .NET Core.</span><span class="sxs-lookup"><span data-stu-id="47d51-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="47d51-114">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="47d51-114">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="47d51-115">Podpora HTTP/2</span><span class="sxs-lookup"><span data-stu-id="47d51-115">HTTP/2 support</span></span>

<span data-ttu-id="47d51-116">[Http/2](https://httpwg.org/specs/rfc7540.html) je k dispozici pro ASP.NET Core aplikace, pokud jsou splněné následující základní požadavky:</span><span class="sxs-lookup"><span data-stu-id="47d51-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="47d51-117">&dagger; operačního systému</span><span class="sxs-lookup"><span data-stu-id="47d51-117">Operating system&dagger;</span></span>
  * <span data-ttu-id="47d51-118">Windows Server 2016/Windows 10 nebo novější&Dagger;</span><span class="sxs-lookup"><span data-stu-id="47d51-118">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="47d51-119">Linux s OpenSSL 1.0.2 nebo novějším (například Ubuntu 16,04 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="47d51-119">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="47d51-120">Cílová architektura: .NET Core 2,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="47d51-120">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="47d51-121">Připojení [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="47d51-121">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="47d51-122">Připojení TLS 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="47d51-122">TLS 1.2 or later connection</span></span>

<span data-ttu-id="47d51-123">&dagger;HTTP/2 bude v budoucí verzi podporován v macOS.</span><span class="sxs-lookup"><span data-stu-id="47d51-123">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="47d51-124">&Dagger;Kestrel má omezená podpora HTTP/2 na Windows Serveru 2012 R2 a Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="47d51-124">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="47d51-125">Podpora je omezená, protože seznam podporovaných šifrovacích sad TLS, které jsou k dispozici v těchto operačních systémech, je omezený.</span><span class="sxs-lookup"><span data-stu-id="47d51-125">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="47d51-126">Pro zabezpečení připojení TLS může být vyžadován certifikát vygenerovaný pomocí algoritmu ECDSA (s připojením typu eliptická křivka).</span><span class="sxs-lookup"><span data-stu-id="47d51-126">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="47d51-127">Pokud je navázáno připojení HTTP/2, sestavy [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="47d51-127">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="47d51-128">HTTP/2 je ve výchozím nastavení zakázané.</span><span class="sxs-lookup"><span data-stu-id="47d51-128">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="47d51-129">Další informace o konfiguraci najdete v částech [Možnosti Kestrel](#kestrel-options) a [ListenOptions. Protocols](#listenoptionsprotocols) .</span><span class="sxs-lookup"><span data-stu-id="47d51-129">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="47d51-130">Kdy použít Kestrel s reverzním proxy serverem</span><span class="sxs-lookup"><span data-stu-id="47d51-130">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="47d51-131">Kestrel může používat sám sebe nebo s *reverzními proxy server*, jako je [Internetová informační služba (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="47d51-131">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="47d51-132">Reverzní proxy server přijímá požadavky HTTP ze sítě a přesměruje je na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="47d51-132">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="47d51-133">Kestrel použitý jako hraniční webový server (internetový):</span><span class="sxs-lookup"><span data-stu-id="47d51-133">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel komunikuje přímo s internetem bez zpětného proxy server](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="47d51-135">Kestrel se používá v konfiguraci reverzního proxy serveru:</span><span class="sxs-lookup"><span data-stu-id="47d51-135">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel nepřímo komunikuje s internetem prostřednictvím reverzní proxy server, jako je IIS, Nginx nebo Apache.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="47d51-137">Buď konfigurace, s zpětným proxy server nebo bez něj, je podporovaná konfigurace hostování.</span><span class="sxs-lookup"><span data-stu-id="47d51-137">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="47d51-138">Kestrel používané jako hraniční Server bez reverzních proxy server nepodporuje sdílení stejné IP adresy a portu mezi více procesy.</span><span class="sxs-lookup"><span data-stu-id="47d51-138">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="47d51-139">Když je Kestrel nakonfigurovaný k naslouchání na portu, Kestrel zpracovává veškerý provoz tohoto portu bez ohledu na hlavičky `Host`.</span><span class="sxs-lookup"><span data-stu-id="47d51-139">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="47d51-140">Reverzní proxy server, který může sdílet porty, má schopnost přesměrovat požadavky na Kestrel na jedinečné IP adresy a porty.</span><span class="sxs-lookup"><span data-stu-id="47d51-140">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="47d51-141">I v případě, že se zpětný proxy server nepožaduje, může být použití reverzní proxy server vhodnou volbou.</span><span class="sxs-lookup"><span data-stu-id="47d51-141">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="47d51-142">Reverzní proxy:</span><span class="sxs-lookup"><span data-stu-id="47d51-142">A reverse proxy:</span></span>

* <span data-ttu-id="47d51-143">Může omezit vystavenou veřejnou plochu v aplikacích, které hostuje.</span><span class="sxs-lookup"><span data-stu-id="47d51-143">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="47d51-144">Poskytněte další vrstvu konfigurace a obrany.</span><span class="sxs-lookup"><span data-stu-id="47d51-144">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="47d51-145">Může se lépe integrovat se stávající infrastrukturou.</span><span class="sxs-lookup"><span data-stu-id="47d51-145">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="47d51-146">Zjednodušte vyrovnávání zatížení a konfiguraci zabezpečené komunikace (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="47d51-146">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="47d51-147">Pouze reverzní proxy server vyžaduje certifikát X. 509 a tento server může komunikovat se servery aplikace v interní síti pomocí prostého protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="47d51-147">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="47d51-148">Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="47d51-148">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="47d51-149">Použití Kestrel v aplikacích ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="47d51-149">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="47d51-150">ASP.NET Core šablony projektu ve výchozím nastavení používají Kestrel.</span><span class="sxs-lookup"><span data-stu-id="47d51-150">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="47d51-151">V *program.cs*kód šablony volá <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>, která volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> na pozadí.</span><span class="sxs-lookup"><span data-stu-id="47d51-151">In *Program.cs*, the template code calls <xref:Microsoft.Extensions.Hosting.Host.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="47d51-152">Další informace o `CreateDefaultBuilder` a vytváření hostitele najdete v oddílech nastavení <xref:fundamentals/host/generic-host#set-up-a-host>nastavení *hostitele* a *výchozího tvůrce* .</span><span class="sxs-lookup"><span data-stu-id="47d51-152">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="47d51-153">Pokud chcete po volání `CreateDefaultBuilder` a `ConfigureWebHostDefaults` zadat další konfiguraci, použijte `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="47d51-153">To provide additional configuration after calling `CreateDefaultBuilder` and `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

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

<span data-ttu-id="47d51-154">Pokud aplikace nevolá `CreateDefaultBuilder` pro nastavení hostitele, **před** voláním `ConfigureKestrel` volejte <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span><span class="sxs-lookup"><span data-stu-id="47d51-154">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="47d51-155">Kestrel možnosti</span><span class="sxs-lookup"><span data-stu-id="47d51-155">Kestrel options</span></span>

<span data-ttu-id="47d51-156">Webový server Kestrel má možnosti konfigurace omezení, které jsou zvláště užitečné v internetových nasazeních.</span><span class="sxs-lookup"><span data-stu-id="47d51-156">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="47d51-157">Nastavte omezení pro vlastnost <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> třídy <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="47d51-157">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="47d51-158">Vlastnost `Limits` obsahuje instanci třídy <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="47d51-158">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="47d51-159">V následujících příkladech se používá obor názvů <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:</span><span class="sxs-lookup"><span data-stu-id="47d51-159">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="47d51-160">Možnosti Kestrel, které jsou konfigurovány C# v kódu v následujících příkladech, lze také nastavit pomocí [poskytovatele konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="47d51-160">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="47d51-161">Poskytovatel konfigurace souboru může například načíst konfiguraci Kestrel z objektu *appSettings. JSON* nebo *appSettings. { Soubor Environment}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="47d51-161">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="47d51-162">Použijte **jeden** z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="47d51-162">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="47d51-163">Konfigurace Kestrel v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="47d51-163">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="47d51-164">Vloží instanci `IConfiguration` do třídy `Startup`.</span><span class="sxs-lookup"><span data-stu-id="47d51-164">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="47d51-165">Následující příklad předpokládá, že vložená konfigurace je přiřazena vlastnosti `Configuration`.</span><span class="sxs-lookup"><span data-stu-id="47d51-165">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="47d51-166">V `Startup.ConfigureServices` načtěte oddíl `Kestrel` konfigurace do konfigurace Kestrel.</span><span class="sxs-lookup"><span data-stu-id="47d51-166">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration.</span></span>

     ```csharp
     // using Microsoft.Extensions.Configuration

     public void ConfigureServices(IServiceCollection services)
     {
         services.Configure<KestrelServerOptions>(
             Configuration.GetSection("Kestrel"));
     }
     ```

* <span data-ttu-id="47d51-167">Nakonfigurovat Kestrel při sestavování hostitele:</span><span class="sxs-lookup"><span data-stu-id="47d51-167">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="47d51-168">V *program.cs*do konfigurace Kestrel načtěte oddíl `Kestrel` konfigurace:</span><span class="sxs-lookup"><span data-stu-id="47d51-168">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="47d51-169">Obě předchozí přístupy fungují u libovolného [poskytovatele konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="47d51-169">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="47d51-170">Časový limit Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="47d51-170">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="47d51-171">Získá nebo nastaví [časový limit zachování](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="47d51-171">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="47d51-172">Výchozí hodnota je 2 minuty.</span><span class="sxs-lookup"><span data-stu-id="47d51-172">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="47d51-173">Maximální počet připojení klientů</span><span class="sxs-lookup"><span data-stu-id="47d51-173">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="47d51-174">Maximální počet souběžných otevřených připojení TCP lze pro celou aplikaci nastavit pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="47d51-174">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="47d51-175">Existuje zvláštní omezení pro připojení, která byla upgradována z protokolu HTTP nebo HTTPS na jiný protokol (například na žádost WebSockets).</span><span class="sxs-lookup"><span data-stu-id="47d51-175">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="47d51-176">Po upgradu připojení se nepočítá s limitem `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="47d51-176">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="47d51-177">Ve výchozím nastavení je maximální počet připojení neomezené (null).</span><span class="sxs-lookup"><span data-stu-id="47d51-177">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="47d51-178">Maximální velikost textu požadavku</span><span class="sxs-lookup"><span data-stu-id="47d51-178">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="47d51-179">Výchozí maximální velikost textu požadavku je 30 000 000 bajtů, což je přibližně 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="47d51-179">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="47d51-180">Doporučený postup pro přepsání limitu v aplikaci ASP.NET Core MVC je použití atributu <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> u metody akce:</span><span class="sxs-lookup"><span data-stu-id="47d51-180">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="47d51-181">Tady je příklad, který ukazuje, jak nakonfigurovat omezení pro aplikaci na každém požadavku:</span><span class="sxs-lookup"><span data-stu-id="47d51-181">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="47d51-182">Přepsat nastavení u konkrétního požadavku v middleware:</span><span class="sxs-lookup"><span data-stu-id="47d51-182">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="47d51-183">Výjimka je vyvolána, pokud aplikace po zahájení aplikace nakonfiguruje limit na žádost.</span><span class="sxs-lookup"><span data-stu-id="47d51-183">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="47d51-184">Existuje vlastnost `IsReadOnly`, která určuje, zda je vlastnost `MaxRequestBodySize` ve stavu jen pro čtení, což znamená, že je příliš pozdě pro konfiguraci limitu.</span><span class="sxs-lookup"><span data-stu-id="47d51-184">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="47d51-185">Když je aplikace spouštěna [mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za [ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module), je omezení velikosti textu požadavku Kestrel zakázané, protože služba IIS tento limit již nastavuje.</span><span class="sxs-lookup"><span data-stu-id="47d51-185">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="47d51-186">Minimální rychlost těla zprávy požadavku</span><span class="sxs-lookup"><span data-stu-id="47d51-186">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="47d51-187">Kestrel kontroluje každou sekundu, pokud data přicházejí za zadanou rychlost v bajtech za sekundu.</span><span class="sxs-lookup"><span data-stu-id="47d51-187">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="47d51-188">Pokud frekvence klesne pod minimální hodnotu, vypršel časový limit připojení. Doba odkladu je doba, kterou Kestrel umožňuje klientovi zvýšit rychlost odesílání až na minimum; frekvence není během této doby kontrolována.</span><span class="sxs-lookup"><span data-stu-id="47d51-188">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="47d51-189">Doba odkladu pomáhá vyhnout se vyřazení připojení, která při počátečním posílání dat zpomalují rychlost, kvůli pomalému spuštění protokolu TCP.</span><span class="sxs-lookup"><span data-stu-id="47d51-189">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="47d51-190">Výchozí minimální sazba je 240 bajtů za sekundu s obdobím 5 sekund odkladu.</span><span class="sxs-lookup"><span data-stu-id="47d51-190">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="47d51-191">Pro odpověď platí také minimální sazba.</span><span class="sxs-lookup"><span data-stu-id="47d51-191">A minimum rate also applies to the response.</span></span> <span data-ttu-id="47d51-192">Kód pro nastavení limitu požadavků a limit odpovědi je stejný s výjimkou `RequestBody` nebo `Response` v názvech vlastností a rozhraní.</span><span class="sxs-lookup"><span data-stu-id="47d51-192">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="47d51-193">Tady je příklad, který ukazuje, jak nakonfigurovat minimální datové tarify v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="47d51-193">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="47d51-194">Přepsat minimální limity přenosové rychlosti na žádost v middlewaru:</span><span class="sxs-lookup"><span data-stu-id="47d51-194">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="47d51-195"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature>, na které se odkazuje v předchozí ukázce, není k dispozici v `HttpContext.Features` pro požadavky HTTP/2, protože Změna omezení přenosové rychlosti u jednotlivých požadavků je obecně Nepodporovaná pro HTTP/2 kvůli podpoře protokolu pro multiplexování požadavků.</span><span class="sxs-lookup"><span data-stu-id="47d51-195">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="47d51-196"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> však stále existují `HttpContext.Features` požadavků HTTP/2, protože omezení četnosti čtení je stále možné *úplně zakázat* na základě jednotlivých požadavků nastavením `IHttpMinRequestBodyDataRateFeature.MinDataRate` na `null` i pro požadavek HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="47d51-196">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="47d51-197">Při pokusu o čtení `IHttpMinRequestBodyDataRateFeature.MinDataRate` nebo pokusu o její nastavení na jinou hodnotu než `null` bude výsledkem `NotSupportedException` vyjímka požadavku HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="47d51-197">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="47d51-198">Omezení přenosové rychlosti pro všechny servery nakonfigurovaná přes `KestrelServerOptions.Limits` se stále vztahují na připojení HTTP/1. x i HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="47d51-198">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="47d51-199">Časový limit hlaviček žádostí</span><span class="sxs-lookup"><span data-stu-id="47d51-199">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="47d51-200">Získá nebo nastaví maximální dobu, po kterou server stráví příjem hlaviček požadavků.</span><span class="sxs-lookup"><span data-stu-id="47d51-200">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="47d51-201">Výchozí hodnota je 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="47d51-201">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="47d51-202">Maximální počet proudů na připojení</span><span class="sxs-lookup"><span data-stu-id="47d51-202">Maximum streams per connection</span></span>

<span data-ttu-id="47d51-203">`Http2.MaxStreamsPerConnection` omezuje počet souběžných proudů požadavků na připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="47d51-203">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="47d51-204">Nadbytečné proudy jsou odmítnuty.</span><span class="sxs-lookup"><span data-stu-id="47d51-204">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="47d51-205">Výchozí hodnota je 100.</span><span class="sxs-lookup"><span data-stu-id="47d51-205">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="47d51-206">Velikost tabulky hlaviček</span><span class="sxs-lookup"><span data-stu-id="47d51-206">Header table size</span></span>

<span data-ttu-id="47d51-207">Dekodér HPACK dekomprimuje hlavičky HTTP pro připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="47d51-207">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="47d51-208">`Http2.HeaderTableSize` omezuje velikost kompresní tabulky hlaviček, kterou používá dekodér HPACK.</span><span class="sxs-lookup"><span data-stu-id="47d51-208">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="47d51-209">Hodnota je uvedena v oktetech a musí být větší než nula (0).</span><span class="sxs-lookup"><span data-stu-id="47d51-209">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="47d51-210">Výchozí hodnota je 4096.</span><span class="sxs-lookup"><span data-stu-id="47d51-210">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="47d51-211">Maximální velikost rámce</span><span class="sxs-lookup"><span data-stu-id="47d51-211">Maximum frame size</span></span>

<span data-ttu-id="47d51-212">`Http2.MaxFrameSize` označuje maximální povolenou velikost datové části rámce připojení HTTP/2 přijatého nebo odesílaného serverem.</span><span class="sxs-lookup"><span data-stu-id="47d51-212">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="47d51-213">Hodnota je uvedena v oktetech a musí být v rozmezí 2 ^ 14 (16 384) až 2 ^ 24-1 (16 777 215).</span><span class="sxs-lookup"><span data-stu-id="47d51-213">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="47d51-214">Výchozí hodnota je 2 ^ 14 (16 384).</span><span class="sxs-lookup"><span data-stu-id="47d51-214">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="47d51-215">Maximální velikost hlavičky požadavku</span><span class="sxs-lookup"><span data-stu-id="47d51-215">Maximum request header size</span></span>

<span data-ttu-id="47d51-216">`Http2.MaxRequestHeaderFieldSize` označuje maximální povolenou velikost v oktetech hodnot hlaviček požadavku.</span><span class="sxs-lookup"><span data-stu-id="47d51-216">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="47d51-217">Toto omezení se vztahuje na název i hodnotu v jejich komprimovaných a nekomprimovaných reprezentujcích.</span><span class="sxs-lookup"><span data-stu-id="47d51-217">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="47d51-218">Hodnota musí být větší než nula (0).</span><span class="sxs-lookup"><span data-stu-id="47d51-218">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="47d51-219">Výchozí hodnota je 8 192.</span><span class="sxs-lookup"><span data-stu-id="47d51-219">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="47d51-220">Velikost okna počátečního připojení</span><span class="sxs-lookup"><span data-stu-id="47d51-220">Initial connection window size</span></span>

<span data-ttu-id="47d51-221">`Http2.InitialConnectionWindowSize` označuje maximální velikost textu žádosti v bajtech. vyrovnávací paměti serveru se v jednom okamžiku agreguje napříč všemi požadavky (datovými proudy) na připojení.</span><span class="sxs-lookup"><span data-stu-id="47d51-221">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="47d51-222">Žádosti jsou také omezené pomocí `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="47d51-222">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="47d51-223">Hodnota musí být větší než nebo rovna 65 535 a menší než 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="47d51-223">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="47d51-224">Výchozí hodnota je 128 KB (131 072).</span><span class="sxs-lookup"><span data-stu-id="47d51-224">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="47d51-225">Počáteční velikost okna datového proudu</span><span class="sxs-lookup"><span data-stu-id="47d51-225">Initial stream window size</span></span>

<span data-ttu-id="47d51-226">`Http2.InitialStreamWindowSize` označuje maximální velikost textu požadavku v bajtech na jednu žádost (Stream) na jednom místě.</span><span class="sxs-lookup"><span data-stu-id="47d51-226">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="47d51-227">Žádosti jsou také omezené pomocí `Http2.InitialConnectionWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="47d51-227">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="47d51-228">Hodnota musí být větší než nebo rovna 65 535 a menší než 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="47d51-228">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="47d51-229">Výchozí hodnota je 96 KB (98 304).</span><span class="sxs-lookup"><span data-stu-id="47d51-229">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="47d51-230">Synchronní v/v</span><span class="sxs-lookup"><span data-stu-id="47d51-230">Synchronous IO</span></span>

<span data-ttu-id="47d51-231"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> řídí, zda je pro požadavek a odpověď povolena synchronní v/v.</span><span class="sxs-lookup"><span data-stu-id="47d51-231"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="47d51-232">Výchozí hodnota je `false`.</span><span class="sxs-lookup"><span data-stu-id="47d51-232">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="47d51-233">Velký počet blokování synchronních vstupně-výstupních operací může vést ke vyčerpání fondu vláken, což aplikaci nereaguje.</span><span class="sxs-lookup"><span data-stu-id="47d51-233">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="47d51-234">Při použití knihovny, která nepodporuje asynchronní vstupně-výstupní operace, povolte pouze `AllowSynchronousIO`.</span><span class="sxs-lookup"><span data-stu-id="47d51-234">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="47d51-235">Následující příklad povoluje synchronní vstupně-výstupní operace:</span><span class="sxs-lookup"><span data-stu-id="47d51-235">The following example enables synchronous IO:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="47d51-236">Informace o dalších možnostech a omezeních Kestrel najdete v tématech:</span><span class="sxs-lookup"><span data-stu-id="47d51-236">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="47d51-237">Konfigurace koncového bodu</span><span class="sxs-lookup"><span data-stu-id="47d51-237">Endpoint configuration</span></span>

<span data-ttu-id="47d51-238">Ve výchozím nastavení ASP.NET Core váže k:</span><span class="sxs-lookup"><span data-stu-id="47d51-238">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="47d51-239">`https://localhost:5001` (Pokud je k dispozici místní vývojový certifikát)</span><span class="sxs-lookup"><span data-stu-id="47d51-239">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="47d51-240">Zadejte adresy URL pomocí:</span><span class="sxs-lookup"><span data-stu-id="47d51-240">Specify URLs using the:</span></span>

* <span data-ttu-id="47d51-241">`ASPNETCORE_URLS` proměnnou prostředí.</span><span class="sxs-lookup"><span data-stu-id="47d51-241">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="47d51-242">argument příkazového řádku `--urls`</span><span class="sxs-lookup"><span data-stu-id="47d51-242">`--urls` command-line argument.</span></span>
* <span data-ttu-id="47d51-243">`urls` konfigurační klíč hostitele.</span><span class="sxs-lookup"><span data-stu-id="47d51-243">`urls` host configuration key.</span></span>
* <span data-ttu-id="47d51-244">Metoda rozšíření `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="47d51-244">`UseUrls` extension method.</span></span>

<span data-ttu-id="47d51-245">Hodnota zadaná pomocí těchto přístupů může být jeden nebo více koncových bodů HTTP a HTTPS (HTTPS, pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="47d51-245">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="47d51-246">Nakonfigurujte hodnotu jako seznam oddělený středníkem (například `"Urls": "http://localhost:8000; http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="47d51-246">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="47d51-247">Další informace o těchto přístupůch najdete v tématu [adresy URL serveru](xref:fundamentals/host/web-host#server-urls) a [potlačení konfigurace](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="47d51-247">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="47d51-248">Vytvoří se vývojový certifikát:</span><span class="sxs-lookup"><span data-stu-id="47d51-248">A development certificate is created:</span></span>

* <span data-ttu-id="47d51-249">Po instalaci [.NET Core SDK](/dotnet/core/sdk) .</span><span class="sxs-lookup"><span data-stu-id="47d51-249">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="47d51-250">[Nástroj pro vývoj a certifikáty](xref:aspnetcore-2.1#https) slouží k vytvoření certifikátu.</span><span class="sxs-lookup"><span data-stu-id="47d51-250">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="47d51-251">Některé prohlížeče vyžadují udělení explicitního oprávnění pro důvěřování místního vývojového certifikátu.</span><span class="sxs-lookup"><span data-stu-id="47d51-251">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="47d51-252">Šablony projektů konfigurují aplikace tak, aby běžely ve výchozím nastavení HTTPS a zahrnovaly [přesměrování https a podporu HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="47d51-252">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="47d51-253">Pokud chcete nakonfigurovat předpony a porty adres URL pro Kestrel, volejte <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> nebo <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> na <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="47d51-253">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="47d51-254">`UseUrls`, argument příkazového řádku `--urls`, `urls` konfigurační klíč hostitele a proměnná prostředí `ASPNETCORE_URLS` fungují, ale mají omezení zaznamenaná později v této části (pro konfiguraci koncového bodu HTTPS musí být k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="47d51-254">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="47d51-255">Konfigurace `KestrelServerOptions`:</span><span class="sxs-lookup"><span data-stu-id="47d51-255">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="47d51-256">ConfigureEndpointDefaults (Action\<ListenOptions >)</span><span class="sxs-lookup"><span data-stu-id="47d51-256">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="47d51-257">Určuje konfiguraci `Action`, která se má spustit pro každý zadaný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="47d51-257">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="47d51-258">Volání `ConfigureEndpointDefaults` několikrát nahrazuje předchozí `Action` s určeným posledním `Action`.</span><span class="sxs-lookup"><span data-stu-id="47d51-258">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="47d51-259">ConfigureHttpsDefaults (Action\<HttpsConnectionAdapterOptions >)</span><span class="sxs-lookup"><span data-stu-id="47d51-259">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="47d51-260">Určuje konfiguraci `Action` pro spuštění každého koncového bodu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="47d51-260">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="47d51-261">Volání `ConfigureHttpsDefaults` několikrát nahrazuje předchozí `Action` s určeným posledním `Action`.</span><span class="sxs-lookup"><span data-stu-id="47d51-261">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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

### <a name="configureiconfiguration"></a><span data-ttu-id="47d51-262">Konfigurace (IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="47d51-262">Configure(IConfiguration)</span></span>

<span data-ttu-id="47d51-263">Vytvoří zavaděč konfigurace pro nastavení Kestrel, který jako vstup převezme <xref:Microsoft.Extensions.Configuration.IConfiguration>.</span><span class="sxs-lookup"><span data-stu-id="47d51-263">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="47d51-264">Konfigurace musí být vymezená na konfigurační oddíl pro Kestrel.</span><span class="sxs-lookup"><span data-stu-id="47d51-264">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="47d51-265">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="47d51-265">ListenOptions.UseHttps</span></span>

<span data-ttu-id="47d51-266">Nakonfigurujte Kestrel tak, aby používal protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="47d51-266">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="47d51-267">rozšíření `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="47d51-267">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="47d51-268">`UseHttps` &ndash; nakonfigurujte Kestrel tak, aby používal HTTPS s výchozím certifikátem.</span><span class="sxs-lookup"><span data-stu-id="47d51-268">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="47d51-269">Vyvolá výjimku, pokud není nakonfigurován žádný výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="47d51-269">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="47d51-270">parametry `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="47d51-270">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="47d51-271">`filename` je cesta a název souboru certifikátu vzhledem k adresáři, který obsahuje soubory obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="47d51-271">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="47d51-272">`password` je heslo vyžadované pro přístup k datům certifikátu X. 509.</span><span class="sxs-lookup"><span data-stu-id="47d51-272">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="47d51-273">`configureOptions` je `Action` ke konfiguraci `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="47d51-273">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="47d51-274">Vrátí `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="47d51-274">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="47d51-275">`storeName` je úložiště certifikátů, ze kterého se má certifikát načíst.</span><span class="sxs-lookup"><span data-stu-id="47d51-275">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="47d51-276">`subject` je název subjektu certifikátu.</span><span class="sxs-lookup"><span data-stu-id="47d51-276">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="47d51-277">`allowInvalid` označuje, zda by měly být zváženy neplatné certifikáty, například certifikáty podepsané svým držitelem.</span><span class="sxs-lookup"><span data-stu-id="47d51-277">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="47d51-278">`location` je umístění úložiště, ze kterého se má načíst certifikát.</span><span class="sxs-lookup"><span data-stu-id="47d51-278">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="47d51-279">`serverCertificate` je certifikát X. 509.</span><span class="sxs-lookup"><span data-stu-id="47d51-279">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="47d51-280">V produkčním prostředí musí být HTTPS explicitně nakonfigurovaný.</span><span class="sxs-lookup"><span data-stu-id="47d51-280">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="47d51-281">Musí být zadán minimálně výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="47d51-281">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="47d51-282">Podporované konfigurace popsané dál:</span><span class="sxs-lookup"><span data-stu-id="47d51-282">Supported configurations described next:</span></span>

* <span data-ttu-id="47d51-283">Žádná konfigurace</span><span class="sxs-lookup"><span data-stu-id="47d51-283">No configuration</span></span>
* <span data-ttu-id="47d51-284">Nahradit výchozí certifikát z konfigurace</span><span class="sxs-lookup"><span data-stu-id="47d51-284">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="47d51-285">Změna výchozích hodnot v kódu</span><span class="sxs-lookup"><span data-stu-id="47d51-285">Change the defaults in code</span></span>

<span data-ttu-id="47d51-286">*Žádná konfigurace*</span><span class="sxs-lookup"><span data-stu-id="47d51-286">*No configuration*</span></span>

<span data-ttu-id="47d51-287">Kestrel naslouchá na `http://localhost:5000` a `https://localhost:5001` (Pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="47d51-287">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="47d51-288">*Nahradit výchozí certifikát z konfigurace*</span><span class="sxs-lookup"><span data-stu-id="47d51-288">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="47d51-289">`CreateDefaultBuilder` volá ve výchozím nastavení `Configure(context.Configuration.GetSection("Kestrel"))`, aby se načetla konfigurace Kestrel.</span><span class="sxs-lookup"><span data-stu-id="47d51-289">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="47d51-290">Pro Kestrel je k dispozici výchozí schéma konfigurace nastavení aplikace HTTPS.</span><span class="sxs-lookup"><span data-stu-id="47d51-290">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="47d51-291">Nakonfigurujte několik koncových bodů, včetně adres URL a certifikátů, které se mají použít, buď ze souboru na disku, nebo z úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="47d51-291">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="47d51-292">V následujícím příkladu *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="47d51-292">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="47d51-293">Nastavte **AllowInvalid** na `true`, pokud chcete povolit použití neplatných certifikátů (například certifikátů podepsaných svým držitelem).</span><span class="sxs-lookup"><span data-stu-id="47d51-293">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="47d51-294">Libovolný koncový bod HTTPS, který neurčuje certifikát (**HttpsDefaultCert** v následujícím příkladu), se vrátí k certifikátu definovanému v části **certifikáty** > **výchozí** nebo vývojový certifikát.</span><span class="sxs-lookup"><span data-stu-id="47d51-294">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="47d51-295">Alternativou k použití **cesty** a **hesla** pro libovolný uzel certifikátu je zadání certifikátu pomocí polí úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="47d51-295">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="47d51-296">Například **certifikáty** > **výchozí** certifikát lze zadat jako:</span><span class="sxs-lookup"><span data-stu-id="47d51-296">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="47d51-297">Poznámky ke schématu:</span><span class="sxs-lookup"><span data-stu-id="47d51-297">Schema notes:</span></span>

* <span data-ttu-id="47d51-298">V názvech koncových bodů se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="47d51-298">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="47d51-299">Například `HTTPS` a `Https` jsou platné.</span><span class="sxs-lookup"><span data-stu-id="47d51-299">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="47d51-300">U každého koncového bodu je vyžadován parametr `Url`.</span><span class="sxs-lookup"><span data-stu-id="47d51-300">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="47d51-301">Formát tohoto parametru je stejný jako konfigurační parametr `Urls` nejvyšší úrovně s tím rozdílem, že je omezen na jedinou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="47d51-301">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="47d51-302">Tyto koncové body nahrazují hodnoty definované v konfiguraci `Urls` na nejvyšší úrovni místo jejich přidávání do těchto koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="47d51-302">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="47d51-303">Koncové body definované v kódu prostřednictvím `Listen` jsou kumulativní s koncovými body definovanými v konfiguračním oddílu.</span><span class="sxs-lookup"><span data-stu-id="47d51-303">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="47d51-304">Oddíl `Certificate` je nepovinný.</span><span class="sxs-lookup"><span data-stu-id="47d51-304">The `Certificate` section is optional.</span></span> <span data-ttu-id="47d51-305">Pokud se nezadá oddíl `Certificate`, použijí se výchozí hodnoty definované v dřívějších scénářích.</span><span class="sxs-lookup"><span data-stu-id="47d51-305">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="47d51-306">Pokud nejsou k dispozici žádné výchozí hodnoty, Server vyvolá výjimku a nemůže se spustit.</span><span class="sxs-lookup"><span data-stu-id="47d51-306">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="47d51-307">Oddíl `Certificate` podporuje jak **cesty**&ndash;**hesla** , tak&ndash;ho **úložiště** **subjektu** .</span><span class="sxs-lookup"><span data-stu-id="47d51-307">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="47d51-308">V takovém případě může být definován libovolný počet koncových bodů, pokud to nezpůsobí konflikty portů.</span><span class="sxs-lookup"><span data-stu-id="47d51-308">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="47d51-309">`options.Configure(context.Configuration.GetSection("{SECTION}"))` vrátí `KestrelConfigurationLoader` s metodou `.Endpoint(string name, listenOptions => { })`, která se dá použít k doplnění nastavení nakonfigurovaného koncového bodu:</span><span class="sxs-lookup"><span data-stu-id="47d51-309">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="47d51-310">`KestrelServerOptions.ConfigurationLoader` se dá získat přímo k pokračování v iteraci u stávajícího zavaděče, jako je třeba ten, který poskytuje <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="47d51-310">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="47d51-311">Konfigurační oddíl pro každý koncový bod je k dispozici na možnostech v metodě `Endpoint`, aby bylo možné číst vlastní nastavení.</span><span class="sxs-lookup"><span data-stu-id="47d51-311">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="47d51-312">Více konfigurací může být načteno voláním `options.Configure(context.Configuration.GetSection("{SECTION}"))` znovu s jiným oddílem.</span><span class="sxs-lookup"><span data-stu-id="47d51-312">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="47d51-313">Je použita pouze poslední konfigurace, pokud není metoda `Load` explicitně volána pro předchozí instance.</span><span class="sxs-lookup"><span data-stu-id="47d51-313">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="47d51-314">Metapackage nevolá `Load`, takže jeho výchozí konfigurační oddíl může být nahrazen.</span><span class="sxs-lookup"><span data-stu-id="47d51-314">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="47d51-315">`KestrelConfigurationLoader` zrcadlí `Listen` rodinu rozhraní API od `KestrelServerOptions` jako přetížení `Endpoint`, takže koncové body kódu a konfigurace lze konfigurovat na stejném místě.</span><span class="sxs-lookup"><span data-stu-id="47d51-315">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="47d51-316">Tato přetížení nepoužívají názvy a využívají pouze výchozí nastavení z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="47d51-316">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="47d51-317">*Změna výchozích hodnot v kódu*</span><span class="sxs-lookup"><span data-stu-id="47d51-317">*Change the defaults in code*</span></span>

<span data-ttu-id="47d51-318">`ConfigureEndpointDefaults` a `ConfigureHttpsDefaults` lze použít ke změně výchozího nastavení pro `ListenOptions` a `HttpsConnectionAdapterOptions`, včetně přepsání výchozího certifikátu zadaného v předchozím scénáři.</span><span class="sxs-lookup"><span data-stu-id="47d51-318">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="47d51-319">`ConfigureEndpointDefaults` a `ConfigureHttpsDefaults` by měly být volány před konfigurací koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="47d51-319">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="47d51-320">*Podpora Kestrel pro SNI*</span><span class="sxs-lookup"><span data-stu-id="47d51-320">*Kestrel support for SNI*</span></span>

<span data-ttu-id="47d51-321">[Indikace názvu serveru (SNI)](https://tools.ietf.org/html/rfc6066#section-3) lze použít k hostování více domén na stejné IP adrese a portu.</span><span class="sxs-lookup"><span data-stu-id="47d51-321">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="47d51-322">Aby SNI fungovalo, klient pošle název hostitele pro zabezpečenou relaci serveru během metody handshake TLS, aby server mohl poskytovat správný certifikát.</span><span class="sxs-lookup"><span data-stu-id="47d51-322">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="47d51-323">Klient používá poskytnutý certifikát pro šifrovanou komunikaci se serverem během zabezpečené relace, která následuje za metodou handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="47d51-323">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="47d51-324">Kestrel podporuje SNI prostřednictvím zpětného volání `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="47d51-324">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="47d51-325">Zpětné volání je vyvoláno jednou za připojení, které aplikaci umožní zkontrolovat název hostitele a vybrat příslušný certifikát.</span><span class="sxs-lookup"><span data-stu-id="47d51-325">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="47d51-326">Podpora SNI vyžaduje:</span><span class="sxs-lookup"><span data-stu-id="47d51-326">SNI support requires:</span></span>

* <span data-ttu-id="47d51-327">Spuštění v cílovém rozhraní `netcoreapp2.1` nebo novější.</span><span class="sxs-lookup"><span data-stu-id="47d51-327">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="47d51-328">V `net461` nebo novějším je zpětné volání vyvoláno, ale `name` je vždy `null`.</span><span class="sxs-lookup"><span data-stu-id="47d51-328">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="47d51-329">`name` taky `null`, pokud klient neposkytne parametr názvu hostitele v handshaki TLS.</span><span class="sxs-lookup"><span data-stu-id="47d51-329">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="47d51-330">Všechny weby běží na stejné instanci Kestrel.</span><span class="sxs-lookup"><span data-stu-id="47d51-330">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="47d51-331">Kestrel nepodporuje sdílení IP adresy a portu napříč několika instancemi bez reverzního proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="47d51-331">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="47d51-332">Protokolování připojení</span><span class="sxs-lookup"><span data-stu-id="47d51-332">Connection logging</span></span>

<span data-ttu-id="47d51-333">Volání <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> k vygenerování protokolů na úrovni ladění pro komunikaci na úrovni bajtů v rámci připojení.</span><span class="sxs-lookup"><span data-stu-id="47d51-333">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="47d51-334">Protokolování připojení je užitečné při řešení problémů v komunikaci nízké úrovně, například během šifrování TLS a za proxy servery.</span><span class="sxs-lookup"><span data-stu-id="47d51-334">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="47d51-335">Pokud je `UseConnectionLogging` před `UseHttps`, zaprotokoluje se zašifrovaný provoz.</span><span class="sxs-lookup"><span data-stu-id="47d51-335">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="47d51-336">Je-li po `UseHttps`umístěn `UseConnectionLogging`, zašifrovaný provoz se zaznamená do protokolu.</span><span class="sxs-lookup"><span data-stu-id="47d51-336">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="47d51-337">Vytvoření vazby na soket TCP</span><span class="sxs-lookup"><span data-stu-id="47d51-337">Bind to a TCP socket</span></span>

<span data-ttu-id="47d51-338">Metoda <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> se váže k soketu TCP a obslužná rutina options umožňuje konfiguraci certifikátu X. 509:</span><span class="sxs-lookup"><span data-stu-id="47d51-338">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="47d51-339">Příklad konfiguruje HTTPS pro koncový bod s <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="47d51-339">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="47d51-340">Ke konfiguraci dalších nastavení Kestrel pro konkrétní koncové body použijte stejné rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="47d51-340">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="47d51-341">Vytvoření vazby na soket UNIX</span><span class="sxs-lookup"><span data-stu-id="47d51-341">Bind to a Unix socket</span></span>

<span data-ttu-id="47d51-342">Naslouchat na soketu se systémem UNIX pomocí <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> pro zlepšení výkonu pomocí Nginx, jak je znázorněno v tomto příkladu:</span><span class="sxs-lookup"><span data-stu-id="47d51-342">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

### <a name="port-0"></a><span data-ttu-id="47d51-343">Port 0</span><span class="sxs-lookup"><span data-stu-id="47d51-343">Port 0</span></span>

<span data-ttu-id="47d51-344">Když je zadané číslo portu `0`, dynamicky se váže k dostupnému portu.</span><span class="sxs-lookup"><span data-stu-id="47d51-344">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="47d51-345">Následující příklad ukazuje, jak určit, který port Kestrel je skutečně vázaný za běhu:</span><span class="sxs-lookup"><span data-stu-id="47d51-345">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="47d51-346">Když se aplikace spustí, výstup okna konzoly indikuje dynamický port, na který se aplikace dá získat:</span><span class="sxs-lookup"><span data-stu-id="47d51-346">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="47d51-347">Omezení</span><span class="sxs-lookup"><span data-stu-id="47d51-347">Limitations</span></span>

<span data-ttu-id="47d51-348">Konfigurace koncových bodů pomocí následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="47d51-348">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="47d51-349">argument příkazového řádku `--urls`</span><span class="sxs-lookup"><span data-stu-id="47d51-349">`--urls` command-line argument</span></span>
* <span data-ttu-id="47d51-350">konfigurační klíč hostitele `urls`</span><span class="sxs-lookup"><span data-stu-id="47d51-350">`urls` host configuration key</span></span>
* <span data-ttu-id="47d51-351">`ASPNETCORE_URLS` proměnná prostředí</span><span class="sxs-lookup"><span data-stu-id="47d51-351">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="47d51-352">Tyto metody jsou užitečné, pokud chcete, aby kód pracoval na jiných serverech než Kestrel.</span><span class="sxs-lookup"><span data-stu-id="47d51-352">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="47d51-353">Mějte ale na paměti následující omezení:</span><span class="sxs-lookup"><span data-stu-id="47d51-353">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="47d51-354">Protokol HTTPS nelze s těmito přístupy použít, pokud není k dispozici výchozí certifikát v konfiguraci koncového bodu HTTPS (například pomocí konfigurace `KestrelServerOptions` nebo konfiguračního souboru, jak je uvedeno výše v tomto tématu).</span><span class="sxs-lookup"><span data-stu-id="47d51-354">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="47d51-355">Při současném použití `Listen` i `UseUrls` jsou koncové body `Listen` popsány v koncových bodech `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="47d51-355">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="47d51-356">Konfigurace koncového bodu služby IIS</span><span class="sxs-lookup"><span data-stu-id="47d51-356">IIS endpoint configuration</span></span>

<span data-ttu-id="47d51-357">Při použití služby IIS jsou vazby adresy URL pro přepsání vazby služby IIS nastaveny buď pomocí `Listen`, nebo `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="47d51-357">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="47d51-358">Další informace najdete v tématu [ASP.NET Core modulu](xref:host-and-deploy/aspnet-core-module) .</span><span class="sxs-lookup"><span data-stu-id="47d51-358">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="47d51-359">ListenOptions. Protocols</span><span class="sxs-lookup"><span data-stu-id="47d51-359">ListenOptions.Protocols</span></span>

<span data-ttu-id="47d51-360">Vlastnost `Protocols` vytváří protokoly HTTP (`HttpProtocols`) povolené na koncovém bodu připojení nebo pro server.</span><span class="sxs-lookup"><span data-stu-id="47d51-360">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="47d51-361">Přiřaďte hodnotu vlastnosti `Protocols` ze výčtu `HttpProtocols`.</span><span class="sxs-lookup"><span data-stu-id="47d51-361">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="47d51-362">hodnota výčtu `HttpProtocols`</span><span class="sxs-lookup"><span data-stu-id="47d51-362">`HttpProtocols` enum value</span></span> | <span data-ttu-id="47d51-363">Povolený protokol připojení</span><span class="sxs-lookup"><span data-stu-id="47d51-363">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="47d51-364">Pouze HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="47d51-364">HTTP/1.1 only.</span></span> <span data-ttu-id="47d51-365">Dá se použít s TLS nebo bez něj.</span><span class="sxs-lookup"><span data-stu-id="47d51-365">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="47d51-366">Pouze HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="47d51-366">HTTP/2 only.</span></span> <span data-ttu-id="47d51-367">Dá se použít bez TLS jenom v případě, že klient podporuje [předchozí režim znalostní báze](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="47d51-367">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="47d51-368">HTTP/1.1 a HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="47d51-368">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="47d51-369">HTTP/2 vyžaduje, aby klient v rámci metody handshake protokolu TLS [(ALPN)](https://tools.ietf.org/html/rfc7301#section-3) vybral http/2. v opačném případě se připojení nastaví jako výchozí HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="47d51-369">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="47d51-370">Výchozí hodnota `ListenOptions.Protocols` pro libovolný koncový bod je `HttpProtocols.Http1AndHttp2`.</span><span class="sxs-lookup"><span data-stu-id="47d51-370">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="47d51-371">Omezení TLS pro HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="47d51-371">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="47d51-372">TLS verze 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="47d51-372">TLS version 1.2 or later</span></span>
* <span data-ttu-id="47d51-373">Opětovné vyjednávání je zakázané.</span><span class="sxs-lookup"><span data-stu-id="47d51-373">Renegotiation disabled</span></span>
* <span data-ttu-id="47d51-374">Komprese vypnuta</span><span class="sxs-lookup"><span data-stu-id="47d51-374">Compression disabled</span></span>
* <span data-ttu-id="47d51-375">Minimální velikosti dočasného výměny klíčů:</span><span class="sxs-lookup"><span data-stu-id="47d51-375">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="47d51-376">Eliptická křivka Diffie-Hellman (ECDH) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bity minima</span><span class="sxs-lookup"><span data-stu-id="47d51-376">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum</span></span>
  * <span data-ttu-id="47d51-377">Omezené pole Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bity minima</span><span class="sxs-lookup"><span data-stu-id="47d51-377">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bits minimum</span></span>
* <span data-ttu-id="47d51-378">Šifrovací sada není zakázaná.</span><span class="sxs-lookup"><span data-stu-id="47d51-378">Cipher suite not blacklisted</span></span>

<span data-ttu-id="47d51-379">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; s eliptickou křivkou P-256 &lbrack;`FIPS186`&rbrack; je ve výchozím nastavení podporován.</span><span class="sxs-lookup"><span data-stu-id="47d51-379">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="47d51-380">Následující příklad povoluje připojení HTTP/1.1 a HTTP/2 na portu 8000.</span><span class="sxs-lookup"><span data-stu-id="47d51-380">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="47d51-381">Připojení TLS zabezpečuje pomocí zadaného certifikátu:</span><span class="sxs-lookup"><span data-stu-id="47d51-381">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="47d51-382">Pomocí middleware připojení můžete v případě potřeby filtrovat handshake na základě jednotlivých připojení pro konkrétní šifry.</span><span class="sxs-lookup"><span data-stu-id="47d51-382">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="47d51-383">Následující příklad vyvolá <xref:System.NotSupportedException> pro jakýkoli šifrovací algoritmus, který aplikace nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="47d51-383">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="47d51-384">Případně můžete definovat a porovnat [ITlsHandshakeFeature. CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) se seznamem přijatelných šifrovacích sad.</span><span class="sxs-lookup"><span data-stu-id="47d51-384">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="47d51-385">Pro šifrovací algoritmus [CipherAlgorithmType. null](xref:System.Security.Authentication.CipherAlgorithmType) se nepoužívá žádné šifrování.</span><span class="sxs-lookup"><span data-stu-id="47d51-385">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

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

<span data-ttu-id="47d51-386">Filtrování připojení lze také nakonfigurovat prostřednictvím lambda <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder>:</span><span class="sxs-lookup"><span data-stu-id="47d51-386">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

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

<span data-ttu-id="47d51-387">V systému Linux lze pomocí <xref:System.Net.Security.CipherSuitesPolicy> filtrovat handshake TLS na jednotlivých připojeních:</span><span class="sxs-lookup"><span data-stu-id="47d51-387">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

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

<span data-ttu-id="47d51-388">*Nastavit protokol z konfigurace*</span><span class="sxs-lookup"><span data-stu-id="47d51-388">*Set the protocol from configuration*</span></span>

<span data-ttu-id="47d51-389">`CreateDefaultBuilder` volá ve výchozím nastavení `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))`, aby se načetla konfigurace Kestrel.</span><span class="sxs-lookup"><span data-stu-id="47d51-389">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="47d51-390">Následující příklad *appSettings. JSON* vytvoří HTTP/1.1 jako výchozí protokol připojení pro všechny koncové body:</span><span class="sxs-lookup"><span data-stu-id="47d51-390">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="47d51-391">Následující příklad *appSettings. JSON* vytvoří protokol připojení HTTP/1.1 pro konkrétní koncový bod:</span><span class="sxs-lookup"><span data-stu-id="47d51-391">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="47d51-392">Protokoly zadané v hodnotách přepisu kódu nastavených podle konfigurace</span><span class="sxs-lookup"><span data-stu-id="47d51-392">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="47d51-393">Konfigurace přenosu</span><span class="sxs-lookup"><span data-stu-id="47d51-393">Transport configuration</span></span>

<span data-ttu-id="47d51-394">Pro projekty, které vyžadují použití Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span><span class="sxs-lookup"><span data-stu-id="47d51-394">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span></span>

* <span data-ttu-id="47d51-395">Přidejte do souboru projektu aplikace závislost pro balíček [Microsoft. AspNetCore. Server. Kestrel. Transport. Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) :</span><span class="sxs-lookup"><span data-stu-id="47d51-395">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* <span data-ttu-id="47d51-396">Na `IWebHostBuilder` volejte <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span><span class="sxs-lookup"><span data-stu-id="47d51-396">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> on the `IWebHostBuilder`:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="47d51-397">Předpony adresy URL</span><span class="sxs-lookup"><span data-stu-id="47d51-397">URL prefixes</span></span>

<span data-ttu-id="47d51-398">Při použití `UseUrls`, `--urls` argumentu příkazového řádku, `urls` konfigurační klíč hostitele nebo proměnná prostředí `ASPNETCORE_URLS`, můžou být předpony adresy URL v některém z následujících formátů.</span><span class="sxs-lookup"><span data-stu-id="47d51-398">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="47d51-399">Platné jsou pouze předpony adresy URL protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="47d51-399">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="47d51-400">Kestrel nepodporuje HTTPS při konfiguraci vazeb URL pomocí `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="47d51-400">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="47d51-401">Adresa IPv4 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="47d51-401">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="47d51-402">`0.0.0.0` je zvláštní případ, který se váže ke všem adresám IPv4.</span><span class="sxs-lookup"><span data-stu-id="47d51-402">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="47d51-403">Adresa IPv6 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="47d51-403">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="47d51-404">`[::]` je ekvivalent protokolu IPv4 `0.0.0.0`protokolu IPv4.</span><span class="sxs-lookup"><span data-stu-id="47d51-404">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="47d51-405">Název hostitele s číslem portu</span><span class="sxs-lookup"><span data-stu-id="47d51-405">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="47d51-406">Názvy hostitelů `*` a `+` nejsou speciální.</span><span class="sxs-lookup"><span data-stu-id="47d51-406">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="47d51-407">Cokoli se nerozpoznalo jako platná IP adresa nebo `localhost` váže ke všem IP adresám IPv4 a IPv6.</span><span class="sxs-lookup"><span data-stu-id="47d51-407">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="47d51-408">Pokud chcete navazovat různé názvy hostitelů na různé ASP.NET Core aplikace na stejném portu, použijte [http. sys](xref:fundamentals/servers/httpsys) nebo reverzní proxy server, třeba IIS, Nginx nebo Apache.</span><span class="sxs-lookup"><span data-stu-id="47d51-408">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="47d51-409">Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="47d51-409">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="47d51-410">Název hostitele `localhost` s číslem portu nebo IP adresou zpětné smyčky s číslem portu</span><span class="sxs-lookup"><span data-stu-id="47d51-410">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="47d51-411">Pokud je zadána hodnota `localhost`, pokusí se Kestrel vytvořit propojení s rozhraními zpětné smyčky IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="47d51-411">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="47d51-412">Pokud je požadovaný port používán jinou službou na rozhraní zpětné smyčky, spuštění Kestrel se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="47d51-412">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="47d51-413">Pokud je rozhraní zpětné smyčky v žádném jiném případě nedostupné (většinou kvůli tomu, že protokol IPv6 není podporovaný), Kestrel zaznamená upozornění.</span><span class="sxs-lookup"><span data-stu-id="47d51-413">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="47d51-414">Filtrování hostitele</span><span class="sxs-lookup"><span data-stu-id="47d51-414">Host filtering</span></span>

<span data-ttu-id="47d51-415">I když Kestrel podporuje konfiguraci na základě prefixů, jako je například `http://example.com:5000`, Kestrel převážně ignoruje název hostitele.</span><span class="sxs-lookup"><span data-stu-id="47d51-415">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="47d51-416">Hostitelská `localhost` je zvláštní případ, který se používá pro vazbu na adresy zpětné smyčky.</span><span class="sxs-lookup"><span data-stu-id="47d51-416">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="47d51-417">Každý hostitel, který je jiný než explicitní IP adresa, se váže na všechny veřejné IP adresy.</span><span class="sxs-lookup"><span data-stu-id="47d51-417">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="47d51-418">hlavičky `Host` nejsou ověřeny.</span><span class="sxs-lookup"><span data-stu-id="47d51-418">`Host` headers aren't validated.</span></span>

<span data-ttu-id="47d51-419">Jako alternativní řešení použijte middleware pro filtrování hostitele.</span><span class="sxs-lookup"><span data-stu-id="47d51-419">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="47d51-420">Middleware pro filtrování hostitele poskytuje balíček [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , který je implicitně k dispozici pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="47d51-420">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="47d51-421">Middleware se přidá pomocí <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, která volá <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="47d51-421">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="47d51-422">Middleware pro filtrování hostitele je ve výchozím nastavení zakázáno.</span><span class="sxs-lookup"><span data-stu-id="47d51-422">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="47d51-423">Chcete-li povolit middleware, definujte `AllowedHosts` klíč v souboru *appSettings. json*/*appSettings.\<prostředí >. JSON*.</span><span class="sxs-lookup"><span data-stu-id="47d51-423">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="47d51-424">Hodnota je seznam názvů hostitelů oddělených středníkem bez čísel portů:</span><span class="sxs-lookup"><span data-stu-id="47d51-424">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="47d51-425">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="47d51-425">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="47d51-426">[Middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer) má taky možnost <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="47d51-426">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="47d51-427">Middleware předávaných hlaviček a middleware pro filtrování hostitele mají podobné funkce pro různé scénáře.</span><span class="sxs-lookup"><span data-stu-id="47d51-427">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="47d51-428">Nastavení `AllowedHosts` s předaným middlewarem hlaviček je vhodné, když se hlavička `Host` nezachová při předávání požadavků pomocí reverzního proxy server nebo nástroje pro vyrovnávání zatížení.</span><span class="sxs-lookup"><span data-stu-id="47d51-428">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="47d51-429">Nastavení `AllowedHosts` pomocí middlewaru pro filtrování hostitele je vhodné, pokud se Kestrel používá jako veřejný hraniční Server nebo když je hlavička `Host` přímo předána.</span><span class="sxs-lookup"><span data-stu-id="47d51-429">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="47d51-430">Další informace o middlewaru předávaných hlaviček najdete v tématu <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="47d51-430">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="47d51-431">Kestrel je [webový server pro různé platformy pro ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="47d51-431">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="47d51-432">Kestrel je webový server, který je ve výchozím nastavení součástí ASP.NET Core šablon projektů.</span><span class="sxs-lookup"><span data-stu-id="47d51-432">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="47d51-433">Kestrel podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="47d51-433">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="47d51-434">HTTPS</span><span class="sxs-lookup"><span data-stu-id="47d51-434">HTTPS</span></span>
* <span data-ttu-id="47d51-435">Neprůhledný upgrade používaný k povolení [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="47d51-435">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="47d51-436">Technologie UNIX Sockets s vysokým výkonem za Nginx</span><span class="sxs-lookup"><span data-stu-id="47d51-436">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="47d51-437">HTTP/2 (kromě macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="47d51-437">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="47d51-438">&dagger;HTTP/2 bude v budoucí verzi podporován v macOS.</span><span class="sxs-lookup"><span data-stu-id="47d51-438">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="47d51-439">Kestrel se podporuje na všech platformách a verzích, které podporuje .NET Core.</span><span class="sxs-lookup"><span data-stu-id="47d51-439">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="47d51-440">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="47d51-440">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="47d51-441">Podpora HTTP/2</span><span class="sxs-lookup"><span data-stu-id="47d51-441">HTTP/2 support</span></span>

<span data-ttu-id="47d51-442">[Http/2](https://httpwg.org/specs/rfc7540.html) je k dispozici pro ASP.NET Core aplikace, pokud jsou splněné následující základní požadavky:</span><span class="sxs-lookup"><span data-stu-id="47d51-442">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="47d51-443">&dagger; operačního systému</span><span class="sxs-lookup"><span data-stu-id="47d51-443">Operating system&dagger;</span></span>
  * <span data-ttu-id="47d51-444">Windows Server 2016/Windows 10 nebo novější&Dagger;</span><span class="sxs-lookup"><span data-stu-id="47d51-444">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="47d51-445">Linux s OpenSSL 1.0.2 nebo novějším (například Ubuntu 16,04 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="47d51-445">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="47d51-446">Cílová architektura: .NET Core 2,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="47d51-446">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="47d51-447">Připojení [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="47d51-447">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="47d51-448">Připojení TLS 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="47d51-448">TLS 1.2 or later connection</span></span>

<span data-ttu-id="47d51-449">&dagger;HTTP/2 bude v budoucí verzi podporován v macOS.</span><span class="sxs-lookup"><span data-stu-id="47d51-449">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="47d51-450">&Dagger;Kestrel má omezená podpora HTTP/2 na Windows Serveru 2012 R2 a Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="47d51-450">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="47d51-451">Podpora je omezená, protože seznam podporovaných šifrovacích sad TLS, které jsou k dispozici v těchto operačních systémech, je omezený.</span><span class="sxs-lookup"><span data-stu-id="47d51-451">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="47d51-452">Pro zabezpečení připojení TLS může být vyžadován certifikát vygenerovaný pomocí algoritmu ECDSA (s připojením typu eliptická křivka).</span><span class="sxs-lookup"><span data-stu-id="47d51-452">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="47d51-453">Pokud je navázáno připojení HTTP/2, sestavy [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/2`.</span><span class="sxs-lookup"><span data-stu-id="47d51-453">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="47d51-454">HTTP/2 je ve výchozím nastavení zakázané.</span><span class="sxs-lookup"><span data-stu-id="47d51-454">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="47d51-455">Další informace o konfiguraci najdete v částech [Možnosti Kestrel](#kestrel-options) a [ListenOptions. Protocols](#listenoptionsprotocols) .</span><span class="sxs-lookup"><span data-stu-id="47d51-455">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="47d51-456">Kdy použít Kestrel s reverzním proxy serverem</span><span class="sxs-lookup"><span data-stu-id="47d51-456">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="47d51-457">Kestrel může používat sám sebe nebo s *reverzními proxy server*, jako je [Internetová informační služba (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="47d51-457">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="47d51-458">Reverzní proxy server přijímá požadavky HTTP ze sítě a přesměruje je na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="47d51-458">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="47d51-459">Kestrel použitý jako hraniční webový server (internetový):</span><span class="sxs-lookup"><span data-stu-id="47d51-459">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel komunikuje přímo s internetem bez zpětného proxy server](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="47d51-461">Kestrel se používá v konfiguraci reverzního proxy serveru:</span><span class="sxs-lookup"><span data-stu-id="47d51-461">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel nepřímo komunikuje s internetem prostřednictvím reverzní proxy server, jako je IIS, Nginx nebo Apache.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="47d51-463">Buď konfigurace, s zpětným proxy server nebo bez něj, je podporovaná konfigurace hostování.</span><span class="sxs-lookup"><span data-stu-id="47d51-463">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="47d51-464">Kestrel používané jako hraniční Server bez reverzních proxy server nepodporuje sdílení stejné IP adresy a portu mezi více procesy.</span><span class="sxs-lookup"><span data-stu-id="47d51-464">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="47d51-465">Když je Kestrel nakonfigurovaný k naslouchání na portu, Kestrel zpracovává veškerý provoz tohoto portu bez ohledu na hlavičky `Host`.</span><span class="sxs-lookup"><span data-stu-id="47d51-465">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="47d51-466">Reverzní proxy server, který může sdílet porty, má schopnost přesměrovat požadavky na Kestrel na jedinečné IP adresy a porty.</span><span class="sxs-lookup"><span data-stu-id="47d51-466">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="47d51-467">I v případě, že se zpětný proxy server nepožaduje, může být použití reverzní proxy server vhodnou volbou.</span><span class="sxs-lookup"><span data-stu-id="47d51-467">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="47d51-468">Reverzní proxy:</span><span class="sxs-lookup"><span data-stu-id="47d51-468">A reverse proxy:</span></span>

* <span data-ttu-id="47d51-469">Může omezit vystavenou veřejnou plochu v aplikacích, které hostuje.</span><span class="sxs-lookup"><span data-stu-id="47d51-469">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="47d51-470">Poskytněte další vrstvu konfigurace a obrany.</span><span class="sxs-lookup"><span data-stu-id="47d51-470">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="47d51-471">Může se lépe integrovat se stávající infrastrukturou.</span><span class="sxs-lookup"><span data-stu-id="47d51-471">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="47d51-472">Zjednodušte vyrovnávání zatížení a konfiguraci zabezpečené komunikace (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="47d51-472">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="47d51-473">Pouze reverzní proxy server vyžaduje certifikát X. 509 a tento server může komunikovat se servery aplikace v interní síti pomocí prostého protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="47d51-473">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="47d51-474">Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="47d51-474">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="47d51-475">Použití Kestrel v aplikacích ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="47d51-475">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="47d51-476">Balíček [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) je součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="47d51-476">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="47d51-477">ASP.NET Core šablony projektu ve výchozím nastavení používají Kestrel.</span><span class="sxs-lookup"><span data-stu-id="47d51-477">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="47d51-478">V *program.cs*kód šablony volá <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, která volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> na pozadí.</span><span class="sxs-lookup"><span data-stu-id="47d51-478">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="47d51-479">Další informace o `CreateDefaultBuilder` a vytváření hostitele najdete v části *Nastavení hostitele* v <xref:fundamentals/host/web-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="47d51-479">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="47d51-480">Pokud chcete po volání `CreateDefaultBuilder` zadat další konfiguraci, použijte `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="47d51-480">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="47d51-481">Pokud aplikace nevolá `CreateDefaultBuilder` pro nastavení hostitele, **před** voláním `ConfigureKestrel` volejte <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span><span class="sxs-lookup"><span data-stu-id="47d51-481">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="47d51-482">Kestrel možnosti</span><span class="sxs-lookup"><span data-stu-id="47d51-482">Kestrel options</span></span>

<span data-ttu-id="47d51-483">Webový server Kestrel má možnosti konfigurace omezení, které jsou zvláště užitečné v internetových nasazeních.</span><span class="sxs-lookup"><span data-stu-id="47d51-483">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="47d51-484">Nastavte omezení pro vlastnost <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> třídy <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="47d51-484">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="47d51-485">Vlastnost `Limits` obsahuje instanci třídy <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="47d51-485">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="47d51-486">V následujících příkladech se používá obor názvů <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:</span><span class="sxs-lookup"><span data-stu-id="47d51-486">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="47d51-487">Možnosti Kestrel, které jsou konfigurovány C# v kódu v následujících příkladech, lze také nastavit pomocí [poskytovatele konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="47d51-487">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="47d51-488">Poskytovatel konfigurace souboru může například načíst konfiguraci Kestrel z objektu *appSettings. JSON* nebo *appSettings. { Soubor Environment}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="47d51-488">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="47d51-489">Použijte **jeden** z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="47d51-489">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="47d51-490">Konfigurace Kestrel v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="47d51-490">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="47d51-491">Vloží instanci `IConfiguration` do třídy `Startup`.</span><span class="sxs-lookup"><span data-stu-id="47d51-491">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="47d51-492">Následující příklad předpokládá, že vložená konfigurace je přiřazena vlastnosti `Configuration`.</span><span class="sxs-lookup"><span data-stu-id="47d51-492">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="47d51-493">V `Startup.ConfigureServices` načtěte oddíl `Kestrel` konfigurace do konfigurace Kestrel.</span><span class="sxs-lookup"><span data-stu-id="47d51-493">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration.</span></span>

     ```csharp
     // using Microsoft.Extensions.Configuration

     public void ConfigureServices(IServiceCollection services)
     {
         services.Configure<KestrelServerOptions>(
             Configuration.GetSection("Kestrel"));
     }
     ```

* <span data-ttu-id="47d51-494">Nakonfigurovat Kestrel při sestavování hostitele:</span><span class="sxs-lookup"><span data-stu-id="47d51-494">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="47d51-495">V *program.cs*do konfigurace Kestrel načtěte oddíl `Kestrel` konfigurace:</span><span class="sxs-lookup"><span data-stu-id="47d51-495">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="47d51-496">Obě předchozí přístupy fungují u libovolného [poskytovatele konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="47d51-496">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="47d51-497">Časový limit Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="47d51-497">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="47d51-498">Získá nebo nastaví [časový limit zachování](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="47d51-498">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="47d51-499">Výchozí hodnota je 2 minuty.</span><span class="sxs-lookup"><span data-stu-id="47d51-499">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="47d51-500">Maximální počet připojení klientů</span><span class="sxs-lookup"><span data-stu-id="47d51-500">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="47d51-501">Maximální počet souběžných otevřených připojení TCP lze pro celou aplikaci nastavit pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="47d51-501">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="47d51-502">Existuje zvláštní omezení pro připojení, která byla upgradována z protokolu HTTP nebo HTTPS na jiný protokol (například na žádost WebSockets).</span><span class="sxs-lookup"><span data-stu-id="47d51-502">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="47d51-503">Po upgradu připojení se nepočítá s limitem `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="47d51-503">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="47d51-504">Ve výchozím nastavení je maximální počet připojení neomezené (null).</span><span class="sxs-lookup"><span data-stu-id="47d51-504">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="47d51-505">Maximální velikost textu požadavku</span><span class="sxs-lookup"><span data-stu-id="47d51-505">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="47d51-506">Výchozí maximální velikost textu požadavku je 30 000 000 bajtů, což je přibližně 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="47d51-506">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="47d51-507">Doporučený postup pro přepsání limitu v aplikaci ASP.NET Core MVC je použití atributu <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> u metody akce:</span><span class="sxs-lookup"><span data-stu-id="47d51-507">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="47d51-508">Tady je příklad, který ukazuje, jak nakonfigurovat omezení pro aplikaci na každém požadavku:</span><span class="sxs-lookup"><span data-stu-id="47d51-508">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="47d51-509">Přepsat nastavení u konkrétního požadavku v middleware:</span><span class="sxs-lookup"><span data-stu-id="47d51-509">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="47d51-510">Výjimka je vyvolána, pokud aplikace po zahájení aplikace nakonfiguruje limit na žádost.</span><span class="sxs-lookup"><span data-stu-id="47d51-510">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="47d51-511">Existuje vlastnost `IsReadOnly`, která určuje, zda je vlastnost `MaxRequestBodySize` ve stavu jen pro čtení, což znamená, že je příliš pozdě pro konfiguraci limitu.</span><span class="sxs-lookup"><span data-stu-id="47d51-511">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="47d51-512">Když je aplikace spouštěna [mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za [ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module), je omezení velikosti textu požadavku Kestrel zakázané, protože služba IIS tento limit již nastavuje.</span><span class="sxs-lookup"><span data-stu-id="47d51-512">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="47d51-513">Minimální rychlost těla zprávy požadavku</span><span class="sxs-lookup"><span data-stu-id="47d51-513">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="47d51-514">Kestrel kontroluje každou sekundu, pokud data přicházejí za zadanou rychlost v bajtech za sekundu.</span><span class="sxs-lookup"><span data-stu-id="47d51-514">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="47d51-515">Pokud frekvence klesne pod minimální hodnotu, vypršel časový limit připojení. Doba odkladu je doba, kterou Kestrel umožňuje klientovi zvýšit rychlost odesílání až na minimum; frekvence není během této doby kontrolována.</span><span class="sxs-lookup"><span data-stu-id="47d51-515">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="47d51-516">Doba odkladu pomáhá vyhnout se vyřazení připojení, která při počátečním posílání dat zpomalují rychlost, kvůli pomalému spuštění protokolu TCP.</span><span class="sxs-lookup"><span data-stu-id="47d51-516">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="47d51-517">Výchozí minimální sazba je 240 bajtů za sekundu s obdobím 5 sekund odkladu.</span><span class="sxs-lookup"><span data-stu-id="47d51-517">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="47d51-518">Pro odpověď platí také minimální sazba.</span><span class="sxs-lookup"><span data-stu-id="47d51-518">A minimum rate also applies to the response.</span></span> <span data-ttu-id="47d51-519">Kód pro nastavení limitu požadavků a limit odpovědi je stejný s výjimkou `RequestBody` nebo `Response` v názvech vlastností a rozhraní.</span><span class="sxs-lookup"><span data-stu-id="47d51-519">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="47d51-520">Tady je příklad, který ukazuje, jak nakonfigurovat minimální datové tarify v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="47d51-520">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="47d51-521">Přepsat minimální limity přenosové rychlosti na žádost v middlewaru:</span><span class="sxs-lookup"><span data-stu-id="47d51-521">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="47d51-522">V předchozí ukázce není k dispozici žádná míra – funkce `HttpContext.Features` pro požadavky HTTP/2, protože Změna omezení přenosové rychlosti u jednotlivých požadavků není pro protokol HTTP/2 podporovaná kvůli podpoře protokolu pro multiplexování požadavků.</span><span class="sxs-lookup"><span data-stu-id="47d51-522">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="47d51-523">Omezení přenosové rychlosti pro všechny servery nakonfigurovaná přes `KestrelServerOptions.Limits` se stále vztahují na připojení HTTP/1. x i HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="47d51-523">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="47d51-524">Časový limit hlaviček žádostí</span><span class="sxs-lookup"><span data-stu-id="47d51-524">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="47d51-525">Získá nebo nastaví maximální dobu, po kterou server stráví příjem hlaviček požadavků.</span><span class="sxs-lookup"><span data-stu-id="47d51-525">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="47d51-526">Výchozí hodnota je 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="47d51-526">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="47d51-527">Maximální počet proudů na připojení</span><span class="sxs-lookup"><span data-stu-id="47d51-527">Maximum streams per connection</span></span>

<span data-ttu-id="47d51-528">`Http2.MaxStreamsPerConnection` omezuje počet souběžných proudů požadavků na připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="47d51-528">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="47d51-529">Nadbytečné proudy jsou odmítnuty.</span><span class="sxs-lookup"><span data-stu-id="47d51-529">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="47d51-530">Výchozí hodnota je 100.</span><span class="sxs-lookup"><span data-stu-id="47d51-530">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="47d51-531">Velikost tabulky hlaviček</span><span class="sxs-lookup"><span data-stu-id="47d51-531">Header table size</span></span>

<span data-ttu-id="47d51-532">Dekodér HPACK dekomprimuje hlavičky HTTP pro připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="47d51-532">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="47d51-533">`Http2.HeaderTableSize` omezuje velikost kompresní tabulky hlaviček, kterou používá dekodér HPACK.</span><span class="sxs-lookup"><span data-stu-id="47d51-533">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="47d51-534">Hodnota je uvedena v oktetech a musí být větší než nula (0).</span><span class="sxs-lookup"><span data-stu-id="47d51-534">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="47d51-535">Výchozí hodnota je 4096.</span><span class="sxs-lookup"><span data-stu-id="47d51-535">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="47d51-536">Maximální velikost rámce</span><span class="sxs-lookup"><span data-stu-id="47d51-536">Maximum frame size</span></span>

<span data-ttu-id="47d51-537">`Http2.MaxFrameSize` označuje maximální velikost datové části rámce připojení HTTP/2, která se má přijmout.</span><span class="sxs-lookup"><span data-stu-id="47d51-537">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="47d51-538">Hodnota je uvedena v oktetech a musí být v rozmezí 2 ^ 14 (16 384) až 2 ^ 24-1 (16 777 215).</span><span class="sxs-lookup"><span data-stu-id="47d51-538">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="47d51-539">Výchozí hodnota je 2 ^ 14 (16 384).</span><span class="sxs-lookup"><span data-stu-id="47d51-539">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="47d51-540">Maximální velikost hlavičky požadavku</span><span class="sxs-lookup"><span data-stu-id="47d51-540">Maximum request header size</span></span>

<span data-ttu-id="47d51-541">`Http2.MaxRequestHeaderFieldSize` označuje maximální povolenou velikost v oktetech hodnot hlaviček požadavku.</span><span class="sxs-lookup"><span data-stu-id="47d51-541">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="47d51-542">Toto omezení platí pro názvy i hodnoty společně v komprimovaných a nekomprimovaných reprezentujcích.</span><span class="sxs-lookup"><span data-stu-id="47d51-542">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="47d51-543">Hodnota musí být větší než nula (0).</span><span class="sxs-lookup"><span data-stu-id="47d51-543">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="47d51-544">Výchozí hodnota je 8 192.</span><span class="sxs-lookup"><span data-stu-id="47d51-544">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="47d51-545">Velikost okna počátečního připojení</span><span class="sxs-lookup"><span data-stu-id="47d51-545">Initial connection window size</span></span>

<span data-ttu-id="47d51-546">`Http2.InitialConnectionWindowSize` označuje maximální velikost textu žádosti v bajtech. vyrovnávací paměti serveru se v jednom okamžiku agreguje napříč všemi požadavky (datovými proudy) na připojení.</span><span class="sxs-lookup"><span data-stu-id="47d51-546">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="47d51-547">Žádosti jsou také omezené pomocí `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="47d51-547">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="47d51-548">Hodnota musí být větší než nebo rovna 65 535 a menší než 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="47d51-548">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="47d51-549">Výchozí hodnota je 128 KB (131 072).</span><span class="sxs-lookup"><span data-stu-id="47d51-549">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="47d51-550">Počáteční velikost okna datového proudu</span><span class="sxs-lookup"><span data-stu-id="47d51-550">Initial stream window size</span></span>

<span data-ttu-id="47d51-551">`Http2.InitialStreamWindowSize` označuje maximální velikost textu požadavku v bajtech na jednu žádost (Stream) na jednom místě.</span><span class="sxs-lookup"><span data-stu-id="47d51-551">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="47d51-552">Žádosti jsou také omezené pomocí `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="47d51-552">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="47d51-553">Hodnota musí být větší než nebo rovna 65 535 a menší než 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="47d51-553">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="47d51-554">Výchozí hodnota je 96 KB (98 304).</span><span class="sxs-lookup"><span data-stu-id="47d51-554">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="47d51-555">Synchronní v/v</span><span class="sxs-lookup"><span data-stu-id="47d51-555">Synchronous IO</span></span>

<span data-ttu-id="47d51-556"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> řídí, zda je pro požadavek a odpověď povolena synchronní v/v.</span><span class="sxs-lookup"><span data-stu-id="47d51-556"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="47d51-557">Výchozí hodnota je `true`.</span><span class="sxs-lookup"><span data-stu-id="47d51-557">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="47d51-558">Velký počet blokování synchronních vstupně-výstupních operací může vést ke vyčerpání fondu vláken, což aplikaci nereaguje.</span><span class="sxs-lookup"><span data-stu-id="47d51-558">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="47d51-559">Při použití knihovny, která nepodporuje asynchronní vstupně-výstupní operace, povolte pouze `AllowSynchronousIO`.</span><span class="sxs-lookup"><span data-stu-id="47d51-559">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="47d51-560">Následující příklad povoluje synchronní vstupně-výstupní operace:</span><span class="sxs-lookup"><span data-stu-id="47d51-560">The following example enables synchronous IO:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="47d51-561">Informace o dalších možnostech a omezeních Kestrel najdete v tématech:</span><span class="sxs-lookup"><span data-stu-id="47d51-561">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="47d51-562">Konfigurace koncového bodu</span><span class="sxs-lookup"><span data-stu-id="47d51-562">Endpoint configuration</span></span>

<span data-ttu-id="47d51-563">Ve výchozím nastavení ASP.NET Core váže k:</span><span class="sxs-lookup"><span data-stu-id="47d51-563">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="47d51-564">`https://localhost:5001` (Pokud je k dispozici místní vývojový certifikát)</span><span class="sxs-lookup"><span data-stu-id="47d51-564">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="47d51-565">Zadejte adresy URL pomocí:</span><span class="sxs-lookup"><span data-stu-id="47d51-565">Specify URLs using the:</span></span>

* <span data-ttu-id="47d51-566">`ASPNETCORE_URLS` proměnnou prostředí.</span><span class="sxs-lookup"><span data-stu-id="47d51-566">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="47d51-567">argument příkazového řádku `--urls`</span><span class="sxs-lookup"><span data-stu-id="47d51-567">`--urls` command-line argument.</span></span>
* <span data-ttu-id="47d51-568">`urls` konfigurační klíč hostitele.</span><span class="sxs-lookup"><span data-stu-id="47d51-568">`urls` host configuration key.</span></span>
* <span data-ttu-id="47d51-569">Metoda rozšíření `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="47d51-569">`UseUrls` extension method.</span></span>

<span data-ttu-id="47d51-570">Hodnota zadaná pomocí těchto přístupů může být jeden nebo více koncových bodů HTTP a HTTPS (HTTPS, pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="47d51-570">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="47d51-571">Nakonfigurujte hodnotu jako seznam oddělený středníkem (například `"Urls": "http://localhost:8000; http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="47d51-571">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="47d51-572">Další informace o těchto přístupůch najdete v tématu [adresy URL serveru](xref:fundamentals/host/web-host#server-urls) a [potlačení konfigurace](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="47d51-572">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="47d51-573">Vytvoří se vývojový certifikát:</span><span class="sxs-lookup"><span data-stu-id="47d51-573">A development certificate is created:</span></span>

* <span data-ttu-id="47d51-574">Po instalaci [.NET Core SDK](/dotnet/core/sdk) .</span><span class="sxs-lookup"><span data-stu-id="47d51-574">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="47d51-575">[Nástroj pro vývoj a certifikáty](xref:aspnetcore-2.1#https) slouží k vytvoření certifikátu.</span><span class="sxs-lookup"><span data-stu-id="47d51-575">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="47d51-576">Některé prohlížeče vyžadují udělení explicitního oprávnění pro důvěřování místního vývojového certifikátu.</span><span class="sxs-lookup"><span data-stu-id="47d51-576">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="47d51-577">Šablony projektů konfigurují aplikace tak, aby běžely ve výchozím nastavení HTTPS a zahrnovaly [přesměrování https a podporu HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="47d51-577">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="47d51-578">Pokud chcete nakonfigurovat předpony a porty adres URL pro Kestrel, volejte <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> nebo <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> na <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="47d51-578">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="47d51-579">`UseUrls`, argument příkazového řádku `--urls`, `urls` konfigurační klíč hostitele a proměnná prostředí `ASPNETCORE_URLS` fungují, ale mají omezení zaznamenaná později v této části (pro konfiguraci koncového bodu HTTPS musí být k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="47d51-579">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="47d51-580">Konfigurace `KestrelServerOptions`:</span><span class="sxs-lookup"><span data-stu-id="47d51-580">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="47d51-581">ConfigureEndpointDefaults (Action\<ListenOptions >)</span><span class="sxs-lookup"><span data-stu-id="47d51-581">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="47d51-582">Určuje konfiguraci `Action`, která se má spustit pro každý zadaný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="47d51-582">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="47d51-583">Volání `ConfigureEndpointDefaults` několikrát nahrazuje předchozí `Action` s určeným posledním `Action`.</span><span class="sxs-lookup"><span data-stu-id="47d51-583">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="47d51-584">ConfigureHttpsDefaults (Action\<HttpsConnectionAdapterOptions >)</span><span class="sxs-lookup"><span data-stu-id="47d51-584">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="47d51-585">Určuje konfiguraci `Action` pro spuštění každého koncového bodu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="47d51-585">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="47d51-586">Volání `ConfigureHttpsDefaults` několikrát nahrazuje předchozí `Action` s určeným posledním `Action`.</span><span class="sxs-lookup"><span data-stu-id="47d51-586">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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

### <a name="configureiconfiguration"></a><span data-ttu-id="47d51-587">Konfigurace (IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="47d51-587">Configure(IConfiguration)</span></span>

<span data-ttu-id="47d51-588">Vytvoří zavaděč konfigurace pro nastavení Kestrel, který jako vstup převezme <xref:Microsoft.Extensions.Configuration.IConfiguration>.</span><span class="sxs-lookup"><span data-stu-id="47d51-588">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="47d51-589">Konfigurace musí být vymezená na konfigurační oddíl pro Kestrel.</span><span class="sxs-lookup"><span data-stu-id="47d51-589">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="47d51-590">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="47d51-590">ListenOptions.UseHttps</span></span>

<span data-ttu-id="47d51-591">Nakonfigurujte Kestrel tak, aby používal protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="47d51-591">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="47d51-592">rozšíření `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="47d51-592">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="47d51-593">`UseHttps` &ndash; nakonfigurujte Kestrel tak, aby používal HTTPS s výchozím certifikátem.</span><span class="sxs-lookup"><span data-stu-id="47d51-593">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="47d51-594">Vyvolá výjimku, pokud není nakonfigurován žádný výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="47d51-594">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="47d51-595">parametry `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="47d51-595">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="47d51-596">`filename` je cesta a název souboru certifikátu vzhledem k adresáři, který obsahuje soubory obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="47d51-596">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="47d51-597">`password` je heslo vyžadované pro přístup k datům certifikátu X. 509.</span><span class="sxs-lookup"><span data-stu-id="47d51-597">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="47d51-598">`configureOptions` je `Action` ke konfiguraci `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="47d51-598">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="47d51-599">Vrátí `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="47d51-599">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="47d51-600">`storeName` je úložiště certifikátů, ze kterého se má certifikát načíst.</span><span class="sxs-lookup"><span data-stu-id="47d51-600">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="47d51-601">`subject` je název subjektu certifikátu.</span><span class="sxs-lookup"><span data-stu-id="47d51-601">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="47d51-602">`allowInvalid` označuje, zda by měly být zváženy neplatné certifikáty, například certifikáty podepsané svým držitelem.</span><span class="sxs-lookup"><span data-stu-id="47d51-602">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="47d51-603">`location` je umístění úložiště, ze kterého se má načíst certifikát.</span><span class="sxs-lookup"><span data-stu-id="47d51-603">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="47d51-604">`serverCertificate` je certifikát X. 509.</span><span class="sxs-lookup"><span data-stu-id="47d51-604">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="47d51-605">V produkčním prostředí musí být HTTPS explicitně nakonfigurovaný.</span><span class="sxs-lookup"><span data-stu-id="47d51-605">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="47d51-606">Musí být zadán minimálně výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="47d51-606">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="47d51-607">Podporované konfigurace popsané dál:</span><span class="sxs-lookup"><span data-stu-id="47d51-607">Supported configurations described next:</span></span>

* <span data-ttu-id="47d51-608">Žádná konfigurace</span><span class="sxs-lookup"><span data-stu-id="47d51-608">No configuration</span></span>
* <span data-ttu-id="47d51-609">Nahradit výchozí certifikát z konfigurace</span><span class="sxs-lookup"><span data-stu-id="47d51-609">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="47d51-610">Změna výchozích hodnot v kódu</span><span class="sxs-lookup"><span data-stu-id="47d51-610">Change the defaults in code</span></span>

<span data-ttu-id="47d51-611">*Žádná konfigurace*</span><span class="sxs-lookup"><span data-stu-id="47d51-611">*No configuration*</span></span>

<span data-ttu-id="47d51-612">Kestrel naslouchá na `http://localhost:5000` a `https://localhost:5001` (Pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="47d51-612">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="47d51-613">*Nahradit výchozí certifikát z konfigurace*</span><span class="sxs-lookup"><span data-stu-id="47d51-613">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="47d51-614">`CreateDefaultBuilder` volá ve výchozím nastavení `Configure(context.Configuration.GetSection("Kestrel"))`, aby se načetla konfigurace Kestrel.</span><span class="sxs-lookup"><span data-stu-id="47d51-614">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="47d51-615">Pro Kestrel je k dispozici výchozí schéma konfigurace nastavení aplikace HTTPS.</span><span class="sxs-lookup"><span data-stu-id="47d51-615">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="47d51-616">Nakonfigurujte několik koncových bodů, včetně adres URL a certifikátů, které se mají použít, buď ze souboru na disku, nebo z úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="47d51-616">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="47d51-617">V následujícím příkladu *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="47d51-617">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="47d51-618">Nastavte **AllowInvalid** na `true`, pokud chcete povolit použití neplatných certifikátů (například certifikátů podepsaných svým držitelem).</span><span class="sxs-lookup"><span data-stu-id="47d51-618">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="47d51-619">Libovolný koncový bod HTTPS, který neurčuje certifikát (**HttpsDefaultCert** v následujícím příkladu), se vrátí k certifikátu definovanému v části **certifikáty** > **výchozí** nebo vývojový certifikát.</span><span class="sxs-lookup"><span data-stu-id="47d51-619">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="47d51-620">Alternativou k použití **cesty** a **hesla** pro libovolný uzel certifikátu je zadání certifikátu pomocí polí úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="47d51-620">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="47d51-621">Například **certifikáty** > **výchozí** certifikát lze zadat jako:</span><span class="sxs-lookup"><span data-stu-id="47d51-621">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="47d51-622">Poznámky ke schématu:</span><span class="sxs-lookup"><span data-stu-id="47d51-622">Schema notes:</span></span>

* <span data-ttu-id="47d51-623">V názvech koncových bodů se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="47d51-623">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="47d51-624">Například `HTTPS` a `Https` jsou platné.</span><span class="sxs-lookup"><span data-stu-id="47d51-624">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="47d51-625">U každého koncového bodu je vyžadován parametr `Url`.</span><span class="sxs-lookup"><span data-stu-id="47d51-625">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="47d51-626">Formát tohoto parametru je stejný jako konfigurační parametr `Urls` nejvyšší úrovně s tím rozdílem, že je omezen na jedinou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="47d51-626">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="47d51-627">Tyto koncové body nahrazují hodnoty definované v konfiguraci `Urls` na nejvyšší úrovni místo jejich přidávání do těchto koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="47d51-627">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="47d51-628">Koncové body definované v kódu prostřednictvím `Listen` jsou kumulativní s koncovými body definovanými v konfiguračním oddílu.</span><span class="sxs-lookup"><span data-stu-id="47d51-628">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="47d51-629">Oddíl `Certificate` je nepovinný.</span><span class="sxs-lookup"><span data-stu-id="47d51-629">The `Certificate` section is optional.</span></span> <span data-ttu-id="47d51-630">Pokud se nezadá oddíl `Certificate`, použijí se výchozí hodnoty definované v dřívějších scénářích.</span><span class="sxs-lookup"><span data-stu-id="47d51-630">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="47d51-631">Pokud nejsou k dispozici žádné výchozí hodnoty, Server vyvolá výjimku a nemůže se spustit.</span><span class="sxs-lookup"><span data-stu-id="47d51-631">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="47d51-632">Oddíl `Certificate` podporuje jak **cesty**&ndash;**hesla** , tak&ndash;ho **úložiště** **subjektu** .</span><span class="sxs-lookup"><span data-stu-id="47d51-632">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="47d51-633">V takovém případě může být definován libovolný počet koncových bodů, pokud to nezpůsobí konflikty portů.</span><span class="sxs-lookup"><span data-stu-id="47d51-633">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="47d51-634">`options.Configure(context.Configuration.GetSection("{SECTION}"))` vrátí `KestrelConfigurationLoader` s metodou `.Endpoint(string name, listenOptions => { })`, která se dá použít k doplnění nastavení nakonfigurovaného koncového bodu:</span><span class="sxs-lookup"><span data-stu-id="47d51-634">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="47d51-635">`KestrelServerOptions.ConfigurationLoader` se dá získat přímo k pokračování v iteraci u stávajícího zavaděče, jako je třeba ten, který poskytuje <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="47d51-635">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="47d51-636">Konfigurační oddíl pro každý koncový bod je k dispozici na možnostech v metodě `Endpoint`, aby bylo možné číst vlastní nastavení.</span><span class="sxs-lookup"><span data-stu-id="47d51-636">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="47d51-637">Více konfigurací může být načteno voláním `options.Configure(context.Configuration.GetSection("{SECTION}"))` znovu s jiným oddílem.</span><span class="sxs-lookup"><span data-stu-id="47d51-637">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="47d51-638">Je použita pouze poslední konfigurace, pokud není metoda `Load` explicitně volána pro předchozí instance.</span><span class="sxs-lookup"><span data-stu-id="47d51-638">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="47d51-639">Metapackage nevolá `Load`, takže jeho výchozí konfigurační oddíl může být nahrazen.</span><span class="sxs-lookup"><span data-stu-id="47d51-639">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="47d51-640">`KestrelConfigurationLoader` zrcadlí `Listen` rodinu rozhraní API od `KestrelServerOptions` jako přetížení `Endpoint`, takže koncové body kódu a konfigurace lze konfigurovat na stejném místě.</span><span class="sxs-lookup"><span data-stu-id="47d51-640">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="47d51-641">Tato přetížení nepoužívají názvy a využívají pouze výchozí nastavení z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="47d51-641">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="47d51-642">*Změna výchozích hodnot v kódu*</span><span class="sxs-lookup"><span data-stu-id="47d51-642">*Change the defaults in code*</span></span>

<span data-ttu-id="47d51-643">`ConfigureEndpointDefaults` a `ConfigureHttpsDefaults` lze použít ke změně výchozího nastavení pro `ListenOptions` a `HttpsConnectionAdapterOptions`, včetně přepsání výchozího certifikátu zadaného v předchozím scénáři.</span><span class="sxs-lookup"><span data-stu-id="47d51-643">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="47d51-644">`ConfigureEndpointDefaults` a `ConfigureHttpsDefaults` by měly být volány před konfigurací koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="47d51-644">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="47d51-645">*Podpora Kestrel pro SNI*</span><span class="sxs-lookup"><span data-stu-id="47d51-645">*Kestrel support for SNI*</span></span>

<span data-ttu-id="47d51-646">[Indikace názvu serveru (SNI)](https://tools.ietf.org/html/rfc6066#section-3) lze použít k hostování více domén na stejné IP adrese a portu.</span><span class="sxs-lookup"><span data-stu-id="47d51-646">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="47d51-647">Aby SNI fungovalo, klient pošle název hostitele pro zabezpečenou relaci serveru během metody handshake TLS, aby server mohl poskytovat správný certifikát.</span><span class="sxs-lookup"><span data-stu-id="47d51-647">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="47d51-648">Klient používá poskytnutý certifikát pro šifrovanou komunikaci se serverem během zabezpečené relace, která následuje za metodou handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="47d51-648">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="47d51-649">Kestrel podporuje SNI prostřednictvím zpětného volání `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="47d51-649">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="47d51-650">Zpětné volání je vyvoláno jednou za připojení, které aplikaci umožní zkontrolovat název hostitele a vybrat příslušný certifikát.</span><span class="sxs-lookup"><span data-stu-id="47d51-650">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="47d51-651">Podpora SNI vyžaduje:</span><span class="sxs-lookup"><span data-stu-id="47d51-651">SNI support requires:</span></span>

* <span data-ttu-id="47d51-652">Spuštění v cílovém rozhraní `netcoreapp2.1` nebo novější.</span><span class="sxs-lookup"><span data-stu-id="47d51-652">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="47d51-653">V `net461` nebo novějším je zpětné volání vyvoláno, ale `name` je vždy `null`.</span><span class="sxs-lookup"><span data-stu-id="47d51-653">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="47d51-654">`name` taky `null`, pokud klient neposkytne parametr názvu hostitele v handshaki TLS.</span><span class="sxs-lookup"><span data-stu-id="47d51-654">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="47d51-655">Všechny weby běží na stejné instanci Kestrel.</span><span class="sxs-lookup"><span data-stu-id="47d51-655">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="47d51-656">Kestrel nepodporuje sdílení IP adresy a portu napříč několika instancemi bez reverzního proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="47d51-656">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="47d51-657">Protokolování připojení</span><span class="sxs-lookup"><span data-stu-id="47d51-657">Connection logging</span></span>

<span data-ttu-id="47d51-658">Volání <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> k vygenerování protokolů na úrovni ladění pro komunikaci na úrovni bajtů v rámci připojení.</span><span class="sxs-lookup"><span data-stu-id="47d51-658">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="47d51-659">Protokolování připojení je užitečné při řešení problémů v komunikaci nízké úrovně, například během šifrování TLS a za proxy servery.</span><span class="sxs-lookup"><span data-stu-id="47d51-659">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="47d51-660">Pokud je `UseConnectionLogging` před `UseHttps`, zaprotokoluje se zašifrovaný provoz.</span><span class="sxs-lookup"><span data-stu-id="47d51-660">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="47d51-661">Je-li po `UseHttps`umístěn `UseConnectionLogging`, zašifrovaný provoz se zaznamená do protokolu.</span><span class="sxs-lookup"><span data-stu-id="47d51-661">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="47d51-662">Vytvoření vazby na soket TCP</span><span class="sxs-lookup"><span data-stu-id="47d51-662">Bind to a TCP socket</span></span>

<span data-ttu-id="47d51-663">Metoda <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> se váže k soketu TCP a obslužná rutina options umožňuje konfiguraci certifikátu X. 509:</span><span class="sxs-lookup"><span data-stu-id="47d51-663">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="47d51-664">Příklad konfiguruje HTTPS pro koncový bod s <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="47d51-664">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="47d51-665">Ke konfiguraci dalších nastavení Kestrel pro konkrétní koncové body použijte stejné rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="47d51-665">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="47d51-666">Vytvoření vazby na soket UNIX</span><span class="sxs-lookup"><span data-stu-id="47d51-666">Bind to a Unix socket</span></span>

<span data-ttu-id="47d51-667">Naslouchat na soketu se systémem UNIX pomocí <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> pro zlepšení výkonu pomocí Nginx, jak je znázorněno v tomto příkladu:</span><span class="sxs-lookup"><span data-stu-id="47d51-667">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

### <a name="port-0"></a><span data-ttu-id="47d51-668">Port 0</span><span class="sxs-lookup"><span data-stu-id="47d51-668">Port 0</span></span>

<span data-ttu-id="47d51-669">Když je zadané číslo portu `0`, dynamicky se váže k dostupnému portu.</span><span class="sxs-lookup"><span data-stu-id="47d51-669">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="47d51-670">Následující příklad ukazuje, jak určit, který port Kestrel je skutečně vázaný za běhu:</span><span class="sxs-lookup"><span data-stu-id="47d51-670">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="47d51-671">Když se aplikace spustí, výstup okna konzoly indikuje dynamický port, na který se aplikace dá získat:</span><span class="sxs-lookup"><span data-stu-id="47d51-671">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="47d51-672">Omezení</span><span class="sxs-lookup"><span data-stu-id="47d51-672">Limitations</span></span>

<span data-ttu-id="47d51-673">Konfigurace koncových bodů pomocí následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="47d51-673">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="47d51-674">argument příkazového řádku `--urls`</span><span class="sxs-lookup"><span data-stu-id="47d51-674">`--urls` command-line argument</span></span>
* <span data-ttu-id="47d51-675">konfigurační klíč hostitele `urls`</span><span class="sxs-lookup"><span data-stu-id="47d51-675">`urls` host configuration key</span></span>
* <span data-ttu-id="47d51-676">`ASPNETCORE_URLS` proměnná prostředí</span><span class="sxs-lookup"><span data-stu-id="47d51-676">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="47d51-677">Tyto metody jsou užitečné, pokud chcete, aby kód pracoval na jiných serverech než Kestrel.</span><span class="sxs-lookup"><span data-stu-id="47d51-677">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="47d51-678">Mějte ale na paměti následující omezení:</span><span class="sxs-lookup"><span data-stu-id="47d51-678">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="47d51-679">Protokol HTTPS nelze s těmito přístupy použít, pokud není k dispozici výchozí certifikát v konfiguraci koncového bodu HTTPS (například pomocí konfigurace `KestrelServerOptions` nebo konfiguračního souboru, jak je uvedeno výše v tomto tématu).</span><span class="sxs-lookup"><span data-stu-id="47d51-679">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="47d51-680">Při současném použití `Listen` i `UseUrls` jsou koncové body `Listen` popsány v koncových bodech `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="47d51-680">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="47d51-681">Konfigurace koncového bodu služby IIS</span><span class="sxs-lookup"><span data-stu-id="47d51-681">IIS endpoint configuration</span></span>

<span data-ttu-id="47d51-682">Při použití služby IIS jsou vazby adresy URL pro přepsání vazby služby IIS nastaveny buď pomocí `Listen`, nebo `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="47d51-682">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="47d51-683">Další informace najdete v tématu [ASP.NET Core modulu](xref:host-and-deploy/aspnet-core-module) .</span><span class="sxs-lookup"><span data-stu-id="47d51-683">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="47d51-684">ListenOptions. Protocols</span><span class="sxs-lookup"><span data-stu-id="47d51-684">ListenOptions.Protocols</span></span>

<span data-ttu-id="47d51-685">Vlastnost `Protocols` vytváří protokoly HTTP (`HttpProtocols`) povolené na koncovém bodu připojení nebo pro server.</span><span class="sxs-lookup"><span data-stu-id="47d51-685">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="47d51-686">Přiřaďte hodnotu vlastnosti `Protocols` ze výčtu `HttpProtocols`.</span><span class="sxs-lookup"><span data-stu-id="47d51-686">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="47d51-687">hodnota výčtu `HttpProtocols`</span><span class="sxs-lookup"><span data-stu-id="47d51-687">`HttpProtocols` enum value</span></span> | <span data-ttu-id="47d51-688">Povolený protokol připojení</span><span class="sxs-lookup"><span data-stu-id="47d51-688">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="47d51-689">Pouze HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="47d51-689">HTTP/1.1 only.</span></span> <span data-ttu-id="47d51-690">Dá se použít s TLS nebo bez něj.</span><span class="sxs-lookup"><span data-stu-id="47d51-690">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="47d51-691">Pouze HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="47d51-691">HTTP/2 only.</span></span> <span data-ttu-id="47d51-692">Dá se použít bez TLS jenom v případě, že klient podporuje [předchozí režim znalostní báze](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="47d51-692">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="47d51-693">HTTP/1.1 a HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="47d51-693">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="47d51-694">HTTP/2 vyžaduje připojení TLS a [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) ; v opačném případě se připojení nastaví jako výchozí HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="47d51-694">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="47d51-695">Výchozí protokol HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="47d51-695">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="47d51-696">Omezení TLS pro HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="47d51-696">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="47d51-697">TLS verze 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="47d51-697">TLS version 1.2 or later</span></span>
* <span data-ttu-id="47d51-698">Opětovné vyjednávání je zakázané.</span><span class="sxs-lookup"><span data-stu-id="47d51-698">Renegotiation disabled</span></span>
* <span data-ttu-id="47d51-699">Komprese vypnuta</span><span class="sxs-lookup"><span data-stu-id="47d51-699">Compression disabled</span></span>
* <span data-ttu-id="47d51-700">Minimální velikosti dočasného výměny klíčů:</span><span class="sxs-lookup"><span data-stu-id="47d51-700">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="47d51-701">Eliptická křivka Diffie-Hellman (ECDH) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bity minima</span><span class="sxs-lookup"><span data-stu-id="47d51-701">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum</span></span>
  * <span data-ttu-id="47d51-702">Omezené pole Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bity minima</span><span class="sxs-lookup"><span data-stu-id="47d51-702">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bits minimum</span></span>
* <span data-ttu-id="47d51-703">Šifrovací sada není zakázaná.</span><span class="sxs-lookup"><span data-stu-id="47d51-703">Cipher suite not blacklisted</span></span>

<span data-ttu-id="47d51-704">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; s eliptickou křivkou P-256 &lbrack;`FIPS186`&rbrack; je ve výchozím nastavení podporován.</span><span class="sxs-lookup"><span data-stu-id="47d51-704">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="47d51-705">Následující příklad povoluje připojení HTTP/1.1 a HTTP/2 na portu 8000.</span><span class="sxs-lookup"><span data-stu-id="47d51-705">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="47d51-706">Připojení TLS zabezpečuje pomocí zadaného certifikátu:</span><span class="sxs-lookup"><span data-stu-id="47d51-706">Connections are secured by TLS with a supplied certificate:</span></span>

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

<span data-ttu-id="47d51-707">Volitelně můžete vytvořit implementaci `IConnectionAdapter` pro filtrování ověřování TLS na základě jednotlivých připojení pro konkrétní šifry:</span><span class="sxs-lookup"><span data-stu-id="47d51-707">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

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

<span data-ttu-id="47d51-708">*Nastavit protokol z konfigurace*</span><span class="sxs-lookup"><span data-stu-id="47d51-708">*Set the protocol from configuration*</span></span>

<span data-ttu-id="47d51-709"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> volá ve výchozím nastavení `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))`, aby se načetla konfigurace Kestrel.</span><span class="sxs-lookup"><span data-stu-id="47d51-709"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="47d51-710">V následujícím příkladu *appSettings. JSON* se pro všechny koncové body Kestrel vytvoří výchozí protokol připojení (http/1.1 a HTTP/2):</span><span class="sxs-lookup"><span data-stu-id="47d51-710">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="47d51-711">Následující příklad konfiguračního souboru vytvoří protokol připojení pro konkrétní koncový bod:</span><span class="sxs-lookup"><span data-stu-id="47d51-711">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="47d51-712">Protokoly zadané v hodnotách přepisu kódu nastavených podle konfigurace</span><span class="sxs-lookup"><span data-stu-id="47d51-712">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="47d51-713">Konfigurace přenosu</span><span class="sxs-lookup"><span data-stu-id="47d51-713">Transport configuration</span></span>

<span data-ttu-id="47d51-714">S vydáním ASP.NET Core 2,1 není výchozí přenos Kestrel založen na Libuv, ale na základě spravovaných soketů.</span><span class="sxs-lookup"><span data-stu-id="47d51-714">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="47d51-715">Jedná se o zásadní změnu pro aplikace ASP.NET Core 2,0 upgradované na 2,1, která volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> a závisí na jednom z následujících balíčků:</span><span class="sxs-lookup"><span data-stu-id="47d51-715">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="47d51-716">[Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (přímý odkaz na balíček)</span><span class="sxs-lookup"><span data-stu-id="47d51-716">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="47d51-717">Microsoft. AspNetCore. app</span><span class="sxs-lookup"><span data-stu-id="47d51-717">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="47d51-718">Pro projekty, které vyžadují použití Libuv:</span><span class="sxs-lookup"><span data-stu-id="47d51-718">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="47d51-719">Přidejte do souboru projektu aplikace závislost pro balíček [Microsoft. AspNetCore. Server. Kestrel. Transport. Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) :</span><span class="sxs-lookup"><span data-stu-id="47d51-719">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="47d51-720">Volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span><span class="sxs-lookup"><span data-stu-id="47d51-720">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="47d51-721">Předpony adresy URL</span><span class="sxs-lookup"><span data-stu-id="47d51-721">URL prefixes</span></span>

<span data-ttu-id="47d51-722">Při použití `UseUrls`, `--urls` argumentu příkazového řádku, `urls` konfigurační klíč hostitele nebo proměnná prostředí `ASPNETCORE_URLS`, můžou být předpony adresy URL v některém z následujících formátů.</span><span class="sxs-lookup"><span data-stu-id="47d51-722">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="47d51-723">Platné jsou pouze předpony adresy URL protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="47d51-723">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="47d51-724">Kestrel nepodporuje HTTPS při konfiguraci vazeb URL pomocí `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="47d51-724">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="47d51-725">Adresa IPv4 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="47d51-725">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="47d51-726">`0.0.0.0` je zvláštní případ, který se váže ke všem adresám IPv4.</span><span class="sxs-lookup"><span data-stu-id="47d51-726">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="47d51-727">Adresa IPv6 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="47d51-727">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="47d51-728">`[::]` je ekvivalent protokolu IPv4 `0.0.0.0`protokolu IPv4.</span><span class="sxs-lookup"><span data-stu-id="47d51-728">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="47d51-729">Název hostitele s číslem portu</span><span class="sxs-lookup"><span data-stu-id="47d51-729">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="47d51-730">Názvy hostitelů `*` a `+` nejsou speciální.</span><span class="sxs-lookup"><span data-stu-id="47d51-730">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="47d51-731">Cokoli se nerozpoznalo jako platná IP adresa nebo `localhost` váže ke všem IP adresám IPv4 a IPv6.</span><span class="sxs-lookup"><span data-stu-id="47d51-731">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="47d51-732">Pokud chcete navazovat různé názvy hostitelů na různé ASP.NET Core aplikace na stejném portu, použijte [http. sys](xref:fundamentals/servers/httpsys) nebo reverzní proxy server, třeba IIS, Nginx nebo Apache.</span><span class="sxs-lookup"><span data-stu-id="47d51-732">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="47d51-733">Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="47d51-733">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="47d51-734">Název hostitele `localhost` s číslem portu nebo IP adresou zpětné smyčky s číslem portu</span><span class="sxs-lookup"><span data-stu-id="47d51-734">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="47d51-735">Pokud je zadána hodnota `localhost`, pokusí se Kestrel vytvořit propojení s rozhraními zpětné smyčky IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="47d51-735">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="47d51-736">Pokud je požadovaný port používán jinou službou na rozhraní zpětné smyčky, spuštění Kestrel se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="47d51-736">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="47d51-737">Pokud je rozhraní zpětné smyčky v žádném jiném případě nedostupné (většinou kvůli tomu, že protokol IPv6 není podporovaný), Kestrel zaznamená upozornění.</span><span class="sxs-lookup"><span data-stu-id="47d51-737">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="47d51-738">Filtrování hostitele</span><span class="sxs-lookup"><span data-stu-id="47d51-738">Host filtering</span></span>

<span data-ttu-id="47d51-739">I když Kestrel podporuje konfiguraci na základě prefixů, jako je například `http://example.com:5000`, Kestrel převážně ignoruje název hostitele.</span><span class="sxs-lookup"><span data-stu-id="47d51-739">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="47d51-740">Hostitelská `localhost` je zvláštní případ, který se používá pro vazbu na adresy zpětné smyčky.</span><span class="sxs-lookup"><span data-stu-id="47d51-740">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="47d51-741">Každý hostitel, který je jiný než explicitní IP adresa, se váže na všechny veřejné IP adresy.</span><span class="sxs-lookup"><span data-stu-id="47d51-741">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="47d51-742">hlavičky `Host` nejsou ověřeny.</span><span class="sxs-lookup"><span data-stu-id="47d51-742">`Host` headers aren't validated.</span></span>

<span data-ttu-id="47d51-743">Jako alternativní řešení použijte middleware pro filtrování hostitele.</span><span class="sxs-lookup"><span data-stu-id="47d51-743">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="47d51-744">Middleware pro filtrování hostitele poskytuje balíček [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , který je součástí [Microsoft. AspNetCore. App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 nebo 2,2).</span><span class="sxs-lookup"><span data-stu-id="47d51-744">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="47d51-745">Middleware se přidá pomocí <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, která volá <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="47d51-745">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="47d51-746">Middleware pro filtrování hostitele je ve výchozím nastavení zakázáno.</span><span class="sxs-lookup"><span data-stu-id="47d51-746">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="47d51-747">Chcete-li povolit middleware, definujte `AllowedHosts` klíč v souboru *appSettings. json*/*appSettings.\<prostředí >. JSON*.</span><span class="sxs-lookup"><span data-stu-id="47d51-747">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="47d51-748">Hodnota je seznam názvů hostitelů oddělených středníkem bez čísel portů:</span><span class="sxs-lookup"><span data-stu-id="47d51-748">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="47d51-749">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="47d51-749">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="47d51-750">[Middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer) má taky možnost <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="47d51-750">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="47d51-751">Middleware předávaných hlaviček a middleware pro filtrování hostitele mají podobné funkce pro různé scénáře.</span><span class="sxs-lookup"><span data-stu-id="47d51-751">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="47d51-752">Nastavení `AllowedHosts` s předaným middlewarem hlaviček je vhodné, když se hlavička `Host` nezachová při předávání požadavků pomocí reverzního proxy server nebo nástroje pro vyrovnávání zatížení.</span><span class="sxs-lookup"><span data-stu-id="47d51-752">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="47d51-753">Nastavení `AllowedHosts` pomocí middlewaru pro filtrování hostitele je vhodné, pokud se Kestrel používá jako veřejný hraniční Server nebo když je hlavička `Host` přímo předána.</span><span class="sxs-lookup"><span data-stu-id="47d51-753">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="47d51-754">Další informace o middlewaru předávaných hlaviček najdete v tématu <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="47d51-754">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="47d51-755">Kestrel je [webový server pro různé platformy pro ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="47d51-755">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="47d51-756">Kestrel je webový server, který je ve výchozím nastavení součástí ASP.NET Core šablon projektů.</span><span class="sxs-lookup"><span data-stu-id="47d51-756">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="47d51-757">Kestrel podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="47d51-757">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="47d51-758">HTTPS</span><span class="sxs-lookup"><span data-stu-id="47d51-758">HTTPS</span></span>
* <span data-ttu-id="47d51-759">Neprůhledný upgrade používaný k povolení [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="47d51-759">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="47d51-760">Technologie UNIX Sockets s vysokým výkonem za Nginx</span><span class="sxs-lookup"><span data-stu-id="47d51-760">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="47d51-761">Kestrel se podporuje na všech platformách a verzích, které podporuje .NET Core.</span><span class="sxs-lookup"><span data-stu-id="47d51-761">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="47d51-762">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="47d51-762">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="47d51-763">Kdy použít Kestrel s reverzním proxy serverem</span><span class="sxs-lookup"><span data-stu-id="47d51-763">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="47d51-764">Kestrel může používat sám sebe nebo s *reverzními proxy server*, jako je [Internetová informační služba (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="47d51-764">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="47d51-765">Reverzní proxy server přijímá požadavky HTTP ze sítě a přesměruje je na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="47d51-765">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="47d51-766">Kestrel použitý jako hraniční webový server (internetový):</span><span class="sxs-lookup"><span data-stu-id="47d51-766">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel komunikuje přímo s internetem bez zpětného proxy server](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="47d51-768">Kestrel se používá v konfiguraci reverzního proxy serveru:</span><span class="sxs-lookup"><span data-stu-id="47d51-768">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel nepřímo komunikuje s internetem prostřednictvím reverzní proxy server, jako je IIS, Nginx nebo Apache.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="47d51-770">Buď konfigurace, s zpětným proxy server nebo bez něj, je podporovaná konfigurace hostování.</span><span class="sxs-lookup"><span data-stu-id="47d51-770">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="47d51-771">Kestrel používané jako hraniční Server bez reverzních proxy server nepodporuje sdílení stejné IP adresy a portu mezi více procesy.</span><span class="sxs-lookup"><span data-stu-id="47d51-771">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="47d51-772">Když je Kestrel nakonfigurovaný k naslouchání na portu, Kestrel zpracovává veškerý provoz tohoto portu bez ohledu na hlavičky `Host`.</span><span class="sxs-lookup"><span data-stu-id="47d51-772">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="47d51-773">Reverzní proxy server, který může sdílet porty, má schopnost přesměrovat požadavky na Kestrel na jedinečné IP adresy a porty.</span><span class="sxs-lookup"><span data-stu-id="47d51-773">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="47d51-774">I v případě, že se zpětný proxy server nepožaduje, může být použití reverzní proxy server vhodnou volbou.</span><span class="sxs-lookup"><span data-stu-id="47d51-774">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="47d51-775">Reverzní proxy:</span><span class="sxs-lookup"><span data-stu-id="47d51-775">A reverse proxy:</span></span>

* <span data-ttu-id="47d51-776">Může omezit vystavenou veřejnou plochu v aplikacích, které hostuje.</span><span class="sxs-lookup"><span data-stu-id="47d51-776">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="47d51-777">Poskytněte další vrstvu konfigurace a obrany.</span><span class="sxs-lookup"><span data-stu-id="47d51-777">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="47d51-778">Může se lépe integrovat se stávající infrastrukturou.</span><span class="sxs-lookup"><span data-stu-id="47d51-778">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="47d51-779">Zjednodušte vyrovnávání zatížení a konfiguraci zabezpečené komunikace (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="47d51-779">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="47d51-780">Pouze reverzní proxy server vyžaduje certifikát X. 509 a tento server může komunikovat se servery aplikace v interní síti pomocí prostého protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="47d51-780">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="47d51-781">Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="47d51-781">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="47d51-782">Použití Kestrel v aplikacích ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="47d51-782">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="47d51-783">Balíček [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) je součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="47d51-783">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="47d51-784">ASP.NET Core šablony projektu ve výchozím nastavení používají Kestrel.</span><span class="sxs-lookup"><span data-stu-id="47d51-784">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="47d51-785">V *program.cs*kód šablony volá <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, která volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> na pozadí.</span><span class="sxs-lookup"><span data-stu-id="47d51-785">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="47d51-786">Pro poskytnutí další konfigurace po volání `CreateDefaultBuilder` volejte <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span><span class="sxs-lookup"><span data-stu-id="47d51-786">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="47d51-787">Další informace o `CreateDefaultBuilder` a vytváření hostitele najdete v části *Nastavení hostitele* v <xref:fundamentals/host/web-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="47d51-787">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="47d51-788">Kestrel možnosti</span><span class="sxs-lookup"><span data-stu-id="47d51-788">Kestrel options</span></span>

<span data-ttu-id="47d51-789">Webový server Kestrel má možnosti konfigurace omezení, které jsou zvláště užitečné v internetových nasazeních.</span><span class="sxs-lookup"><span data-stu-id="47d51-789">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="47d51-790">Nastavte omezení pro vlastnost <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> třídy <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="47d51-790">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="47d51-791">Vlastnost `Limits` obsahuje instanci třídy <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="47d51-791">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="47d51-792">V následujících příkladech se používá obor názvů <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:</span><span class="sxs-lookup"><span data-stu-id="47d51-792">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="47d51-793">Možnosti Kestrel, které jsou konfigurovány C# v kódu v následujících příkladech, lze také nastavit pomocí [poskytovatele konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="47d51-793">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="47d51-794">Poskytovatel konfigurace souboru může například načíst konfiguraci Kestrel z objektu *appSettings. JSON* nebo *appSettings. { Soubor Environment}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="47d51-794">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="47d51-795">Použijte **jeden** z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="47d51-795">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="47d51-796">Konfigurace Kestrel v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="47d51-796">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="47d51-797">Vloží instanci `IConfiguration` do třídy `Startup`.</span><span class="sxs-lookup"><span data-stu-id="47d51-797">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="47d51-798">Následující příklad předpokládá, že vložená konfigurace je přiřazena vlastnosti `Configuration`.</span><span class="sxs-lookup"><span data-stu-id="47d51-798">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="47d51-799">V `Startup.ConfigureServices` načtěte oddíl `Kestrel` konfigurace do konfigurace Kestrel.</span><span class="sxs-lookup"><span data-stu-id="47d51-799">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration.</span></span>

     ```csharp
     // using Microsoft.Extensions.Configuration

     public void ConfigureServices(IServiceCollection services)
     {
         services.Configure<KestrelServerOptions>(
             Configuration.GetSection("Kestrel"));
     }
     ```

* <span data-ttu-id="47d51-800">Nakonfigurovat Kestrel při sestavování hostitele:</span><span class="sxs-lookup"><span data-stu-id="47d51-800">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="47d51-801">V *program.cs*do konfigurace Kestrel načtěte oddíl `Kestrel` konfigurace:</span><span class="sxs-lookup"><span data-stu-id="47d51-801">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="47d51-802">Obě předchozí přístupy fungují u libovolného [poskytovatele konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="47d51-802">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="47d51-803">Časový limit Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="47d51-803">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="47d51-804">Získá nebo nastaví [časový limit zachování](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="47d51-804">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="47d51-805">Výchozí hodnota je 2 minuty.</span><span class="sxs-lookup"><span data-stu-id="47d51-805">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="47d51-806">Maximální počet připojení klientů</span><span class="sxs-lookup"><span data-stu-id="47d51-806">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="47d51-807">Maximální počet souběžných otevřených připojení TCP lze pro celou aplikaci nastavit pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="47d51-807">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="47d51-808">Existuje zvláštní omezení pro připojení, která byla upgradována z protokolu HTTP nebo HTTPS na jiný protokol (například na žádost WebSockets).</span><span class="sxs-lookup"><span data-stu-id="47d51-808">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="47d51-809">Po upgradu připojení se nepočítá s limitem `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="47d51-809">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="47d51-810">Ve výchozím nastavení je maximální počet připojení neomezené (null).</span><span class="sxs-lookup"><span data-stu-id="47d51-810">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="47d51-811">Maximální velikost textu požadavku</span><span class="sxs-lookup"><span data-stu-id="47d51-811">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="47d51-812">Výchozí maximální velikost textu požadavku je 30 000 000 bajtů, což je přibližně 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="47d51-812">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="47d51-813">Doporučený postup pro přepsání limitu v aplikaci ASP.NET Core MVC je použití atributu <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> u metody akce:</span><span class="sxs-lookup"><span data-stu-id="47d51-813">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="47d51-814">Tady je příklad, který ukazuje, jak nakonfigurovat omezení pro aplikaci na každém požadavku:</span><span class="sxs-lookup"><span data-stu-id="47d51-814">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="47d51-815">Přepsat nastavení u konkrétního požadavku v middleware:</span><span class="sxs-lookup"><span data-stu-id="47d51-815">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="47d51-816">Výjimka je vyvolána, pokud aplikace po zahájení aplikace nakonfiguruje limit na žádost.</span><span class="sxs-lookup"><span data-stu-id="47d51-816">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="47d51-817">Existuje vlastnost `IsReadOnly`, která určuje, zda je vlastnost `MaxRequestBodySize` ve stavu jen pro čtení, což znamená, že je příliš pozdě pro konfiguraci limitu.</span><span class="sxs-lookup"><span data-stu-id="47d51-817">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="47d51-818">Když je aplikace spouštěna [mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za [ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module), je omezení velikosti textu požadavku Kestrel zakázané, protože služba IIS tento limit již nastavuje.</span><span class="sxs-lookup"><span data-stu-id="47d51-818">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="47d51-819">Minimální rychlost těla zprávy požadavku</span><span class="sxs-lookup"><span data-stu-id="47d51-819">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="47d51-820">Kestrel kontroluje každou sekundu, pokud data přicházejí za zadanou rychlost v bajtech za sekundu.</span><span class="sxs-lookup"><span data-stu-id="47d51-820">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="47d51-821">Pokud frekvence klesne pod minimální hodnotu, vypršel časový limit připojení. Doba odkladu je doba, kterou Kestrel umožňuje klientovi zvýšit rychlost odesílání až na minimum; frekvence není během této doby kontrolována.</span><span class="sxs-lookup"><span data-stu-id="47d51-821">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="47d51-822">Doba odkladu pomáhá vyhnout se vyřazení připojení, která při počátečním posílání dat zpomalují rychlost, kvůli pomalému spuštění protokolu TCP.</span><span class="sxs-lookup"><span data-stu-id="47d51-822">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="47d51-823">Výchozí minimální sazba je 240 bajtů za sekundu s obdobím 5 sekund odkladu.</span><span class="sxs-lookup"><span data-stu-id="47d51-823">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="47d51-824">Pro odpověď platí také minimální sazba.</span><span class="sxs-lookup"><span data-stu-id="47d51-824">A minimum rate also applies to the response.</span></span> <span data-ttu-id="47d51-825">Kód pro nastavení limitu požadavků a limit odpovědi je stejný s výjimkou `RequestBody` nebo `Response` v názvech vlastností a rozhraní.</span><span class="sxs-lookup"><span data-stu-id="47d51-825">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="47d51-826">Tady je příklad, který ukazuje, jak nakonfigurovat minimální datové tarify v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="47d51-826">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

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

### <a name="request-headers-timeout"></a><span data-ttu-id="47d51-827">Časový limit hlaviček žádostí</span><span class="sxs-lookup"><span data-stu-id="47d51-827">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="47d51-828">Získá nebo nastaví maximální dobu, po kterou server stráví příjem hlaviček požadavků.</span><span class="sxs-lookup"><span data-stu-id="47d51-828">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="47d51-829">Výchozí hodnota je 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="47d51-829">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="47d51-830">Synchronní v/v</span><span class="sxs-lookup"><span data-stu-id="47d51-830">Synchronous IO</span></span>

<span data-ttu-id="47d51-831"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> řídí, zda je pro požadavek a odpověď povolena synchronní v/v.</span><span class="sxs-lookup"><span data-stu-id="47d51-831"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="47d51-832">Výchozí hodnota je `true`.</span><span class="sxs-lookup"><span data-stu-id="47d51-832">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="47d51-833">Velký počet blokování synchronních vstupně-výstupních operací může vést ke vyčerpání fondu vláken, což aplikaci nereaguje.</span><span class="sxs-lookup"><span data-stu-id="47d51-833">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="47d51-834">Při použití knihovny, která nepodporuje asynchronní vstupně-výstupní operace, povolte pouze `AllowSynchronousIO`.</span><span class="sxs-lookup"><span data-stu-id="47d51-834">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="47d51-835">Následující příklad zakáže synchronní v/v:</span><span class="sxs-lookup"><span data-stu-id="47d51-835">The following example disables synchronous IO:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="47d51-836">Informace o dalších možnostech a omezeních Kestrel najdete v tématech:</span><span class="sxs-lookup"><span data-stu-id="47d51-836">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="47d51-837">Konfigurace koncového bodu</span><span class="sxs-lookup"><span data-stu-id="47d51-837">Endpoint configuration</span></span>

<span data-ttu-id="47d51-838">Ve výchozím nastavení ASP.NET Core váže k:</span><span class="sxs-lookup"><span data-stu-id="47d51-838">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="47d51-839">`https://localhost:5001` (Pokud je k dispozici místní vývojový certifikát)</span><span class="sxs-lookup"><span data-stu-id="47d51-839">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="47d51-840">Zadejte adresy URL pomocí:</span><span class="sxs-lookup"><span data-stu-id="47d51-840">Specify URLs using the:</span></span>

* <span data-ttu-id="47d51-841">`ASPNETCORE_URLS` proměnnou prostředí.</span><span class="sxs-lookup"><span data-stu-id="47d51-841">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="47d51-842">argument příkazového řádku `--urls`</span><span class="sxs-lookup"><span data-stu-id="47d51-842">`--urls` command-line argument.</span></span>
* <span data-ttu-id="47d51-843">`urls` konfigurační klíč hostitele.</span><span class="sxs-lookup"><span data-stu-id="47d51-843">`urls` host configuration key.</span></span>
* <span data-ttu-id="47d51-844">Metoda rozšíření `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="47d51-844">`UseUrls` extension method.</span></span>

<span data-ttu-id="47d51-845">Hodnota zadaná pomocí těchto přístupů může být jeden nebo více koncových bodů HTTP a HTTPS (HTTPS, pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="47d51-845">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="47d51-846">Nakonfigurujte hodnotu jako seznam oddělený středníkem (například `"Urls": "http://localhost:8000; http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="47d51-846">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="47d51-847">Další informace o těchto přístupůch najdete v tématu [adresy URL serveru](xref:fundamentals/host/web-host#server-urls) a [potlačení konfigurace](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="47d51-847">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="47d51-848">Vytvoří se vývojový certifikát:</span><span class="sxs-lookup"><span data-stu-id="47d51-848">A development certificate is created:</span></span>

* <span data-ttu-id="47d51-849">Po instalaci [.NET Core SDK](/dotnet/core/sdk) .</span><span class="sxs-lookup"><span data-stu-id="47d51-849">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="47d51-850">[Nástroj pro vývoj a certifikáty](xref:aspnetcore-2.1#https) slouží k vytvoření certifikátu.</span><span class="sxs-lookup"><span data-stu-id="47d51-850">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="47d51-851">Některé prohlížeče vyžadují udělení explicitního oprávnění pro důvěřování místního vývojového certifikátu.</span><span class="sxs-lookup"><span data-stu-id="47d51-851">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="47d51-852">Šablony projektů konfigurují aplikace tak, aby běžely ve výchozím nastavení HTTPS a zahrnovaly [přesměrování https a podporu HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="47d51-852">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="47d51-853">Pokud chcete nakonfigurovat předpony a porty adres URL pro Kestrel, volejte <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> nebo <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> na <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="47d51-853">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="47d51-854">`UseUrls`, argument příkazového řádku `--urls`, `urls` konfigurační klíč hostitele a proměnná prostředí `ASPNETCORE_URLS` fungují, ale mají omezení zaznamenaná později v této části (pro konfiguraci koncového bodu HTTPS musí být k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="47d51-854">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="47d51-855">Konfigurace `KestrelServerOptions`:</span><span class="sxs-lookup"><span data-stu-id="47d51-855">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="47d51-856">ConfigureEndpointDefaults (Action\<ListenOptions >)</span><span class="sxs-lookup"><span data-stu-id="47d51-856">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="47d51-857">Určuje konfiguraci `Action`, která se má spustit pro každý zadaný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="47d51-857">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="47d51-858">Volání `ConfigureEndpointDefaults` několikrát nahrazuje předchozí `Action` s určeným posledním `Action`.</span><span class="sxs-lookup"><span data-stu-id="47d51-858">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="47d51-859">ConfigureHttpsDefaults (Action\<HttpsConnectionAdapterOptions >)</span><span class="sxs-lookup"><span data-stu-id="47d51-859">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="47d51-860">Určuje konfiguraci `Action` pro spuštění každého koncového bodu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="47d51-860">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="47d51-861">Volání `ConfigureHttpsDefaults` několikrát nahrazuje předchozí `Action` s určeným posledním `Action`.</span><span class="sxs-lookup"><span data-stu-id="47d51-861">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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

### <a name="configureiconfiguration"></a><span data-ttu-id="47d51-862">Konfigurace (IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="47d51-862">Configure(IConfiguration)</span></span>

<span data-ttu-id="47d51-863">Vytvoří zavaděč konfigurace pro nastavení Kestrel, který jako vstup převezme <xref:Microsoft.Extensions.Configuration.IConfiguration>.</span><span class="sxs-lookup"><span data-stu-id="47d51-863">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="47d51-864">Konfigurace musí být vymezená na konfigurační oddíl pro Kestrel.</span><span class="sxs-lookup"><span data-stu-id="47d51-864">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="47d51-865">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="47d51-865">ListenOptions.UseHttps</span></span>

<span data-ttu-id="47d51-866">Nakonfigurujte Kestrel tak, aby používal protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="47d51-866">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="47d51-867">rozšíření `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="47d51-867">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="47d51-868">`UseHttps` &ndash; nakonfigurujte Kestrel tak, aby používal HTTPS s výchozím certifikátem.</span><span class="sxs-lookup"><span data-stu-id="47d51-868">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="47d51-869">Vyvolá výjimku, pokud není nakonfigurován žádný výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="47d51-869">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="47d51-870">parametry `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="47d51-870">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="47d51-871">`filename` je cesta a název souboru certifikátu vzhledem k adresáři, který obsahuje soubory obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="47d51-871">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="47d51-872">`password` je heslo vyžadované pro přístup k datům certifikátu X. 509.</span><span class="sxs-lookup"><span data-stu-id="47d51-872">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="47d51-873">`configureOptions` je `Action` ke konfiguraci `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="47d51-873">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="47d51-874">Vrátí `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="47d51-874">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="47d51-875">`storeName` je úložiště certifikátů, ze kterého se má certifikát načíst.</span><span class="sxs-lookup"><span data-stu-id="47d51-875">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="47d51-876">`subject` je název subjektu certifikátu.</span><span class="sxs-lookup"><span data-stu-id="47d51-876">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="47d51-877">`allowInvalid` označuje, zda by měly být zváženy neplatné certifikáty, například certifikáty podepsané svým držitelem.</span><span class="sxs-lookup"><span data-stu-id="47d51-877">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="47d51-878">`location` je umístění úložiště, ze kterého se má načíst certifikát.</span><span class="sxs-lookup"><span data-stu-id="47d51-878">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="47d51-879">`serverCertificate` je certifikát X. 509.</span><span class="sxs-lookup"><span data-stu-id="47d51-879">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="47d51-880">V produkčním prostředí musí být HTTPS explicitně nakonfigurovaný.</span><span class="sxs-lookup"><span data-stu-id="47d51-880">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="47d51-881">Musí být zadán minimálně výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="47d51-881">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="47d51-882">Podporované konfigurace popsané dál:</span><span class="sxs-lookup"><span data-stu-id="47d51-882">Supported configurations described next:</span></span>

* <span data-ttu-id="47d51-883">Žádná konfigurace</span><span class="sxs-lookup"><span data-stu-id="47d51-883">No configuration</span></span>
* <span data-ttu-id="47d51-884">Nahradit výchozí certifikát z konfigurace</span><span class="sxs-lookup"><span data-stu-id="47d51-884">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="47d51-885">Změna výchozích hodnot v kódu</span><span class="sxs-lookup"><span data-stu-id="47d51-885">Change the defaults in code</span></span>

<span data-ttu-id="47d51-886">*Žádná konfigurace*</span><span class="sxs-lookup"><span data-stu-id="47d51-886">*No configuration*</span></span>

<span data-ttu-id="47d51-887">Kestrel naslouchá na `http://localhost:5000` a `https://localhost:5001` (Pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="47d51-887">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="47d51-888">*Nahradit výchozí certifikát z konfigurace*</span><span class="sxs-lookup"><span data-stu-id="47d51-888">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="47d51-889">`CreateDefaultBuilder` volá ve výchozím nastavení `Configure(context.Configuration.GetSection("Kestrel"))`, aby se načetla konfigurace Kestrel.</span><span class="sxs-lookup"><span data-stu-id="47d51-889">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="47d51-890">Pro Kestrel je k dispozici výchozí schéma konfigurace nastavení aplikace HTTPS.</span><span class="sxs-lookup"><span data-stu-id="47d51-890">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="47d51-891">Nakonfigurujte několik koncových bodů, včetně adres URL a certifikátů, které se mají použít, buď ze souboru na disku, nebo z úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="47d51-891">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="47d51-892">V následujícím příkladu *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="47d51-892">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="47d51-893">Nastavte **AllowInvalid** na `true`, pokud chcete povolit použití neplatných certifikátů (například certifikátů podepsaných svým držitelem).</span><span class="sxs-lookup"><span data-stu-id="47d51-893">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="47d51-894">Libovolný koncový bod HTTPS, který neurčuje certifikát (**HttpsDefaultCert** v následujícím příkladu), se vrátí k certifikátu definovanému v části **certifikáty** > **výchozí** nebo vývojový certifikát.</span><span class="sxs-lookup"><span data-stu-id="47d51-894">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="47d51-895">Alternativou k použití **cesty** a **hesla** pro libovolný uzel certifikátu je zadání certifikátu pomocí polí úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="47d51-895">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="47d51-896">Například **certifikáty** > **výchozí** certifikát lze zadat jako:</span><span class="sxs-lookup"><span data-stu-id="47d51-896">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="47d51-897">Poznámky ke schématu:</span><span class="sxs-lookup"><span data-stu-id="47d51-897">Schema notes:</span></span>

* <span data-ttu-id="47d51-898">V názvech koncových bodů se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="47d51-898">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="47d51-899">Například `HTTPS` a `Https` jsou platné.</span><span class="sxs-lookup"><span data-stu-id="47d51-899">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="47d51-900">U každého koncového bodu je vyžadován parametr `Url`.</span><span class="sxs-lookup"><span data-stu-id="47d51-900">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="47d51-901">Formát tohoto parametru je stejný jako konfigurační parametr `Urls` nejvyšší úrovně s tím rozdílem, že je omezen na jedinou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="47d51-901">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="47d51-902">Tyto koncové body nahrazují hodnoty definované v konfiguraci `Urls` na nejvyšší úrovni místo jejich přidávání do těchto koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="47d51-902">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="47d51-903">Koncové body definované v kódu prostřednictvím `Listen` jsou kumulativní s koncovými body definovanými v konfiguračním oddílu.</span><span class="sxs-lookup"><span data-stu-id="47d51-903">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="47d51-904">Oddíl `Certificate` je nepovinný.</span><span class="sxs-lookup"><span data-stu-id="47d51-904">The `Certificate` section is optional.</span></span> <span data-ttu-id="47d51-905">Pokud se nezadá oddíl `Certificate`, použijí se výchozí hodnoty definované v dřívějších scénářích.</span><span class="sxs-lookup"><span data-stu-id="47d51-905">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="47d51-906">Pokud nejsou k dispozici žádné výchozí hodnoty, Server vyvolá výjimku a nemůže se spustit.</span><span class="sxs-lookup"><span data-stu-id="47d51-906">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="47d51-907">Oddíl `Certificate` podporuje jak **cesty**&ndash;**hesla** , tak&ndash;ho **úložiště** **subjektu** .</span><span class="sxs-lookup"><span data-stu-id="47d51-907">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="47d51-908">V takovém případě může být definován libovolný počet koncových bodů, pokud to nezpůsobí konflikty portů.</span><span class="sxs-lookup"><span data-stu-id="47d51-908">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="47d51-909">`options.Configure(context.Configuration.GetSection("{SECTION}"))` vrátí `KestrelConfigurationLoader` s metodou `.Endpoint(string name, listenOptions => { })`, která se dá použít k doplnění nastavení nakonfigurovaného koncového bodu:</span><span class="sxs-lookup"><span data-stu-id="47d51-909">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="47d51-910">`KestrelServerOptions.ConfigurationLoader` se dá získat přímo k pokračování v iteraci u stávajícího zavaděče, jako je třeba ten, který poskytuje <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span><span class="sxs-lookup"><span data-stu-id="47d51-910">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="47d51-911">Konfigurační oddíl pro každý koncový bod je k dispozici na možnostech v metodě `Endpoint`, aby bylo možné číst vlastní nastavení.</span><span class="sxs-lookup"><span data-stu-id="47d51-911">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="47d51-912">Více konfigurací může být načteno voláním `options.Configure(context.Configuration.GetSection("{SECTION}"))` znovu s jiným oddílem.</span><span class="sxs-lookup"><span data-stu-id="47d51-912">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="47d51-913">Je použita pouze poslední konfigurace, pokud není metoda `Load` explicitně volána pro předchozí instance.</span><span class="sxs-lookup"><span data-stu-id="47d51-913">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="47d51-914">Metapackage nevolá `Load`, takže jeho výchozí konfigurační oddíl může být nahrazen.</span><span class="sxs-lookup"><span data-stu-id="47d51-914">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="47d51-915">`KestrelConfigurationLoader` zrcadlí `Listen` rodinu rozhraní API od `KestrelServerOptions` jako přetížení `Endpoint`, takže koncové body kódu a konfigurace lze konfigurovat na stejném místě.</span><span class="sxs-lookup"><span data-stu-id="47d51-915">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="47d51-916">Tato přetížení nepoužívají názvy a využívají pouze výchozí nastavení z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="47d51-916">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="47d51-917">*Změna výchozích hodnot v kódu*</span><span class="sxs-lookup"><span data-stu-id="47d51-917">*Change the defaults in code*</span></span>

<span data-ttu-id="47d51-918">`ConfigureEndpointDefaults` a `ConfigureHttpsDefaults` lze použít ke změně výchozího nastavení pro `ListenOptions` a `HttpsConnectionAdapterOptions`, včetně přepsání výchozího certifikátu zadaného v předchozím scénáři.</span><span class="sxs-lookup"><span data-stu-id="47d51-918">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="47d51-919">`ConfigureEndpointDefaults` a `ConfigureHttpsDefaults` by měly být volány před konfigurací koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="47d51-919">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="47d51-920">*Podpora Kestrel pro SNI*</span><span class="sxs-lookup"><span data-stu-id="47d51-920">*Kestrel support for SNI*</span></span>

<span data-ttu-id="47d51-921">[Indikace názvu serveru (SNI)](https://tools.ietf.org/html/rfc6066#section-3) lze použít k hostování více domén na stejné IP adrese a portu.</span><span class="sxs-lookup"><span data-stu-id="47d51-921">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="47d51-922">Aby SNI fungovalo, klient pošle název hostitele pro zabezpečenou relaci serveru během metody handshake TLS, aby server mohl poskytovat správný certifikát.</span><span class="sxs-lookup"><span data-stu-id="47d51-922">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="47d51-923">Klient používá poskytnutý certifikát pro šifrovanou komunikaci se serverem během zabezpečené relace, která následuje za metodou handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="47d51-923">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="47d51-924">Kestrel podporuje SNI prostřednictvím zpětného volání `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="47d51-924">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="47d51-925">Zpětné volání je vyvoláno jednou za připojení, které aplikaci umožní zkontrolovat název hostitele a vybrat příslušný certifikát.</span><span class="sxs-lookup"><span data-stu-id="47d51-925">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="47d51-926">Podpora SNI vyžaduje:</span><span class="sxs-lookup"><span data-stu-id="47d51-926">SNI support requires:</span></span>

* <span data-ttu-id="47d51-927">Spuštění v cílovém rozhraní `netcoreapp2.1` nebo novější.</span><span class="sxs-lookup"><span data-stu-id="47d51-927">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="47d51-928">V `net461` nebo novějším je zpětné volání vyvoláno, ale `name` je vždy `null`.</span><span class="sxs-lookup"><span data-stu-id="47d51-928">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="47d51-929">`name` taky `null`, pokud klient neposkytne parametr názvu hostitele v handshaki TLS.</span><span class="sxs-lookup"><span data-stu-id="47d51-929">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="47d51-930">Všechny weby běží na stejné instanci Kestrel.</span><span class="sxs-lookup"><span data-stu-id="47d51-930">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="47d51-931">Kestrel nepodporuje sdílení IP adresy a portu napříč několika instancemi bez reverzního proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="47d51-931">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="47d51-932">Protokolování připojení</span><span class="sxs-lookup"><span data-stu-id="47d51-932">Connection logging</span></span>

<span data-ttu-id="47d51-933">Volání <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> k vygenerování protokolů na úrovni ladění pro komunikaci na úrovni bajtů v rámci připojení.</span><span class="sxs-lookup"><span data-stu-id="47d51-933">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="47d51-934">Protokolování připojení je užitečné při řešení problémů v komunikaci nízké úrovně, například během šifrování TLS a za proxy servery.</span><span class="sxs-lookup"><span data-stu-id="47d51-934">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="47d51-935">Pokud je `UseConnectionLogging` před `UseHttps`, zaprotokoluje se zašifrovaný provoz.</span><span class="sxs-lookup"><span data-stu-id="47d51-935">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="47d51-936">Je-li po `UseHttps`umístěn `UseConnectionLogging`, zašifrovaný provoz se zaznamená do protokolu.</span><span class="sxs-lookup"><span data-stu-id="47d51-936">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="47d51-937">Vytvoření vazby na soket TCP</span><span class="sxs-lookup"><span data-stu-id="47d51-937">Bind to a TCP socket</span></span>

<span data-ttu-id="47d51-938">Metoda <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> se váže k soketu TCP a obslužná rutina options umožňuje konfiguraci certifikátu X. 509:</span><span class="sxs-lookup"><span data-stu-id="47d51-938">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

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

<span data-ttu-id="47d51-939">Příklad konfiguruje HTTPS pro koncový bod s <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="47d51-939">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="47d51-940">Ke konfiguraci dalších nastavení Kestrel pro konkrétní koncové body použijte stejné rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="47d51-940">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="47d51-941">Vytvoření vazby na soket UNIX</span><span class="sxs-lookup"><span data-stu-id="47d51-941">Bind to a Unix socket</span></span>

<span data-ttu-id="47d51-942">Naslouchat na soketu se systémem UNIX pomocí <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> pro zlepšení výkonu pomocí Nginx, jak je znázorněno v tomto příkladu:</span><span class="sxs-lookup"><span data-stu-id="47d51-942">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

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

### <a name="port-0"></a><span data-ttu-id="47d51-943">Port 0</span><span class="sxs-lookup"><span data-stu-id="47d51-943">Port 0</span></span>

<span data-ttu-id="47d51-944">Když je zadané číslo portu `0`, dynamicky se váže k dostupnému portu.</span><span class="sxs-lookup"><span data-stu-id="47d51-944">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="47d51-945">Následující příklad ukazuje, jak určit, který port Kestrel je skutečně vázaný za běhu:</span><span class="sxs-lookup"><span data-stu-id="47d51-945">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="47d51-946">Když se aplikace spustí, výstup okna konzoly indikuje dynamický port, na který se aplikace dá získat:</span><span class="sxs-lookup"><span data-stu-id="47d51-946">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="47d51-947">Omezení</span><span class="sxs-lookup"><span data-stu-id="47d51-947">Limitations</span></span>

<span data-ttu-id="47d51-948">Konfigurace koncových bodů pomocí následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="47d51-948">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="47d51-949">argument příkazového řádku `--urls`</span><span class="sxs-lookup"><span data-stu-id="47d51-949">`--urls` command-line argument</span></span>
* <span data-ttu-id="47d51-950">konfigurační klíč hostitele `urls`</span><span class="sxs-lookup"><span data-stu-id="47d51-950">`urls` host configuration key</span></span>
* <span data-ttu-id="47d51-951">`ASPNETCORE_URLS` proměnná prostředí</span><span class="sxs-lookup"><span data-stu-id="47d51-951">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="47d51-952">Tyto metody jsou užitečné, pokud chcete, aby kód pracoval na jiných serverech než Kestrel.</span><span class="sxs-lookup"><span data-stu-id="47d51-952">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="47d51-953">Mějte ale na paměti následující omezení:</span><span class="sxs-lookup"><span data-stu-id="47d51-953">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="47d51-954">Protokol HTTPS nelze s těmito přístupy použít, pokud není k dispozici výchozí certifikát v konfiguraci koncového bodu HTTPS (například pomocí konfigurace `KestrelServerOptions` nebo konfiguračního souboru, jak je uvedeno výše v tomto tématu).</span><span class="sxs-lookup"><span data-stu-id="47d51-954">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="47d51-955">Při současném použití `Listen` i `UseUrls` jsou koncové body `Listen` popsány v koncových bodech `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="47d51-955">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="47d51-956">Konfigurace koncového bodu služby IIS</span><span class="sxs-lookup"><span data-stu-id="47d51-956">IIS endpoint configuration</span></span>

<span data-ttu-id="47d51-957">Při použití služby IIS jsou vazby adresy URL pro přepsání vazby služby IIS nastaveny buď pomocí `Listen`, nebo `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="47d51-957">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="47d51-958">Další informace najdete v tématu [ASP.NET Core modulu](xref:host-and-deploy/aspnet-core-module) .</span><span class="sxs-lookup"><span data-stu-id="47d51-958">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="47d51-959">Konfigurace přenosu</span><span class="sxs-lookup"><span data-stu-id="47d51-959">Transport configuration</span></span>

<span data-ttu-id="47d51-960">S vydáním ASP.NET Core 2,1 není výchozí přenos Kestrel založen na Libuv, ale na základě spravovaných soketů.</span><span class="sxs-lookup"><span data-stu-id="47d51-960">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="47d51-961">Jedná se o zásadní změnu pro aplikace ASP.NET Core 2,0 upgradované na 2,1, která volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> a závisí na jednom z následujících balíčků:</span><span class="sxs-lookup"><span data-stu-id="47d51-961">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="47d51-962">[Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (přímý odkaz na balíček)</span><span class="sxs-lookup"><span data-stu-id="47d51-962">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="47d51-963">Microsoft. AspNetCore. app</span><span class="sxs-lookup"><span data-stu-id="47d51-963">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="47d51-964">Pro projekty, které vyžadují použití Libuv:</span><span class="sxs-lookup"><span data-stu-id="47d51-964">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="47d51-965">Přidejte do souboru projektu aplikace závislost pro balíček [Microsoft. AspNetCore. Server. Kestrel. Transport. Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) :</span><span class="sxs-lookup"><span data-stu-id="47d51-965">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="47d51-966">Volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span><span class="sxs-lookup"><span data-stu-id="47d51-966">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="47d51-967">Předpony adresy URL</span><span class="sxs-lookup"><span data-stu-id="47d51-967">URL prefixes</span></span>

<span data-ttu-id="47d51-968">Při použití `UseUrls`, `--urls` argumentu příkazového řádku, `urls` konfigurační klíč hostitele nebo proměnná prostředí `ASPNETCORE_URLS`, můžou být předpony adresy URL v některém z následujících formátů.</span><span class="sxs-lookup"><span data-stu-id="47d51-968">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="47d51-969">Platné jsou pouze předpony adresy URL protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="47d51-969">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="47d51-970">Kestrel nepodporuje HTTPS při konfiguraci vazeb URL pomocí `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="47d51-970">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="47d51-971">Adresa IPv4 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="47d51-971">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="47d51-972">`0.0.0.0` je zvláštní případ, který se váže ke všem adresám IPv4.</span><span class="sxs-lookup"><span data-stu-id="47d51-972">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="47d51-973">Adresa IPv6 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="47d51-973">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="47d51-974">`[::]` je ekvivalent protokolu IPv4 `0.0.0.0`protokolu IPv4.</span><span class="sxs-lookup"><span data-stu-id="47d51-974">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="47d51-975">Název hostitele s číslem portu</span><span class="sxs-lookup"><span data-stu-id="47d51-975">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="47d51-976">Názvy hostitelů `*` a `+` nejsou speciální.</span><span class="sxs-lookup"><span data-stu-id="47d51-976">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="47d51-977">Cokoli se nerozpoznalo jako platná IP adresa nebo `localhost` váže ke všem IP adresám IPv4 a IPv6.</span><span class="sxs-lookup"><span data-stu-id="47d51-977">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="47d51-978">Pokud chcete navazovat různé názvy hostitelů na různé ASP.NET Core aplikace na stejném portu, použijte [http. sys](xref:fundamentals/servers/httpsys) nebo reverzní proxy server, třeba IIS, Nginx nebo Apache.</span><span class="sxs-lookup"><span data-stu-id="47d51-978">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="47d51-979">Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="47d51-979">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="47d51-980">Název hostitele `localhost` s číslem portu nebo IP adresou zpětné smyčky s číslem portu</span><span class="sxs-lookup"><span data-stu-id="47d51-980">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="47d51-981">Pokud je zadána hodnota `localhost`, pokusí se Kestrel vytvořit propojení s rozhraními zpětné smyčky IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="47d51-981">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="47d51-982">Pokud je požadovaný port používán jinou službou na rozhraní zpětné smyčky, spuštění Kestrel se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="47d51-982">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="47d51-983">Pokud je rozhraní zpětné smyčky v žádném jiném případě nedostupné (většinou kvůli tomu, že protokol IPv6 není podporovaný), Kestrel zaznamená upozornění.</span><span class="sxs-lookup"><span data-stu-id="47d51-983">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="47d51-984">Filtrování hostitele</span><span class="sxs-lookup"><span data-stu-id="47d51-984">Host filtering</span></span>

<span data-ttu-id="47d51-985">I když Kestrel podporuje konfiguraci na základě prefixů, jako je například `http://example.com:5000`, Kestrel převážně ignoruje název hostitele.</span><span class="sxs-lookup"><span data-stu-id="47d51-985">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="47d51-986">Hostitelská `localhost` je zvláštní případ, který se používá pro vazbu na adresy zpětné smyčky.</span><span class="sxs-lookup"><span data-stu-id="47d51-986">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="47d51-987">Každý hostitel, který je jiný než explicitní IP adresa, se váže na všechny veřejné IP adresy.</span><span class="sxs-lookup"><span data-stu-id="47d51-987">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="47d51-988">hlavičky `Host` nejsou ověřeny.</span><span class="sxs-lookup"><span data-stu-id="47d51-988">`Host` headers aren't validated.</span></span>

<span data-ttu-id="47d51-989">Jako alternativní řešení použijte middleware pro filtrování hostitele.</span><span class="sxs-lookup"><span data-stu-id="47d51-989">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="47d51-990">Middleware pro filtrování hostitele poskytuje balíček [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , který je součástí [Microsoft. AspNetCore. App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 nebo 2,2).</span><span class="sxs-lookup"><span data-stu-id="47d51-990">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="47d51-991">Middleware se přidá pomocí <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, která volá <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="47d51-991">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="47d51-992">Middleware pro filtrování hostitele je ve výchozím nastavení zakázáno.</span><span class="sxs-lookup"><span data-stu-id="47d51-992">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="47d51-993">Chcete-li povolit middleware, definujte `AllowedHosts` klíč v souboru *appSettings. json*/*appSettings.\<prostředí >. JSON*.</span><span class="sxs-lookup"><span data-stu-id="47d51-993">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="47d51-994">Hodnota je seznam názvů hostitelů oddělených středníkem bez čísel portů:</span><span class="sxs-lookup"><span data-stu-id="47d51-994">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="47d51-995">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="47d51-995">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="47d51-996">[Middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer) má taky možnost <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="47d51-996">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="47d51-997">Middleware předávaných hlaviček a middleware pro filtrování hostitele mají podobné funkce pro různé scénáře.</span><span class="sxs-lookup"><span data-stu-id="47d51-997">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="47d51-998">Nastavení `AllowedHosts` s předaným middlewarem hlaviček je vhodné, když se hlavička `Host` nezachová při předávání požadavků pomocí reverzního proxy server nebo nástroje pro vyrovnávání zatížení.</span><span class="sxs-lookup"><span data-stu-id="47d51-998">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="47d51-999">Nastavení `AllowedHosts` pomocí middlewaru pro filtrování hostitele je vhodné, pokud se Kestrel používá jako veřejný hraniční Server nebo když je hlavička `Host` přímo předána.</span><span class="sxs-lookup"><span data-stu-id="47d51-999">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="47d51-1000">Další informace o middlewaru předávaných hlaviček najdete v tématu <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="47d51-1000">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="47d51-1001">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="47d51-1001">Additional resources</span></span>

* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="47d51-1002">RFC 7230: syntaxe zprávy a směrování (oddíl 5,4: hostitel)</span><span class="sxs-lookup"><span data-stu-id="47d51-1002">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
