---
title: Implementace webového serveru Kestrel v ASP.NET Core
author: rick-anderson
description: Přečtěte si o Kestrel, webovém serveru pro různé platformy pro ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2020
uid: fundamentals/servers/kestrel
ms.openlocfilehash: e9b4b57ee70e4050f9399b90a6e34e8cc9cca78d
ms.sourcegitcommit: 91dc1dd3d055b4c7d7298420927b3fd161067c64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "80218827"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="b7f65-103">Implementace webového serveru Kestrel v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b7f65-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="b7f65-104">Díky [Dykstra](https://github.com/tdykstra), [Novák Rossův](https://github.com/Tratcher)a zablokování [Stephen](https://twitter.com/halter73)</span><span class="sxs-lookup"><span data-stu-id="b7f65-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b7f65-105">Kestrel je [webový server pro různé platformy pro ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="b7f65-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="b7f65-106">Kestrel je webový server, který je ve výchozím nastavení součástí ASP.NET Core šablon projektů.</span><span class="sxs-lookup"><span data-stu-id="b7f65-106">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="b7f65-107">Kestrel podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="b7f65-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="b7f65-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="b7f65-108">HTTPS</span></span>
* <span data-ttu-id="b7f65-109">Neprůhledný upgrade používaný k povolení [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="b7f65-109">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="b7f65-110">Technologie UNIX Sockets s vysokým výkonem za Nginx</span><span class="sxs-lookup"><span data-stu-id="b7f65-110">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="b7f65-111">HTTP/2 (kromě macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="b7f65-111">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="b7f65-112">&dagger;HTTP/2 bude v budoucí verzi podporován v macOS.</span><span class="sxs-lookup"><span data-stu-id="b7f65-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="b7f65-113">Kestrel se podporuje na všech platformách a verzích, které podporuje .NET Core.</span><span class="sxs-lookup"><span data-stu-id="b7f65-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="b7f65-114">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b7f65-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="b7f65-115">Podpora HTTP/2</span><span class="sxs-lookup"><span data-stu-id="b7f65-115">HTTP/2 support</span></span>

<span data-ttu-id="b7f65-116">[Http/2](https://httpwg.org/specs/rfc7540.html) je k dispozici pro ASP.NET Core aplikace, pokud jsou splněné následující základní požadavky:</span><span class="sxs-lookup"><span data-stu-id="b7f65-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="b7f65-117">&dagger; operačního systému</span><span class="sxs-lookup"><span data-stu-id="b7f65-117">Operating system&dagger;</span></span>
  * <span data-ttu-id="b7f65-118">Windows Server 2016/Windows 10 nebo novější&Dagger;</span><span class="sxs-lookup"><span data-stu-id="b7f65-118">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="b7f65-119">Linux s OpenSSL 1.0.2 nebo novějším (například Ubuntu 16,04 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="b7f65-119">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="b7f65-120">Cílová architektura: .NET Core 2,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="b7f65-120">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="b7f65-121">Připojení [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="b7f65-121">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="b7f65-122">Protokol TLS 1.2 nebo vyšší připojení</span><span class="sxs-lookup"><span data-stu-id="b7f65-122">TLS 1.2 or later connection</span></span>

<span data-ttu-id="b7f65-123">&dagger;HTTP/2 bude v budoucí verzi podporován v macOS.</span><span class="sxs-lookup"><span data-stu-id="b7f65-123">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="b7f65-124">&Dagger;Kestrel má omezená podpora HTTP/2 na Windows Serveru 2012 R2 a Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="b7f65-124">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="b7f65-125">Podpora je omezená, protože seznam podporovaných šifrovacích sad TLS, které jsou k dispozici v těchto operačních systémech, je omezený.</span><span class="sxs-lookup"><span data-stu-id="b7f65-125">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="b7f65-126">Pro zabezpečení připojení TLS může být vyžadován certifikát vygenerovaný pomocí algoritmu ECDSA (s připojením typu eliptická křivka).</span><span class="sxs-lookup"><span data-stu-id="b7f65-126">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="b7f65-127">Pokud je navázáno připojení HTTP/2, `HTTP/2`sestavy [protokolu HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) .</span><span class="sxs-lookup"><span data-stu-id="b7f65-127">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="b7f65-128">HTTP/2 je ve výchozím nastavení zakázané.</span><span class="sxs-lookup"><span data-stu-id="b7f65-128">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="b7f65-129">Další informace o konfiguraci najdete v částech [Možnosti Kestrel](#kestrel-options) a [ListenOptions. Protocols](#listenoptionsprotocols) .</span><span class="sxs-lookup"><span data-stu-id="b7f65-129">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="b7f65-130">Kdy použít Kestrel s reverzním proxy serverem</span><span class="sxs-lookup"><span data-stu-id="b7f65-130">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="b7f65-131">Kestrel může používat sám sebe nebo s *reverzními proxy server*, jako je [Internetová informační služba (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="b7f65-131">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="b7f65-132">Reverzní proxy server přijímá požadavky HTTP ze sítě a přesměruje je na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="b7f65-132">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="b7f65-133">Kestrel použitý jako hraniční webový server (internetový):</span><span class="sxs-lookup"><span data-stu-id="b7f65-133">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel komunikuje přímo s internetem bez zpětného proxy server](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="b7f65-135">Kestrel se používá v konfiguraci reverzního proxy serveru:</span><span class="sxs-lookup"><span data-stu-id="b7f65-135">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel nepřímo komunikuje s internetem prostřednictvím reverzní proxy server, jako je IIS, Nginx nebo Apache.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="b7f65-137">Buď konfigurace, s zpětným proxy server nebo bez něj, je podporovaná konfigurace hostování.</span><span class="sxs-lookup"><span data-stu-id="b7f65-137">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="b7f65-138">Kestrel používané jako hraniční Server bez reverzních proxy server nepodporuje sdílení stejné IP adresy a portu mezi více procesy.</span><span class="sxs-lookup"><span data-stu-id="b7f65-138">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="b7f65-139">Když je Kestrel nakonfigurovaný k naslouchání na portu, Kestrel zpracovává veškerý provoz tohoto portu bez ohledu na hlavičky `Host`ch požadavků.</span><span class="sxs-lookup"><span data-stu-id="b7f65-139">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="b7f65-140">Reverzní proxy server, který může sdílet porty, má schopnost přesměrovat požadavky na Kestrel na jedinečné IP adresy a porty.</span><span class="sxs-lookup"><span data-stu-id="b7f65-140">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="b7f65-141">I v případě, že se zpětný proxy server nepožaduje, může být použití reverzní proxy server vhodnou volbou.</span><span class="sxs-lookup"><span data-stu-id="b7f65-141">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="b7f65-142">Reverzní proxy:</span><span class="sxs-lookup"><span data-stu-id="b7f65-142">A reverse proxy:</span></span>

* <span data-ttu-id="b7f65-143">Může omezit vystavenou veřejnou plochu v aplikacích, které hostuje.</span><span class="sxs-lookup"><span data-stu-id="b7f65-143">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="b7f65-144">Poskytněte další vrstvu konfigurace a obrany.</span><span class="sxs-lookup"><span data-stu-id="b7f65-144">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="b7f65-145">Může se lépe integrovat se stávající infrastrukturou.</span><span class="sxs-lookup"><span data-stu-id="b7f65-145">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="b7f65-146">Zjednodušte vyrovnávání zatížení a konfiguraci zabezpečené komunikace (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="b7f65-146">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="b7f65-147">Pouze reverzní proxy server vyžaduje certifikát X. 509 a tento server může komunikovat se servery aplikace v interní síti pomocí prostého protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="b7f65-147">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="b7f65-148">Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="b7f65-148">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="kestrel-in-aspnet-core-apps"></a><span data-ttu-id="b7f65-149">Kestrel v aplikacích ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b7f65-149">Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="b7f65-150">ASP.NET Core šablony projektu ve výchozím nastavení používají Kestrel.</span><span class="sxs-lookup"><span data-stu-id="b7f65-150">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="b7f65-151">V *program.cs*volá metoda <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span><span class="sxs-lookup"><span data-stu-id="b7f65-151">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="b7f65-152">Další informace o vytváření hostitele naleznete v části nastavení *hostitele* a *výchozí nastavení tvůrce* <xref:fundamentals/host/generic-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="b7f65-152">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="b7f65-153">K poskytnutí další konfigurace po volání `ConfigureWebHostDefaults`použijte `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="b7f65-153">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="b7f65-154">Kestrel možnosti</span><span class="sxs-lookup"><span data-stu-id="b7f65-154">Kestrel options</span></span>

<span data-ttu-id="b7f65-155">Webový server Kestrel má možnosti konfigurace omezení, které jsou zvláště užitečné v internetových nasazeních.</span><span class="sxs-lookup"><span data-stu-id="b7f65-155">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="b7f65-156">Nastavte omezení pro vlastnost <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> třídy <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="b7f65-156">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="b7f65-157">Vlastnost `Limits` obsahuje instanci třídy <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="b7f65-157">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="b7f65-158">V následujících příkladech je použit obor názvů <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:</span><span class="sxs-lookup"><span data-stu-id="b7f65-158">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="b7f65-159">V příkladech, které jsou uvedeny dále v tomto článku, jsou C# možnosti Kestrel konfigurovány v kódu.</span><span class="sxs-lookup"><span data-stu-id="b7f65-159">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="b7f65-160">Možnosti Kestrel lze také nastavit pomocí [poskytovatele konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="b7f65-160">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="b7f65-161">[Poskytovatel konfigurace souboru](xref:fundamentals/configuration/index#file-configuration-provider) může například načíst konfiguraci Kestrel z objektu *appSettings. JSON* nebo *appSettings. { Soubor Environment}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="b7f65-161">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

> [!NOTE]
> <span data-ttu-id="b7f65-162">Konfigurace <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> a [koncových bodů](#endpoint-configuration) se konfiguruje z poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b7f65-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](#endpoint-configuration) are configurable from configuration providers.</span></span> <span data-ttu-id="b7f65-163">Zbývající konfigurace Kestrel musí být nakonfigurovaná C# v kódu.</span><span class="sxs-lookup"><span data-stu-id="b7f65-163">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="b7f65-164">Použijte **jeden** z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="b7f65-164">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="b7f65-165">Konfigurace Kestrel v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b7f65-165">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="b7f65-166">Vloží instanci `IConfiguration` do `Startup` třídy.</span><span class="sxs-lookup"><span data-stu-id="b7f65-166">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="b7f65-167">Následující příklad předpokládá, že vložená konfigurace je přiřazena vlastnosti `Configuration`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-167">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="b7f65-168">V `Startup.ConfigureServices`načtěte oddíl `Kestrel` konfigurace do konfigurace Kestrel:</span><span class="sxs-lookup"><span data-stu-id="b7f65-168">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="b7f65-169">Nakonfigurovat Kestrel při sestavování hostitele:</span><span class="sxs-lookup"><span data-stu-id="b7f65-169">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="b7f65-170">V *program.cs*načtěte část `Kestrel` konfigurace do konfigurace Kestrel:</span><span class="sxs-lookup"><span data-stu-id="b7f65-170">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="b7f65-171">Obě předchozí přístupy fungují u libovolného [poskytovatele konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="b7f65-171">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="b7f65-172">Časový limit Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="b7f65-172">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="b7f65-173">Získá nebo nastaví [časový limit zachování](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="b7f65-173">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="b7f65-174">Výchozí hodnota je 2 minuty.</span><span class="sxs-lookup"><span data-stu-id="b7f65-174">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="b7f65-175">Maximální počet připojení klientů</span><span class="sxs-lookup"><span data-stu-id="b7f65-175">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="b7f65-176">Maximální počet souběžných otevřených připojení TCP lze pro celou aplikaci nastavit pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="b7f65-176">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="b7f65-177">Existuje zvláštní omezení pro připojení, která byla upgradována z protokolu HTTP nebo HTTPS na jiný protokol (například na žádost WebSockets).</span><span class="sxs-lookup"><span data-stu-id="b7f65-177">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="b7f65-178">Po upgradu připojení se nepočítá s limitem `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-178">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="b7f65-179">Ve výchozím nastavení je maximální počet připojení neomezené (null).</span><span class="sxs-lookup"><span data-stu-id="b7f65-179">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="b7f65-180">Maximální velikost textu požadavku</span><span class="sxs-lookup"><span data-stu-id="b7f65-180">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="b7f65-181">Výchozí maximální velikost textu požadavku je 30 000 000 bajtů, což je přibližně 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="b7f65-181">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="b7f65-182">Doporučený postup pro přepsání limitu v aplikaci ASP.NET Core MVC je použití atributu <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> u metody Action:</span><span class="sxs-lookup"><span data-stu-id="b7f65-182">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="b7f65-183">Tady je příklad, který ukazuje, jak nakonfigurovat omezení pro aplikaci na každém požadavku:</span><span class="sxs-lookup"><span data-stu-id="b7f65-183">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="b7f65-184">Přepsat nastavení u konkrétního požadavku v middleware:</span><span class="sxs-lookup"><span data-stu-id="b7f65-184">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="b7f65-185">Výjimka je vyvolána, pokud aplikace po zahájení aplikace nakonfiguruje limit na žádost.</span><span class="sxs-lookup"><span data-stu-id="b7f65-185">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="b7f65-186">Existuje vlastnost `IsReadOnly`, která určuje, zda je vlastnost `MaxRequestBodySize` ve stavu jen pro čtení, což znamená, že je příliš pozdě pro konfiguraci limitu.</span><span class="sxs-lookup"><span data-stu-id="b7f65-186">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="b7f65-187">Když je aplikace spouštěna [mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za [ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module), je omezení velikosti textu požadavku Kestrel zakázané, protože služba IIS tento limit již nastavuje.</span><span class="sxs-lookup"><span data-stu-id="b7f65-187">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="b7f65-188">Minimální rychlost těla zprávy požadavku</span><span class="sxs-lookup"><span data-stu-id="b7f65-188">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="b7f65-189">Kestrel kontroluje každou sekundu, pokud data přicházejí za zadanou rychlost v bajtech za sekundu.</span><span class="sxs-lookup"><span data-stu-id="b7f65-189">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="b7f65-190">Pokud frekvence klesne pod minimální hodnotu, vypršel časový limit připojení. Doba odkladu je doba, kterou Kestrel umožňuje klientovi zvýšit rychlost odesílání až na minimum; frekvence není během této doby kontrolována.</span><span class="sxs-lookup"><span data-stu-id="b7f65-190">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="b7f65-191">Doba odkladu pomáhá vyhnout se vyřazení připojení, která při počátečním posílání dat zpomalují rychlost, kvůli pomalému spuštění protokolu TCP.</span><span class="sxs-lookup"><span data-stu-id="b7f65-191">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="b7f65-192">Výchozí minimální sazba je 240 bajtů za sekundu s obdobím 5 sekund odkladu.</span><span class="sxs-lookup"><span data-stu-id="b7f65-192">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="b7f65-193">Pro odpověď platí také minimální sazba.</span><span class="sxs-lookup"><span data-stu-id="b7f65-193">A minimum rate also applies to the response.</span></span> <span data-ttu-id="b7f65-194">Kód pro nastavení limitu požadavku a limit odezvy je stejný, s výjimkou `RequestBody` nebo `Response` v názvech vlastností a rozhraní.</span><span class="sxs-lookup"><span data-stu-id="b7f65-194">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="b7f65-195">Tady je příklad, který ukazuje, jak nakonfigurovat minimální datové tarify v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="b7f65-195">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="b7f65-196">Přepsat minimální limity přenosové rychlosti na žádost v middlewaru:</span><span class="sxs-lookup"><span data-stu-id="b7f65-196">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="b7f65-197"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature>, na které se odkazuje v předchozí ukázce, není k dispozici v `HttpContext.Features` pro požadavky HTTP/2, protože Změna omezení přenosové rychlosti u jednotlivých požadavků je obecně Nepodporovaná pro HTTP/2 kvůli podpoře protokolu pro multiplexování požadavků.</span><span class="sxs-lookup"><span data-stu-id="b7f65-197">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="b7f65-198"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> však stále existují `HttpContext.Features` požadavků HTTP/2, protože omezení četnosti čtení je stále možné *úplně zakázat* na základě jednotlivých požadavků nastavením `IHttpMinRequestBodyDataRateFeature.MinDataRate` na `null` i pro požadavek HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="b7f65-198">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="b7f65-199">Při pokusu o čtení `IHttpMinRequestBodyDataRateFeature.MinDataRate` nebo pokusu o její nastavení na jinou hodnotu než `null` bude výsledkem `NotSupportedException` vyjímka požadavku HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="b7f65-199">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="b7f65-200">Omezení přenosové rychlosti pro všechny servery konfigurovaná prostřednictvím `KestrelServerOptions.Limits` se pořád vztahují na připojení HTTP/1. x i HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="b7f65-200">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="b7f65-201">Časový limit hlaviček žádostí</span><span class="sxs-lookup"><span data-stu-id="b7f65-201">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="b7f65-202">Získá nebo nastaví maximální dobu, po kterou server stráví příjem hlaviček požadavků.</span><span class="sxs-lookup"><span data-stu-id="b7f65-202">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="b7f65-203">Výchozí hodnota je 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="b7f65-203">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="b7f65-204">Maximální počet proudů na připojení</span><span class="sxs-lookup"><span data-stu-id="b7f65-204">Maximum streams per connection</span></span>

<span data-ttu-id="b7f65-205">`Http2.MaxStreamsPerConnection` omezuje počet souběžných datových proudů požadavků na připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="b7f65-205">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="b7f65-206">Nadbytečné proudy jsou odmítnuty.</span><span class="sxs-lookup"><span data-stu-id="b7f65-206">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="b7f65-207">Výchozí hodnota je 100.</span><span class="sxs-lookup"><span data-stu-id="b7f65-207">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="b7f65-208">Velikost tabulky hlaviček</span><span class="sxs-lookup"><span data-stu-id="b7f65-208">Header table size</span></span>

<span data-ttu-id="b7f65-209">Dekodér HPACK dekomprimuje hlavičky HTTP pro připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="b7f65-209">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="b7f65-210">`Http2.HeaderTableSize` omezuje velikost kompresní tabulky hlaviček, kterou používá dekodér HPACK.</span><span class="sxs-lookup"><span data-stu-id="b7f65-210">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="b7f65-211">Hodnota je uvedena v oktetech a musí být větší než nula (0).</span><span class="sxs-lookup"><span data-stu-id="b7f65-211">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="b7f65-212">Výchozí hodnota je 4096.</span><span class="sxs-lookup"><span data-stu-id="b7f65-212">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="b7f65-213">Maximální velikost rámce</span><span class="sxs-lookup"><span data-stu-id="b7f65-213">Maximum frame size</span></span>

<span data-ttu-id="b7f65-214">`Http2.MaxFrameSize` určuje maximální povolenou velikost datové části rámce připojení HTTP/2 přijatého nebo odesílaného serverem.</span><span class="sxs-lookup"><span data-stu-id="b7f65-214">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="b7f65-215">Hodnota je uvedena v oktetech a musí být v rozmezí 2 ^ 14 (16 384) až 2 ^ 24-1 (16 777 215).</span><span class="sxs-lookup"><span data-stu-id="b7f65-215">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="b7f65-216">Výchozí hodnota je 2 ^ 14 (16 384).</span><span class="sxs-lookup"><span data-stu-id="b7f65-216">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="b7f65-217">Maximální velikost hlavičky požadavku</span><span class="sxs-lookup"><span data-stu-id="b7f65-217">Maximum request header size</span></span>

<span data-ttu-id="b7f65-218">`Http2.MaxRequestHeaderFieldSize` určuje maximální povolenou velikost v oktetech hodnot hlaviček požadavku.</span><span class="sxs-lookup"><span data-stu-id="b7f65-218">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="b7f65-219">Toto omezení se vztahuje na název i hodnotu v jejich komprimovaných a nekomprimovaných reprezentujcích.</span><span class="sxs-lookup"><span data-stu-id="b7f65-219">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="b7f65-220">Hodnota musí být větší než nula (0).</span><span class="sxs-lookup"><span data-stu-id="b7f65-220">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="b7f65-221">Výchozí hodnota je 8 192.</span><span class="sxs-lookup"><span data-stu-id="b7f65-221">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="b7f65-222">Velikost okna počátečního připojení</span><span class="sxs-lookup"><span data-stu-id="b7f65-222">Initial connection window size</span></span>

<span data-ttu-id="b7f65-223">`Http2.InitialConnectionWindowSize` určuje maximální velikost textu požadavku v bajtech, které jsou v jednom okamžiku agregovány v rámci všech požadavků (datových proudů) na připojení.</span><span class="sxs-lookup"><span data-stu-id="b7f65-223">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="b7f65-224">Žádosti jsou také omezeny `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-224">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="b7f65-225">Hodnota musí být větší než nebo rovna 65 535 a menší než 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="b7f65-225">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="b7f65-226">Výchozí hodnota je 128 KB (131 072).</span><span class="sxs-lookup"><span data-stu-id="b7f65-226">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="b7f65-227">Počáteční velikost okna datového proudu</span><span class="sxs-lookup"><span data-stu-id="b7f65-227">Initial stream window size</span></span>

<span data-ttu-id="b7f65-228">`Http2.InitialStreamWindowSize` určuje maximální množství údajů o textu požadavku v bajtech, které jsou ukládány do vyrovnávací paměti serveru na jednu žádost (datový proud).</span><span class="sxs-lookup"><span data-stu-id="b7f65-228">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="b7f65-229">Žádosti jsou také omezeny `Http2.InitialConnectionWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-229">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="b7f65-230">Hodnota musí být větší než nebo rovna 65 535 a menší než 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="b7f65-230">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="b7f65-231">Výchozí hodnota je 96 KB (98 304).</span><span class="sxs-lookup"><span data-stu-id="b7f65-231">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="b7f65-232">Synchronní v/v</span><span class="sxs-lookup"><span data-stu-id="b7f65-232">Synchronous IO</span></span>

<span data-ttu-id="b7f65-233"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> určuje, zda je pro požadavek a odpověď povolena synchronní v/v.</span><span class="sxs-lookup"><span data-stu-id="b7f65-233"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="b7f65-234">Výchozí hodnota je `false`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-234">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="b7f65-235">Velký počet blokování synchronních vstupně-výstupních operací může vést ke vyčerpání fondu vláken, což aplikaci nereaguje.</span><span class="sxs-lookup"><span data-stu-id="b7f65-235">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="b7f65-236">Povolit `AllowSynchronousIO` jenom při použití knihovny, která nepodporuje asynchronní vstupně-výstupní operace.</span><span class="sxs-lookup"><span data-stu-id="b7f65-236">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="b7f65-237">Následující příklad povoluje synchronní vstupně-výstupní operace:</span><span class="sxs-lookup"><span data-stu-id="b7f65-237">The following example enables synchronous IO:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="b7f65-238">Informace o dalších možnostech a omezeních Kestrel najdete v tématech:</span><span class="sxs-lookup"><span data-stu-id="b7f65-238">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="b7f65-239">Konfigurace koncového bodu</span><span class="sxs-lookup"><span data-stu-id="b7f65-239">Endpoint configuration</span></span>

<span data-ttu-id="b7f65-240">Ve výchozím nastavení ASP.NET Core váže k:</span><span class="sxs-lookup"><span data-stu-id="b7f65-240">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="b7f65-241">`https://localhost:5001` (Pokud je k dispozici místní vývojový certifikát)</span><span class="sxs-lookup"><span data-stu-id="b7f65-241">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="b7f65-242">Zadejte adresy URL pomocí:</span><span class="sxs-lookup"><span data-stu-id="b7f65-242">Specify URLs using the:</span></span>

* <span data-ttu-id="b7f65-243">`ASPNETCORE_URLS` proměnnou prostředí.</span><span class="sxs-lookup"><span data-stu-id="b7f65-243">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="b7f65-244">`--urls` argument příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="b7f65-244">`--urls` command-line argument.</span></span>
* <span data-ttu-id="b7f65-245">`urls` konfigurační klíč hostitele.</span><span class="sxs-lookup"><span data-stu-id="b7f65-245">`urls` host configuration key.</span></span>
* <span data-ttu-id="b7f65-246">`UseUrls` metoda rozšíření.</span><span class="sxs-lookup"><span data-stu-id="b7f65-246">`UseUrls` extension method.</span></span>

<span data-ttu-id="b7f65-247">Hodnota zadaná pomocí těchto přístupů může být jeden nebo více koncových bodů HTTP a HTTPS (HTTPS, pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="b7f65-247">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="b7f65-248">Nakonfigurujte hodnotu jako seznam oddělený středníkem (například `"Urls": "http://localhost:8000; http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="b7f65-248">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="b7f65-249">Další informace o těchto přístupůch najdete v tématu [adresy URL serveru](xref:fundamentals/host/web-host#server-urls) a [potlačení konfigurace](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="b7f65-249">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="b7f65-250">Vytvoří se vývojový certifikát:</span><span class="sxs-lookup"><span data-stu-id="b7f65-250">A development certificate is created:</span></span>

* <span data-ttu-id="b7f65-251">Po instalaci [.NET Core SDK](/dotnet/core/sdk) .</span><span class="sxs-lookup"><span data-stu-id="b7f65-251">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="b7f65-252">[Nástroj pro vývoj a certifikáty](xref:aspnetcore-2.1#https) slouží k vytvoření certifikátu.</span><span class="sxs-lookup"><span data-stu-id="b7f65-252">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="b7f65-253">Některé prohlížeče vyžadují udělení explicitního oprávnění pro důvěřování místního vývojového certifikátu.</span><span class="sxs-lookup"><span data-stu-id="b7f65-253">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="b7f65-254">Šablony projektů konfigurují aplikace tak, aby běžely ve výchozím nastavení HTTPS a zahrnovaly [přesměrování https a podporu HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="b7f65-254">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="b7f65-255">Pokud chcete nakonfigurovat předpony a porty adres URL pro Kestrel, volejte <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> nebo <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> metody na <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="b7f65-255">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="b7f65-256">`UseUrls`, argument příkazového řádku `--urls`, `urls` konfigurační klíč hostitele a proměnná prostředí `ASPNETCORE_URLS` fungují, ale mají omezení zaznamenaná později v této části (pro konfiguraci koncového bodu HTTPS musí být k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="b7f65-256">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="b7f65-257">Konfigurace `KestrelServerOptions`:</span><span class="sxs-lookup"><span data-stu-id="b7f65-257">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="b7f65-258">ConfigureEndpointDefaults (Action\<ListenOptions >)</span><span class="sxs-lookup"><span data-stu-id="b7f65-258">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="b7f65-259">Určuje `Action` konfigurace, která se má spustit pro každý zadaný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="b7f65-259">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="b7f65-260">Volání `ConfigureEndpointDefaults` několikrát nahrazuje předchozí `Action`s uvedením posledního `Action`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-260">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.ConfigureEndpointDefaults(listenOptions =>
    {
        // Configure endpoint defaults
    });
});
```

> [!NOTE]
> <span data-ttu-id="b7f65-261">Koncové body vytvořené voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **před** voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> nebudou použity výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="b7f65-261">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="b7f65-262">ConfigureHttpsDefaults (Action\<HttpsConnectionAdapterOptions >)</span><span class="sxs-lookup"><span data-stu-id="b7f65-262">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="b7f65-263">Určuje `Action` konfigurace, která se má spustit pro každý koncový bod HTTPS.</span><span class="sxs-lookup"><span data-stu-id="b7f65-263">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="b7f65-264">Volání `ConfigureHttpsDefaults` několikrát nahrazuje předchozí `Action`s uvedením posledního `Action`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-264">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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

> [!NOTE]
> <span data-ttu-id="b7f65-265">Koncové body vytvořené voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **před** voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> nebudou použity výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="b7f65-265">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="b7f65-266">Konfigurace (IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="b7f65-266">Configure(IConfiguration)</span></span>

<span data-ttu-id="b7f65-267">Vytvoří zavaděč konfigurace pro nastavení Kestrel, který jako vstup převezme <xref:Microsoft.Extensions.Configuration.IConfiguration>.</span><span class="sxs-lookup"><span data-stu-id="b7f65-267">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="b7f65-268">Konfigurace musí být vymezená na konfigurační oddíl pro Kestrel.</span><span class="sxs-lookup"><span data-stu-id="b7f65-268">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="b7f65-269">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="b7f65-269">ListenOptions.UseHttps</span></span>

<span data-ttu-id="b7f65-270">Nakonfigurujte Kestrel tak, aby používal protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="b7f65-270">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="b7f65-271">rozšíření `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="b7f65-271">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="b7f65-272">`UseHttps` &ndash; nakonfigurovat Kestrel na používání protokolu HTTPS s výchozím certifikátem.</span><span class="sxs-lookup"><span data-stu-id="b7f65-272">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="b7f65-273">Vyvolá výjimku, pokud není nakonfigurován žádný výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="b7f65-273">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="b7f65-274">parametry `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="b7f65-274">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="b7f65-275">`filename` je cesta a název souboru certifikátu vzhledem k adresáři, který obsahuje soubory obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="b7f65-275">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="b7f65-276">`password` je heslo vyžadované pro přístup k datům certifikátu X. 509.</span><span class="sxs-lookup"><span data-stu-id="b7f65-276">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="b7f65-277">`configureOptions` je `Action` ke konfiguraci `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-277">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="b7f65-278">Vrátí `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-278">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="b7f65-279">`storeName` je úložiště certifikátů, ze kterého se má certifikát načíst.</span><span class="sxs-lookup"><span data-stu-id="b7f65-279">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="b7f65-280">`subject` je název subjektu certifikátu.</span><span class="sxs-lookup"><span data-stu-id="b7f65-280">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="b7f65-281">`allowInvalid` určuje, jestli by se měly brát v úvahu neplatné certifikáty, například certifikáty podepsané svým držitelem.</span><span class="sxs-lookup"><span data-stu-id="b7f65-281">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="b7f65-282">`location` je umístění úložiště, ze kterého se má načíst certifikát.</span><span class="sxs-lookup"><span data-stu-id="b7f65-282">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="b7f65-283">`serverCertificate` je certifikát X. 509.</span><span class="sxs-lookup"><span data-stu-id="b7f65-283">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="b7f65-284">V produkčním prostředí musí být HTTPS explicitně nakonfigurovaný.</span><span class="sxs-lookup"><span data-stu-id="b7f65-284">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="b7f65-285">Musí být zadán minimálně výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="b7f65-285">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="b7f65-286">Podporované konfigurace popsané dál:</span><span class="sxs-lookup"><span data-stu-id="b7f65-286">Supported configurations described next:</span></span>

* <span data-ttu-id="b7f65-287">Žádná konfigurace</span><span class="sxs-lookup"><span data-stu-id="b7f65-287">No configuration</span></span>
* <span data-ttu-id="b7f65-288">Nahradit výchozí certifikát z konfigurace</span><span class="sxs-lookup"><span data-stu-id="b7f65-288">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="b7f65-289">Změna výchozích hodnot v kódu</span><span class="sxs-lookup"><span data-stu-id="b7f65-289">Change the defaults in code</span></span>

<span data-ttu-id="b7f65-290">*Žádná konfigurace*</span><span class="sxs-lookup"><span data-stu-id="b7f65-290">*No configuration*</span></span>

<span data-ttu-id="b7f65-291">Kestrel naslouchá na `http://localhost:5000` a `https://localhost:5001` (Pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="b7f65-291">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="b7f65-292">*Nahradit výchozí certifikát z konfigurace*</span><span class="sxs-lookup"><span data-stu-id="b7f65-292">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="b7f65-293">`CreateDefaultBuilder` ve výchozím nastavení volá `Configure(context.Configuration.GetSection("Kestrel"))`, aby se načetla konfigurace Kestrel.</span><span class="sxs-lookup"><span data-stu-id="b7f65-293">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="b7f65-294">Pro Kestrel je k dispozici výchozí schéma konfigurace nastavení aplikace HTTPS.</span><span class="sxs-lookup"><span data-stu-id="b7f65-294">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="b7f65-295">Nakonfigurujte několik koncových bodů, včetně adres URL a certifikátů, které se mají použít, buď ze souboru na disku, nebo z úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="b7f65-295">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="b7f65-296">V následujícím příkladu *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="b7f65-296">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="b7f65-297">Nastavte **AllowInvalid** na `true`, aby bylo možné povolit použití neplatných certifikátů (například certifikátů podepsaných svým držitelem).</span><span class="sxs-lookup"><span data-stu-id="b7f65-297">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="b7f65-298">Libovolný koncový bod HTTPS, který neurčuje certifikát (**HttpsDefaultCert** v následujícím příkladu), se vrátí k certifikátu definovanému v části **certifikáty** > **výchozím** nebo vývojovým certifikátem.</span><span class="sxs-lookup"><span data-stu-id="b7f65-298">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="b7f65-299">Alternativou k použití **cesty** a **hesla** pro libovolný uzel certifikátu je zadání certifikátu pomocí polí úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="b7f65-299">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="b7f65-300">Například **certifikáty** > **výchozí** certifikát lze zadat jako:</span><span class="sxs-lookup"><span data-stu-id="b7f65-300">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="b7f65-301">Poznámky ke schématu:</span><span class="sxs-lookup"><span data-stu-id="b7f65-301">Schema notes:</span></span>

* <span data-ttu-id="b7f65-302">V názvech koncových bodů se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="b7f65-302">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="b7f65-303">Například `HTTPS` a `Https` jsou platné.</span><span class="sxs-lookup"><span data-stu-id="b7f65-303">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="b7f65-304">U každého koncového bodu je vyžadován parametr `Url`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-304">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="b7f65-305">Formát pro tento parametr je stejný jako konfigurační parametr `Urls` nejvyšší úrovně s tím rozdílem, že je omezen na jedinou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="b7f65-305">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="b7f65-306">Tyto koncové body nahrazují hodnoty definované v konfiguraci `Urls` na nejvyšší úrovni místo jejich přidávání do těchto koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="b7f65-306">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="b7f65-307">Koncové body definované v kódu prostřednictvím `Listen` jsou kumulativní s použitím koncových bodů definovaných v konfiguračním oddílu.</span><span class="sxs-lookup"><span data-stu-id="b7f65-307">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="b7f65-308">Oddíl `Certificate` je nepovinný.</span><span class="sxs-lookup"><span data-stu-id="b7f65-308">The `Certificate` section is optional.</span></span> <span data-ttu-id="b7f65-309">Pokud není zadaný oddíl `Certificate`, použijí se výchozí hodnoty definované v dřívějších scénářích.</span><span class="sxs-lookup"><span data-stu-id="b7f65-309">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="b7f65-310">Pokud nejsou k dispozici žádné výchozí hodnoty, Server vyvolá výjimku a nemůže se spustit.</span><span class="sxs-lookup"><span data-stu-id="b7f65-310">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="b7f65-311">Oddíl `Certificate` podporuje jak **cesty**&ndash;**hesla** , tak&ndash;ho **úložiště** **subjektu** .</span><span class="sxs-lookup"><span data-stu-id="b7f65-311">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="b7f65-312">V takovém případě může být definován libovolný počet koncových bodů, pokud to nezpůsobí konflikty portů.</span><span class="sxs-lookup"><span data-stu-id="b7f65-312">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="b7f65-313">`options.Configure(context.Configuration.GetSection("{SECTION}"))` vrátí `KestrelConfigurationLoader` s metodou `.Endpoint(string name, listenOptions => { })`, která se dá použít k doplnění nastavení nakonfigurovaného koncového bodu:</span><span class="sxs-lookup"><span data-stu-id="b7f65-313">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="b7f65-314">k pokračování v iteraci u stávajícího zavaděče, jako je třeba ten, který poskytuje <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, se dá `KestrelServerOptions.ConfigurationLoader` použít přímo.</span><span class="sxs-lookup"><span data-stu-id="b7f65-314">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="b7f65-315">Konfigurační oddíl pro každý koncový bod je k dispozici na možnostech v metodě `Endpoint`, aby bylo možné číst vlastní nastavení.</span><span class="sxs-lookup"><span data-stu-id="b7f65-315">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="b7f65-316">Více konfigurací může být načteno voláním `options.Configure(context.Configuration.GetSection("{SECTION}"))` znovu s jiným oddílem.</span><span class="sxs-lookup"><span data-stu-id="b7f65-316">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="b7f65-317">Použije se jenom poslední konfigurace, pokud se `Load` explicitně nevolá při předchozích instancích.</span><span class="sxs-lookup"><span data-stu-id="b7f65-317">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="b7f65-318">Metapackage nevolá `Load`, aby se jeho výchozí konfigurační oddíl mohl nahradit.</span><span class="sxs-lookup"><span data-stu-id="b7f65-318">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="b7f65-319">`KestrelConfigurationLoader` zrcadlí `Listen` rodinu rozhraní API `KestrelServerOptions` jako `Endpoint` přetížení, takže koncové body kódu a konfigurace lze konfigurovat na stejném místě.</span><span class="sxs-lookup"><span data-stu-id="b7f65-319">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="b7f65-320">Tato přetížení nepoužívají názvy a využívají pouze výchozí nastavení z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b7f65-320">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="b7f65-321">*Změna výchozích hodnot v kódu*</span><span class="sxs-lookup"><span data-stu-id="b7f65-321">*Change the defaults in code*</span></span>

<span data-ttu-id="b7f65-322">`ConfigureEndpointDefaults` a `ConfigureHttpsDefaults` lze použít ke změně výchozího nastavení pro `ListenOptions` a `HttpsConnectionAdapterOptions`, včetně přepsání výchozího certifikátu zadaného v předchozím scénáři.</span><span class="sxs-lookup"><span data-stu-id="b7f65-322">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="b7f65-323">`ConfigureEndpointDefaults` a `ConfigureHttpsDefaults` by měly být volány před konfigurací koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="b7f65-323">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="b7f65-324">*Podpora Kestrel pro SNI*</span><span class="sxs-lookup"><span data-stu-id="b7f65-324">*Kestrel support for SNI*</span></span>

<span data-ttu-id="b7f65-325">[Indikace názvu serveru (SNI)](https://tools.ietf.org/html/rfc6066#section-3) lze použít k hostování více domén na stejné IP adrese a portu.</span><span class="sxs-lookup"><span data-stu-id="b7f65-325">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="b7f65-326">Aby SNI fungovalo, klient pošle název hostitele pro zabezpečenou relaci serveru během metody handshake TLS, aby server mohl poskytovat správný certifikát.</span><span class="sxs-lookup"><span data-stu-id="b7f65-326">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="b7f65-327">Klient používá poskytnutý certifikát pro šifrovanou komunikaci se serverem během zabezpečené relace, která následuje za metodou handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="b7f65-327">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="b7f65-328">Kestrel podporuje SNI prostřednictvím zpětného volání `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-328">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="b7f65-329">Zpětné volání je vyvoláno jednou za připojení, které aplikaci umožní zkontrolovat název hostitele a vybrat příslušný certifikát.</span><span class="sxs-lookup"><span data-stu-id="b7f65-329">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="b7f65-330">Podpora SNI vyžaduje:</span><span class="sxs-lookup"><span data-stu-id="b7f65-330">SNI support requires:</span></span>

* <span data-ttu-id="b7f65-331">Spuštění v cílovém rozhraní `netcoreapp2.1` nebo novějším.</span><span class="sxs-lookup"><span data-stu-id="b7f65-331">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="b7f65-332">V `net461` nebo novějším je zpětné volání vyvoláno, ale `name` je vždy `null`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-332">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="b7f65-333">`name` taky `null`, pokud klient neposkytne parametr názvu hostitele v handshaki TLS.</span><span class="sxs-lookup"><span data-stu-id="b7f65-333">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="b7f65-334">Všechny weby běží na stejné instanci Kestrel.</span><span class="sxs-lookup"><span data-stu-id="b7f65-334">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="b7f65-335">Kestrel nepodporuje sdílení IP adresy a portu napříč několika instancemi bez reverzního proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="b7f65-335">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="b7f65-336">Protokolování připojení</span><span class="sxs-lookup"><span data-stu-id="b7f65-336">Connection logging</span></span>

<span data-ttu-id="b7f65-337">Volání <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> k vygenerování protokolů na úrovni ladění pro komunikaci na úrovni bajtů v rámci připojení.</span><span class="sxs-lookup"><span data-stu-id="b7f65-337">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="b7f65-338">Protokolování připojení je užitečné při řešení problémů v komunikaci nízké úrovně, například během šifrování TLS a za proxy servery.</span><span class="sxs-lookup"><span data-stu-id="b7f65-338">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="b7f65-339">Pokud je `UseConnectionLogging` před `UseHttps`, zaprotokoluje se zašifrovaný provoz.</span><span class="sxs-lookup"><span data-stu-id="b7f65-339">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="b7f65-340">Je-li po `UseHttps`umístěn `UseConnectionLogging`, zašifrovaný provoz se zaznamená do protokolu.</span><span class="sxs-lookup"><span data-stu-id="b7f65-340">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="b7f65-341">Vytvoření vazby na soket TCP</span><span class="sxs-lookup"><span data-stu-id="b7f65-341">Bind to a TCP socket</span></span>

<span data-ttu-id="b7f65-342">Metoda <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> se váže k soketu TCP a obslužná rutina možností umožňuje konfiguraci certifikátu X. 509:</span><span class="sxs-lookup"><span data-stu-id="b7f65-342">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="b7f65-343">Příklad konfiguruje HTTPS pro koncový bod s <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="b7f65-343">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="b7f65-344">Ke konfiguraci dalších nastavení Kestrel pro konkrétní koncové body použijte stejné rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="b7f65-344">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="b7f65-345">Vytvoření vazby na soket UNIX</span><span class="sxs-lookup"><span data-stu-id="b7f65-345">Bind to a Unix socket</span></span>

<span data-ttu-id="b7f65-346">Naslouchat na soketu UNIX pomocí <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> pro zlepšení výkonu pomocí Nginx, jak je znázorněno v tomto příkladu:</span><span class="sxs-lookup"><span data-stu-id="b7f65-346">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="b7f65-347">V konfiguračním souboru Nginx nastavte  > `server``location`položka  > `proxy_pass`.`http://unix:/tmp/{KESTREL SOCKET}:/;`</span><span class="sxs-lookup"><span data-stu-id="b7f65-347">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="b7f65-348">`{KESTREL SOCKET}` je název soketu, který je k dispozici pro <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (například `kestrel-test.sock` v předchozím příkladu).</span><span class="sxs-lookup"><span data-stu-id="b7f65-348">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="b7f65-349">Zajistěte, aby byl soket zapisovatelný pomocí Nginx (například `chmod go+w /tmp/kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="b7f65-349">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

### <a name="port-0"></a><span data-ttu-id="b7f65-350">Port 0</span><span class="sxs-lookup"><span data-stu-id="b7f65-350">Port 0</span></span>

<span data-ttu-id="b7f65-351">Když je zadané číslo portu `0`, Kestrel se dynamicky váže k dostupnému portu.</span><span class="sxs-lookup"><span data-stu-id="b7f65-351">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="b7f65-352">Následující příklad ukazuje, jak určit, který port Kestrel je skutečně vázaný za běhu:</span><span class="sxs-lookup"><span data-stu-id="b7f65-352">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="b7f65-353">Když se aplikace spustí, výstup okna konzoly indikuje dynamický port, na který se aplikace dá získat:</span><span class="sxs-lookup"><span data-stu-id="b7f65-353">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="b7f65-354">Omezení</span><span class="sxs-lookup"><span data-stu-id="b7f65-354">Limitations</span></span>

<span data-ttu-id="b7f65-355">Konfigurace koncových bodů pomocí následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="b7f65-355">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="b7f65-356">`--urls` argument příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="b7f65-356">`--urls` command-line argument</span></span>
* <span data-ttu-id="b7f65-357">`urls` konfigurační klíč hostitele</span><span class="sxs-lookup"><span data-stu-id="b7f65-357">`urls` host configuration key</span></span>
* <span data-ttu-id="b7f65-358">`ASPNETCORE_URLS` proměnná prostředí</span><span class="sxs-lookup"><span data-stu-id="b7f65-358">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="b7f65-359">Tyto metody jsou užitečné, pokud chcete, aby kód pracoval na jiných serverech než Kestrel.</span><span class="sxs-lookup"><span data-stu-id="b7f65-359">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="b7f65-360">Mějte ale na paměti následující omezení:</span><span class="sxs-lookup"><span data-stu-id="b7f65-360">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="b7f65-361">Protokol HTTPS nelze s těmito přístupy použít, pokud není k dispozici výchozí certifikát v konfiguraci koncového bodu HTTPS (například pomocí konfigurace `KestrelServerOptions` nebo konfiguračního souboru, jak je uvedeno výše v tomto tématu).</span><span class="sxs-lookup"><span data-stu-id="b7f65-361">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="b7f65-362">Při současném použití `Listen` i `UseUrls` se používají koncové body `Listen` `UseUrls` koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="b7f65-362">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="b7f65-363">Konfigurace koncového bodu služby IIS</span><span class="sxs-lookup"><span data-stu-id="b7f65-363">IIS endpoint configuration</span></span>

<span data-ttu-id="b7f65-364">Při použití služby IIS jsou vazby adresy URL pro přepsání vazby služby IIS nastaveny buď `Listen`, nebo `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-364">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="b7f65-365">Další informace najdete v tématu [ASP.NET Core modulu](xref:host-and-deploy/aspnet-core-module) .</span><span class="sxs-lookup"><span data-stu-id="b7f65-365">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="b7f65-366">ListenOptions. Protocols</span><span class="sxs-lookup"><span data-stu-id="b7f65-366">ListenOptions.Protocols</span></span>

<span data-ttu-id="b7f65-367">Vlastnost `Protocols` vytváří protokoly HTTP (`HttpProtocols`) povolené pro koncový bod připojení nebo pro server.</span><span class="sxs-lookup"><span data-stu-id="b7f65-367">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="b7f65-368">Přiřaďte hodnotu vlastnosti `Protocols` z výčtu `HttpProtocols`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-368">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="b7f65-369">hodnota výčtu `HttpProtocols`</span><span class="sxs-lookup"><span data-stu-id="b7f65-369">`HttpProtocols` enum value</span></span> | <span data-ttu-id="b7f65-370">Povolený protokol připojení</span><span class="sxs-lookup"><span data-stu-id="b7f65-370">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="b7f65-371">Pouze HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="b7f65-371">HTTP/1.1 only.</span></span> <span data-ttu-id="b7f65-372">Dá se použít s TLS nebo bez něj.</span><span class="sxs-lookup"><span data-stu-id="b7f65-372">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="b7f65-373">Pouze HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="b7f65-373">HTTP/2 only.</span></span> <span data-ttu-id="b7f65-374">Dá se použít bez TLS jenom v případě, že klient podporuje [předchozí režim znalostní báze](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="b7f65-374">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="b7f65-375">HTTP/1.1 a HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="b7f65-375">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="b7f65-376">HTTP/2 vyžaduje, aby klient v rámci metody handshake protokolu TLS [(ALPN)](https://tools.ietf.org/html/rfc7301#section-3) vybral http/2. v opačném případě se připojení nastaví jako výchozí HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="b7f65-376">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="b7f65-377">Výchozí hodnota `ListenOptions.Protocols` pro libovolný koncový bod je `HttpProtocols.Http1AndHttp2`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-377">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="b7f65-378">Omezení TLS pro HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="b7f65-378">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="b7f65-379">TLS verze 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="b7f65-379">TLS version 1.2 or later</span></span>
* <span data-ttu-id="b7f65-380">Opětovné vyjednávání je zakázané.</span><span class="sxs-lookup"><span data-stu-id="b7f65-380">Renegotiation disabled</span></span>
* <span data-ttu-id="b7f65-381">Komprese vypnuta</span><span class="sxs-lookup"><span data-stu-id="b7f65-381">Compression disabled</span></span>
* <span data-ttu-id="b7f65-382">Minimální velikosti dočasného výměny klíčů:</span><span class="sxs-lookup"><span data-stu-id="b7f65-382">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="b7f65-383">Eliptická křivka Diffie-Hellman (ECDH) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bity minima</span><span class="sxs-lookup"><span data-stu-id="b7f65-383">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum</span></span>
  * <span data-ttu-id="b7f65-384">Omezené pole Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bity minima</span><span class="sxs-lookup"><span data-stu-id="b7f65-384">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bits minimum</span></span>
* <span data-ttu-id="b7f65-385">Šifrovací sada není zakázaná.</span><span class="sxs-lookup"><span data-stu-id="b7f65-385">Cipher suite not blacklisted</span></span>

<span data-ttu-id="b7f65-386">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; s eliptickou křivkou P-256 &lbrack;`FIPS186`&rbrack; je ve výchozím nastavení podporován.</span><span class="sxs-lookup"><span data-stu-id="b7f65-386">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="b7f65-387">Následující příklad povoluje připojení HTTP/1.1 a HTTP/2 na portu 8000.</span><span class="sxs-lookup"><span data-stu-id="b7f65-387">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="b7f65-388">Připojení TLS zabezpečuje pomocí zadaného certifikátu:</span><span class="sxs-lookup"><span data-stu-id="b7f65-388">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="b7f65-389">Pomocí middleware připojení můžete v případě potřeby filtrovat handshake na základě jednotlivých připojení pro konkrétní šifry.</span><span class="sxs-lookup"><span data-stu-id="b7f65-389">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="b7f65-390">Následující příklad vyvolá <xref:System.NotSupportedException> pro jakýkoli šifrovací algoritmus, který aplikace nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="b7f65-390">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="b7f65-391">Případně můžete definovat a porovnat [ITlsHandshakeFeature. CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) se seznamem přijatelných šifrovacích sad.</span><span class="sxs-lookup"><span data-stu-id="b7f65-391">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="b7f65-392">Pro šifrovací algoritmus [CipherAlgorithmType. null](xref:System.Security.Authentication.CipherAlgorithmType) se nepoužívá žádné šifrování.</span><span class="sxs-lookup"><span data-stu-id="b7f65-392">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

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

<span data-ttu-id="b7f65-393">Filtrování připojení lze také nakonfigurovat prostřednictvím <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span><span class="sxs-lookup"><span data-stu-id="b7f65-393">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

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

<span data-ttu-id="b7f65-394">V systému Linux lze pomocí <xref:System.Net.Security.CipherSuitesPolicy> filtrovat handshake TLS na jednotlivých připojeních:</span><span class="sxs-lookup"><span data-stu-id="b7f65-394">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

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

<span data-ttu-id="b7f65-395">*Nastavit protokol z konfigurace*</span><span class="sxs-lookup"><span data-stu-id="b7f65-395">*Set the protocol from configuration*</span></span>

<span data-ttu-id="b7f65-396">`CreateDefaultBuilder` ve výchozím nastavení volá `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))`, aby se načetla konfigurace Kestrel.</span><span class="sxs-lookup"><span data-stu-id="b7f65-396">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="b7f65-397">Následující příklad *appSettings. JSON* vytvoří HTTP/1.1 jako výchozí protokol připojení pro všechny koncové body:</span><span class="sxs-lookup"><span data-stu-id="b7f65-397">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="b7f65-398">Následující příklad *appSettings. JSON* vytvoří protokol připojení HTTP/1.1 pro konkrétní koncový bod:</span><span class="sxs-lookup"><span data-stu-id="b7f65-398">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="b7f65-399">Protokoly zadané v hodnotách přepisu kódu nastavených podle konfigurace</span><span class="sxs-lookup"><span data-stu-id="b7f65-399">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="b7f65-400">Konfigurace přenosu</span><span class="sxs-lookup"><span data-stu-id="b7f65-400">Transport configuration</span></span>

<span data-ttu-id="b7f65-401">Pro projekty, které vyžadují použití Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span><span class="sxs-lookup"><span data-stu-id="b7f65-401">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span></span>

* <span data-ttu-id="b7f65-402">Přidejte do souboru projektu aplikace závislost pro balíček [Microsoft. AspNetCore. Server. Kestrel. Transport. Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) :</span><span class="sxs-lookup"><span data-stu-id="b7f65-402">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* <span data-ttu-id="b7f65-403">Zavolejte <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> na `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="b7f65-403">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> on the `IWebHostBuilder`:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="b7f65-404">Předpony adresy URL</span><span class="sxs-lookup"><span data-stu-id="b7f65-404">URL prefixes</span></span>

<span data-ttu-id="b7f65-405">Při použití `UseUrls`, `--urls` argumentu příkazového řádku, `urls` konfigurační klíč hostitele nebo proměnná prostředí `ASPNETCORE_URLS`, můžou být předpony adresy URL v některém z následujících formátů.</span><span class="sxs-lookup"><span data-stu-id="b7f65-405">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="b7f65-406">Platné jsou pouze předpony adresy URL protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="b7f65-406">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="b7f65-407">Kestrel nepodporuje protokol HTTPS při konfiguraci vazeb URL pomocí `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-407">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="b7f65-408">Adresa IPv4 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="b7f65-408">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="b7f65-409">`0.0.0.0` je zvláštní případ, který se váže ke všem adresám IPv4.</span><span class="sxs-lookup"><span data-stu-id="b7f65-409">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="b7f65-410">Adresa IPv6 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="b7f65-410">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="b7f65-411">`[::]` je ekvivalent protokolu IPv4 `0.0.0.0`protokolu IPv4.</span><span class="sxs-lookup"><span data-stu-id="b7f65-411">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="b7f65-412">Název hostitele s číslem portu</span><span class="sxs-lookup"><span data-stu-id="b7f65-412">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="b7f65-413">Názvy hostitelů, `*`a `+`nejsou speciální.</span><span class="sxs-lookup"><span data-stu-id="b7f65-413">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="b7f65-414">Cokoli se nerozpoznalo jako platná IP adresa nebo `localhost` se váže ke všem IP adresám IPv4 a IPv6.</span><span class="sxs-lookup"><span data-stu-id="b7f65-414">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="b7f65-415">Pokud chcete navazovat různé názvy hostitelů na různé ASP.NET Core aplikace na stejném portu, použijte [http. sys](xref:fundamentals/servers/httpsys) nebo reverzní proxy server, třeba IIS, Nginx nebo Apache.</span><span class="sxs-lookup"><span data-stu-id="b7f65-415">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="b7f65-416">Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="b7f65-416">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="b7f65-417">Název hostitele `localhost` s číslem portu nebo IP adresou zpětné smyčky s číslem portu</span><span class="sxs-lookup"><span data-stu-id="b7f65-417">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="b7f65-418">Při zadání `localhost` se Kestrel pokusí vytvořit propojení s rozhraními zpětné smyčky IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="b7f65-418">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="b7f65-419">Pokud je požadovaný port používán jinou službou na rozhraní zpětné smyčky, spuštění Kestrel se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="b7f65-419">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="b7f65-420">Pokud je rozhraní zpětné smyčky v žádném jiném případě nedostupné (většinou kvůli tomu, že protokol IPv6 není podporovaný), Kestrel zaznamená upozornění.</span><span class="sxs-lookup"><span data-stu-id="b7f65-420">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="b7f65-421">Filtrování hostitele</span><span class="sxs-lookup"><span data-stu-id="b7f65-421">Host filtering</span></span>

<span data-ttu-id="b7f65-422">I když Kestrel podporuje konfiguraci na základě prefixů, jako je například `http://example.com:5000`, Kestrel převážně ignoruje název hostitele.</span><span class="sxs-lookup"><span data-stu-id="b7f65-422">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="b7f65-423">Hostitelská `localhost` je zvláštní případ, který se používá pro vazbu na adresy zpětné smyčky.</span><span class="sxs-lookup"><span data-stu-id="b7f65-423">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="b7f65-424">Každý hostitel, který je jiný než explicitní IP adresa, se váže na všechny veřejné IP adresy.</span><span class="sxs-lookup"><span data-stu-id="b7f65-424">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="b7f65-425">`Host` hlavičky nejsou ověřeny.</span><span class="sxs-lookup"><span data-stu-id="b7f65-425">`Host` headers aren't validated.</span></span>

<span data-ttu-id="b7f65-426">Jako alternativní řešení použijte middleware pro filtrování hostitele.</span><span class="sxs-lookup"><span data-stu-id="b7f65-426">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="b7f65-427">Middleware pro filtrování hostitele poskytuje balíček [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , který je implicitně k dispozici pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="b7f65-427">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="b7f65-428">Middleware je přidána pomocí <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, která volá <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="b7f65-428">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="b7f65-429">Middleware pro filtrování hostitele je ve výchozím nastavení zakázáno.</span><span class="sxs-lookup"><span data-stu-id="b7f65-429">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="b7f65-430">Chcete-li povolit middleware, definujte `AllowedHosts` klíč v souboru *appSettings. json*/*appSettings.\<prostředí >. JSON*.</span><span class="sxs-lookup"><span data-stu-id="b7f65-430">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="b7f65-431">Hodnota je seznam názvů hostitelů oddělených středníkem bez čísel portů:</span><span class="sxs-lookup"><span data-stu-id="b7f65-431">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="b7f65-432">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="b7f65-432">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="b7f65-433">[Middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer) má taky možnost <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="b7f65-433">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="b7f65-434">Middleware předávaných hlaviček a middleware pro filtrování hostitele mají podobné funkce pro různé scénáře.</span><span class="sxs-lookup"><span data-stu-id="b7f65-434">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="b7f65-435">Nastavení `AllowedHosts` pomocí předávaných middlewarových hlaviček je vhodné, pokud při předávání požadavků pomocí reverzního proxy server nebo nástroje pro vyrovnávání zatížení nejsou zachovány hlavičky `Host`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-435">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="b7f65-436">Nastavení `AllowedHosts` pomocí middlewaru pro filtrování hostitele je vhodné při použití Kestrel jako veřejného hraničního serveru nebo při přímém přeposílání hlavičky `Host`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-436">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="b7f65-437">Další informace o middlewaru předávaných hlaviček najdete v tématu <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="b7f65-437">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="b7f65-438">Kestrel je [webový server pro různé platformy pro ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="b7f65-438">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="b7f65-439">Kestrel je webový server, který je ve výchozím nastavení součástí ASP.NET Core šablon projektů.</span><span class="sxs-lookup"><span data-stu-id="b7f65-439">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="b7f65-440">Kestrel podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="b7f65-440">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="b7f65-441">HTTPS</span><span class="sxs-lookup"><span data-stu-id="b7f65-441">HTTPS</span></span>
* <span data-ttu-id="b7f65-442">Neprůhledný upgrade používaný k povolení [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="b7f65-442">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="b7f65-443">Technologie UNIX Sockets s vysokým výkonem za Nginx</span><span class="sxs-lookup"><span data-stu-id="b7f65-443">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="b7f65-444">HTTP/2 (kromě macOS&dagger;)</span><span class="sxs-lookup"><span data-stu-id="b7f65-444">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="b7f65-445">&dagger;HTTP/2 bude v budoucí verzi podporován v macOS.</span><span class="sxs-lookup"><span data-stu-id="b7f65-445">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="b7f65-446">Kestrel se podporuje na všech platformách a verzích, které podporuje .NET Core.</span><span class="sxs-lookup"><span data-stu-id="b7f65-446">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="b7f65-447">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b7f65-447">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="b7f65-448">Podpora HTTP/2</span><span class="sxs-lookup"><span data-stu-id="b7f65-448">HTTP/2 support</span></span>

<span data-ttu-id="b7f65-449">[Http/2](https://httpwg.org/specs/rfc7540.html) je k dispozici pro ASP.NET Core aplikace, pokud jsou splněné následující základní požadavky:</span><span class="sxs-lookup"><span data-stu-id="b7f65-449">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="b7f65-450">&dagger; operačního systému</span><span class="sxs-lookup"><span data-stu-id="b7f65-450">Operating system&dagger;</span></span>
  * <span data-ttu-id="b7f65-451">Windows Server 2016/Windows 10 nebo novější&Dagger;</span><span class="sxs-lookup"><span data-stu-id="b7f65-451">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="b7f65-452">Linux s OpenSSL 1.0.2 nebo novějším (například Ubuntu 16,04 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="b7f65-452">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="b7f65-453">Cílová architektura: .NET Core 2,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="b7f65-453">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="b7f65-454">Připojení [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="b7f65-454">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="b7f65-455">Protokol TLS 1.2 nebo vyšší připojení</span><span class="sxs-lookup"><span data-stu-id="b7f65-455">TLS 1.2 or later connection</span></span>

<span data-ttu-id="b7f65-456">&dagger;HTTP/2 bude v budoucí verzi podporován v macOS.</span><span class="sxs-lookup"><span data-stu-id="b7f65-456">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="b7f65-457">&Dagger;Kestrel má omezená podpora HTTP/2 na Windows Serveru 2012 R2 a Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="b7f65-457">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="b7f65-458">Podpora je omezená, protože seznam podporovaných šifrovacích sad TLS, které jsou k dispozici v těchto operačních systémech, je omezený.</span><span class="sxs-lookup"><span data-stu-id="b7f65-458">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="b7f65-459">Pro zabezpečení připojení TLS může být vyžadován certifikát vygenerovaný pomocí algoritmu ECDSA (s připojením typu eliptická křivka).</span><span class="sxs-lookup"><span data-stu-id="b7f65-459">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="b7f65-460">Pokud je navázáno připojení HTTP/2, `HTTP/2`sestavy [protokolu HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) .</span><span class="sxs-lookup"><span data-stu-id="b7f65-460">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="b7f65-461">HTTP/2 je ve výchozím nastavení zakázané.</span><span class="sxs-lookup"><span data-stu-id="b7f65-461">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="b7f65-462">Další informace o konfiguraci najdete v částech [Možnosti Kestrel](#kestrel-options) a [ListenOptions. Protocols](#listenoptionsprotocols) .</span><span class="sxs-lookup"><span data-stu-id="b7f65-462">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="b7f65-463">Kdy použít Kestrel s reverzním proxy serverem</span><span class="sxs-lookup"><span data-stu-id="b7f65-463">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="b7f65-464">Kestrel může používat sám sebe nebo s *reverzními proxy server*, jako je [Internetová informační služba (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="b7f65-464">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="b7f65-465">Reverzní proxy server přijímá požadavky HTTP ze sítě a přesměruje je na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="b7f65-465">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="b7f65-466">Kestrel použitý jako hraniční webový server (internetový):</span><span class="sxs-lookup"><span data-stu-id="b7f65-466">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel komunikuje přímo s internetem bez zpětného proxy server](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="b7f65-468">Kestrel se používá v konfiguraci reverzního proxy serveru:</span><span class="sxs-lookup"><span data-stu-id="b7f65-468">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel nepřímo komunikuje s internetem prostřednictvím reverzní proxy server, jako je IIS, Nginx nebo Apache.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="b7f65-470">Buď konfigurace, s zpětným proxy server nebo bez něj, je podporovaná konfigurace hostování.</span><span class="sxs-lookup"><span data-stu-id="b7f65-470">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="b7f65-471">Kestrel používané jako hraniční Server bez reverzních proxy server nepodporuje sdílení stejné IP adresy a portu mezi více procesy.</span><span class="sxs-lookup"><span data-stu-id="b7f65-471">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="b7f65-472">Když je Kestrel nakonfigurovaný k naslouchání na portu, Kestrel zpracovává veškerý provoz tohoto portu bez ohledu na hlavičky `Host`ch požadavků.</span><span class="sxs-lookup"><span data-stu-id="b7f65-472">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="b7f65-473">Reverzní proxy server, který může sdílet porty, má schopnost přesměrovat požadavky na Kestrel na jedinečné IP adresy a porty.</span><span class="sxs-lookup"><span data-stu-id="b7f65-473">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="b7f65-474">I v případě, že se zpětný proxy server nepožaduje, může být použití reverzní proxy server vhodnou volbou.</span><span class="sxs-lookup"><span data-stu-id="b7f65-474">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="b7f65-475">Reverzní proxy:</span><span class="sxs-lookup"><span data-stu-id="b7f65-475">A reverse proxy:</span></span>

* <span data-ttu-id="b7f65-476">Může omezit vystavenou veřejnou plochu v aplikacích, které hostuje.</span><span class="sxs-lookup"><span data-stu-id="b7f65-476">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="b7f65-477">Poskytněte další vrstvu konfigurace a obrany.</span><span class="sxs-lookup"><span data-stu-id="b7f65-477">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="b7f65-478">Může se lépe integrovat se stávající infrastrukturou.</span><span class="sxs-lookup"><span data-stu-id="b7f65-478">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="b7f65-479">Zjednodušte vyrovnávání zatížení a konfiguraci zabezpečené komunikace (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="b7f65-479">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="b7f65-480">Pouze reverzní proxy server vyžaduje certifikát X. 509 a tento server může komunikovat se servery aplikace v interní síti pomocí prostého protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="b7f65-480">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="b7f65-481">Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="b7f65-481">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="b7f65-482">Použití Kestrel v aplikacích ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b7f65-482">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="b7f65-483">Balíček [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) je součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="b7f65-483">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="b7f65-484">ASP.NET Core šablony projektu ve výchozím nastavení používají Kestrel.</span><span class="sxs-lookup"><span data-stu-id="b7f65-484">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="b7f65-485">V *program.cs*kód šablony volá <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, která volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> na pozadí.</span><span class="sxs-lookup"><span data-stu-id="b7f65-485">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="b7f65-486">Další informace o `CreateDefaultBuilder` a vytváření hostitele najdete v části *Nastavení hostitele* v <xref:fundamentals/host/web-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="b7f65-486">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="b7f65-487">K poskytnutí další konfigurace po volání `CreateDefaultBuilder`použijte `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="b7f65-487">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="b7f65-488">Pokud aplikace nevolá `CreateDefaultBuilder` pro nastavení hostitele, zavolejte <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **před** voláním `ConfigureKestrel`:</span><span class="sxs-lookup"><span data-stu-id="b7f65-488">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="b7f65-489">Kestrel možnosti</span><span class="sxs-lookup"><span data-stu-id="b7f65-489">Kestrel options</span></span>

<span data-ttu-id="b7f65-490">Webový server Kestrel má možnosti konfigurace omezení, které jsou zvláště užitečné v internetových nasazeních.</span><span class="sxs-lookup"><span data-stu-id="b7f65-490">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="b7f65-491">Nastavte omezení pro vlastnost <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> třídy <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="b7f65-491">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="b7f65-492">Vlastnost `Limits` obsahuje instanci třídy <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="b7f65-492">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="b7f65-493">V následujících příkladech je použit obor názvů <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:</span><span class="sxs-lookup"><span data-stu-id="b7f65-493">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="b7f65-494">Možnosti Kestrel, které jsou konfigurovány C# v kódu v následujících příkladech, lze také nastavit pomocí [poskytovatele konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="b7f65-494">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="b7f65-495">Poskytovatel konfigurace souboru může například načíst konfiguraci Kestrel z objektu *appSettings. JSON* nebo *appSettings. { Soubor Environment}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="b7f65-495">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="b7f65-496">Použijte **jeden** z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="b7f65-496">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="b7f65-497">Konfigurace Kestrel v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b7f65-497">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="b7f65-498">Vloží instanci `IConfiguration` do `Startup` třídy.</span><span class="sxs-lookup"><span data-stu-id="b7f65-498">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="b7f65-499">Následující příklad předpokládá, že vložená konfigurace je přiřazena vlastnosti `Configuration`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-499">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="b7f65-500">V `Startup.ConfigureServices`načtěte oddíl `Kestrel` konfigurace do konfigurace Kestrel:</span><span class="sxs-lookup"><span data-stu-id="b7f65-500">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="b7f65-501">Nakonfigurovat Kestrel při sestavování hostitele:</span><span class="sxs-lookup"><span data-stu-id="b7f65-501">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="b7f65-502">V *program.cs*načtěte část `Kestrel` konfigurace do konfigurace Kestrel:</span><span class="sxs-lookup"><span data-stu-id="b7f65-502">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="b7f65-503">Obě předchozí přístupy fungují u libovolného [poskytovatele konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="b7f65-503">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="b7f65-504">Časový limit Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="b7f65-504">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="b7f65-505">Získá nebo nastaví [časový limit zachování](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="b7f65-505">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="b7f65-506">Výchozí hodnota je 2 minuty.</span><span class="sxs-lookup"><span data-stu-id="b7f65-506">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="b7f65-507">Maximální počet připojení klientů</span><span class="sxs-lookup"><span data-stu-id="b7f65-507">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="b7f65-508">Maximální počet souběžných otevřených připojení TCP lze pro celou aplikaci nastavit pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="b7f65-508">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="b7f65-509">Existuje zvláštní omezení pro připojení, která byla upgradována z protokolu HTTP nebo HTTPS na jiný protokol (například na žádost WebSockets).</span><span class="sxs-lookup"><span data-stu-id="b7f65-509">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="b7f65-510">Po upgradu připojení se nepočítá s limitem `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-510">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="b7f65-511">Ve výchozím nastavení je maximální počet připojení neomezené (null).</span><span class="sxs-lookup"><span data-stu-id="b7f65-511">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="b7f65-512">Maximální velikost textu požadavku</span><span class="sxs-lookup"><span data-stu-id="b7f65-512">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="b7f65-513">Výchozí maximální velikost textu požadavku je 30 000 000 bajtů, což je přibližně 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="b7f65-513">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="b7f65-514">Doporučený postup pro přepsání limitu v aplikaci ASP.NET Core MVC je použití atributu <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> u metody Action:</span><span class="sxs-lookup"><span data-stu-id="b7f65-514">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="b7f65-515">Tady je příklad, který ukazuje, jak nakonfigurovat omezení pro aplikaci na každém požadavku:</span><span class="sxs-lookup"><span data-stu-id="b7f65-515">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="b7f65-516">Přepsat nastavení u konkrétního požadavku v middleware:</span><span class="sxs-lookup"><span data-stu-id="b7f65-516">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="b7f65-517">Výjimka je vyvolána, pokud aplikace po zahájení aplikace nakonfiguruje limit na žádost.</span><span class="sxs-lookup"><span data-stu-id="b7f65-517">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="b7f65-518">Existuje vlastnost `IsReadOnly`, která určuje, zda je vlastnost `MaxRequestBodySize` ve stavu jen pro čtení, což znamená, že je příliš pozdě pro konfiguraci limitu.</span><span class="sxs-lookup"><span data-stu-id="b7f65-518">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="b7f65-519">Když je aplikace spouštěna [mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za [ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module), je omezení velikosti textu požadavku Kestrel zakázané, protože služba IIS tento limit již nastavuje.</span><span class="sxs-lookup"><span data-stu-id="b7f65-519">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="b7f65-520">Minimální rychlost těla zprávy požadavku</span><span class="sxs-lookup"><span data-stu-id="b7f65-520">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="b7f65-521">Kestrel kontroluje každou sekundu, pokud data přicházejí za zadanou rychlost v bajtech za sekundu.</span><span class="sxs-lookup"><span data-stu-id="b7f65-521">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="b7f65-522">Pokud frekvence klesne pod minimální hodnotu, vypršel časový limit připojení. Doba odkladu je doba, kterou Kestrel umožňuje klientovi zvýšit rychlost odesílání až na minimum; frekvence není během této doby kontrolována.</span><span class="sxs-lookup"><span data-stu-id="b7f65-522">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="b7f65-523">Doba odkladu pomáhá vyhnout se vyřazení připojení, která při počátečním posílání dat zpomalují rychlost, kvůli pomalému spuštění protokolu TCP.</span><span class="sxs-lookup"><span data-stu-id="b7f65-523">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="b7f65-524">Výchozí minimální sazba je 240 bajtů za sekundu s obdobím 5 sekund odkladu.</span><span class="sxs-lookup"><span data-stu-id="b7f65-524">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="b7f65-525">Pro odpověď platí také minimální sazba.</span><span class="sxs-lookup"><span data-stu-id="b7f65-525">A minimum rate also applies to the response.</span></span> <span data-ttu-id="b7f65-526">Kód pro nastavení limitu požadavku a limit odezvy je stejný, s výjimkou `RequestBody` nebo `Response` v názvech vlastností a rozhraní.</span><span class="sxs-lookup"><span data-stu-id="b7f65-526">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="b7f65-527">Tady je příklad, který ukazuje, jak nakonfigurovat minimální datové tarify v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="b7f65-527">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="b7f65-528">Přepsat minimální limity přenosové rychlosti na žádost v middlewaru:</span><span class="sxs-lookup"><span data-stu-id="b7f65-528">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="b7f65-529">`HttpContext.Features` v předchozích ukázkových příkladech se neodkazuje žádná funkce míry, protože Změna omezení četnosti pro jednotlivé požadavky není pro HTTP/2 podporovaná kvůli podpoře protokolu pro multiplexování požadavků.</span><span class="sxs-lookup"><span data-stu-id="b7f65-529">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="b7f65-530">Omezení přenosové rychlosti pro všechny servery konfigurovaná prostřednictvím `KestrelServerOptions.Limits` se pořád vztahují na připojení HTTP/1. x i HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="b7f65-530">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="b7f65-531">Časový limit hlaviček žádostí</span><span class="sxs-lookup"><span data-stu-id="b7f65-531">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="b7f65-532">Získá nebo nastaví maximální dobu, po kterou server stráví příjem hlaviček požadavků.</span><span class="sxs-lookup"><span data-stu-id="b7f65-532">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="b7f65-533">Výchozí hodnota je 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="b7f65-533">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="b7f65-534">Maximální počet proudů na připojení</span><span class="sxs-lookup"><span data-stu-id="b7f65-534">Maximum streams per connection</span></span>

<span data-ttu-id="b7f65-535">`Http2.MaxStreamsPerConnection` omezuje počet souběžných datových proudů požadavků na připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="b7f65-535">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="b7f65-536">Nadbytečné proudy jsou odmítnuty.</span><span class="sxs-lookup"><span data-stu-id="b7f65-536">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="b7f65-537">Výchozí hodnota je 100.</span><span class="sxs-lookup"><span data-stu-id="b7f65-537">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="b7f65-538">Velikost tabulky hlaviček</span><span class="sxs-lookup"><span data-stu-id="b7f65-538">Header table size</span></span>

<span data-ttu-id="b7f65-539">Dekodér HPACK dekomprimuje hlavičky HTTP pro připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="b7f65-539">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="b7f65-540">`Http2.HeaderTableSize` omezuje velikost kompresní tabulky hlaviček, kterou používá dekodér HPACK.</span><span class="sxs-lookup"><span data-stu-id="b7f65-540">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="b7f65-541">Hodnota je uvedena v oktetech a musí být větší než nula (0).</span><span class="sxs-lookup"><span data-stu-id="b7f65-541">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="b7f65-542">Výchozí hodnota je 4096.</span><span class="sxs-lookup"><span data-stu-id="b7f65-542">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="b7f65-543">Maximální velikost rámce</span><span class="sxs-lookup"><span data-stu-id="b7f65-543">Maximum frame size</span></span>

<span data-ttu-id="b7f65-544">`Http2.MaxFrameSize` určuje maximální velikost datové části rámce připojení HTTP/2, která se má přijmout.</span><span class="sxs-lookup"><span data-stu-id="b7f65-544">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="b7f65-545">Hodnota je uvedena v oktetech a musí být v rozmezí 2 ^ 14 (16 384) až 2 ^ 24-1 (16 777 215).</span><span class="sxs-lookup"><span data-stu-id="b7f65-545">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="b7f65-546">Výchozí hodnota je 2 ^ 14 (16 384).</span><span class="sxs-lookup"><span data-stu-id="b7f65-546">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="b7f65-547">Maximální velikost hlavičky požadavku</span><span class="sxs-lookup"><span data-stu-id="b7f65-547">Maximum request header size</span></span>

<span data-ttu-id="b7f65-548">`Http2.MaxRequestHeaderFieldSize` určuje maximální povolenou velikost v oktetech hodnot hlaviček požadavku.</span><span class="sxs-lookup"><span data-stu-id="b7f65-548">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="b7f65-549">Toto omezení platí pro názvy i hodnoty společně v komprimovaných a nekomprimovaných reprezentujcích.</span><span class="sxs-lookup"><span data-stu-id="b7f65-549">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="b7f65-550">Hodnota musí být větší než nula (0).</span><span class="sxs-lookup"><span data-stu-id="b7f65-550">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="b7f65-551">Výchozí hodnota je 8 192.</span><span class="sxs-lookup"><span data-stu-id="b7f65-551">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="b7f65-552">Velikost okna počátečního připojení</span><span class="sxs-lookup"><span data-stu-id="b7f65-552">Initial connection window size</span></span>

<span data-ttu-id="b7f65-553">`Http2.InitialConnectionWindowSize` určuje maximální velikost textu požadavku v bajtech, které jsou v jednom okamžiku agregovány v rámci všech požadavků (datových proudů) na připojení.</span><span class="sxs-lookup"><span data-stu-id="b7f65-553">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="b7f65-554">Žádosti jsou také omezeny `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-554">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="b7f65-555">Hodnota musí být větší než nebo rovna 65 535 a menší než 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="b7f65-555">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="b7f65-556">Výchozí hodnota je 128 KB (131 072).</span><span class="sxs-lookup"><span data-stu-id="b7f65-556">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="b7f65-557">Počáteční velikost okna datového proudu</span><span class="sxs-lookup"><span data-stu-id="b7f65-557">Initial stream window size</span></span>

<span data-ttu-id="b7f65-558">`Http2.InitialStreamWindowSize` určuje maximální množství údajů o textu požadavku v bajtech, které jsou ukládány do vyrovnávací paměti serveru na jednu žádost (datový proud).</span><span class="sxs-lookup"><span data-stu-id="b7f65-558">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="b7f65-559">Žádosti jsou také omezeny `Http2.InitialStreamWindowSize`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-559">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="b7f65-560">Hodnota musí být větší než nebo rovna 65 535 a menší než 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="b7f65-560">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="b7f65-561">Výchozí hodnota je 96 KB (98 304).</span><span class="sxs-lookup"><span data-stu-id="b7f65-561">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="b7f65-562">Synchronní v/v</span><span class="sxs-lookup"><span data-stu-id="b7f65-562">Synchronous IO</span></span>

<span data-ttu-id="b7f65-563"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> určuje, zda je pro požadavek a odpověď povolena synchronní v/v.</span><span class="sxs-lookup"><span data-stu-id="b7f65-563"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="b7f65-564">Výchozí hodnota je `true`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-564">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="b7f65-565">Velký počet blokování synchronních vstupně-výstupních operací může vést ke vyčerpání fondu vláken, což aplikaci nereaguje.</span><span class="sxs-lookup"><span data-stu-id="b7f65-565">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="b7f65-566">Povolit `AllowSynchronousIO` jenom při použití knihovny, která nepodporuje asynchronní vstupně-výstupní operace.</span><span class="sxs-lookup"><span data-stu-id="b7f65-566">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="b7f65-567">Následující příklad povoluje synchronní vstupně-výstupní operace:</span><span class="sxs-lookup"><span data-stu-id="b7f65-567">The following example enables synchronous IO:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="b7f65-568">Informace o dalších možnostech a omezeních Kestrel najdete v tématech:</span><span class="sxs-lookup"><span data-stu-id="b7f65-568">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="b7f65-569">Konfigurace koncového bodu</span><span class="sxs-lookup"><span data-stu-id="b7f65-569">Endpoint configuration</span></span>

<span data-ttu-id="b7f65-570">Ve výchozím nastavení ASP.NET Core váže k:</span><span class="sxs-lookup"><span data-stu-id="b7f65-570">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="b7f65-571">`https://localhost:5001` (Pokud je k dispozici místní vývojový certifikát)</span><span class="sxs-lookup"><span data-stu-id="b7f65-571">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="b7f65-572">Zadejte adresy URL pomocí:</span><span class="sxs-lookup"><span data-stu-id="b7f65-572">Specify URLs using the:</span></span>

* <span data-ttu-id="b7f65-573">`ASPNETCORE_URLS` proměnnou prostředí.</span><span class="sxs-lookup"><span data-stu-id="b7f65-573">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="b7f65-574">`--urls` argument příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="b7f65-574">`--urls` command-line argument.</span></span>
* <span data-ttu-id="b7f65-575">`urls` konfigurační klíč hostitele.</span><span class="sxs-lookup"><span data-stu-id="b7f65-575">`urls` host configuration key.</span></span>
* <span data-ttu-id="b7f65-576">`UseUrls` metoda rozšíření.</span><span class="sxs-lookup"><span data-stu-id="b7f65-576">`UseUrls` extension method.</span></span>

<span data-ttu-id="b7f65-577">Hodnota zadaná pomocí těchto přístupů může být jeden nebo více koncových bodů HTTP a HTTPS (HTTPS, pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="b7f65-577">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="b7f65-578">Nakonfigurujte hodnotu jako seznam oddělený středníkem (například `"Urls": "http://localhost:8000; http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="b7f65-578">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="b7f65-579">Další informace o těchto přístupůch najdete v tématu [adresy URL serveru](xref:fundamentals/host/web-host#server-urls) a [potlačení konfigurace](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="b7f65-579">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="b7f65-580">Vytvoří se vývojový certifikát:</span><span class="sxs-lookup"><span data-stu-id="b7f65-580">A development certificate is created:</span></span>

* <span data-ttu-id="b7f65-581">Po instalaci [.NET Core SDK](/dotnet/core/sdk) .</span><span class="sxs-lookup"><span data-stu-id="b7f65-581">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="b7f65-582">[Nástroj pro vývoj a certifikáty](xref:aspnetcore-2.1#https) slouží k vytvoření certifikátu.</span><span class="sxs-lookup"><span data-stu-id="b7f65-582">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="b7f65-583">Některé prohlížeče vyžadují udělení explicitního oprávnění pro důvěřování místního vývojového certifikátu.</span><span class="sxs-lookup"><span data-stu-id="b7f65-583">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="b7f65-584">Šablony projektů konfigurují aplikace tak, aby běžely ve výchozím nastavení HTTPS a zahrnovaly [přesměrování https a podporu HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="b7f65-584">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="b7f65-585">Pokud chcete nakonfigurovat předpony a porty adres URL pro Kestrel, volejte <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> nebo <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> metody na <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="b7f65-585">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="b7f65-586">`UseUrls`, argument příkazového řádku `--urls`, `urls` konfigurační klíč hostitele a proměnná prostředí `ASPNETCORE_URLS` fungují, ale mají omezení zaznamenaná později v této části (pro konfiguraci koncového bodu HTTPS musí být k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="b7f65-586">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="b7f65-587">Konfigurace `KestrelServerOptions`:</span><span class="sxs-lookup"><span data-stu-id="b7f65-587">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="b7f65-588">ConfigureEndpointDefaults (Action\<ListenOptions >)</span><span class="sxs-lookup"><span data-stu-id="b7f65-588">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="b7f65-589">Určuje `Action` konfigurace, která se má spustit pro každý zadaný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="b7f65-589">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="b7f65-590">Volání `ConfigureEndpointDefaults` několikrát nahrazuje předchozí `Action`s uvedením posledního `Action`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-590">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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

> [!NOTE]
> <span data-ttu-id="b7f65-591">Koncové body vytvořené voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **před** voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> nebudou použity výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="b7f65-591">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="b7f65-592">ConfigureHttpsDefaults (Action\<HttpsConnectionAdapterOptions >)</span><span class="sxs-lookup"><span data-stu-id="b7f65-592">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="b7f65-593">Určuje `Action` konfigurace, která se má spustit pro každý koncový bod HTTPS.</span><span class="sxs-lookup"><span data-stu-id="b7f65-593">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="b7f65-594">Volání `ConfigureHttpsDefaults` několikrát nahrazuje předchozí `Action`s uvedením posledního `Action`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-594">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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

> [!NOTE]
> <span data-ttu-id="b7f65-595">Koncové body vytvořené voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **před** voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> nebudou použity výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="b7f65-595">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>


### <a name="configureiconfiguration"></a><span data-ttu-id="b7f65-596">Konfigurace (IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="b7f65-596">Configure(IConfiguration)</span></span>

<span data-ttu-id="b7f65-597">Vytvoří zavaděč konfigurace pro nastavení Kestrel, který jako vstup převezme <xref:Microsoft.Extensions.Configuration.IConfiguration>.</span><span class="sxs-lookup"><span data-stu-id="b7f65-597">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="b7f65-598">Konfigurace musí být vymezená na konfigurační oddíl pro Kestrel.</span><span class="sxs-lookup"><span data-stu-id="b7f65-598">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="b7f65-599">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="b7f65-599">ListenOptions.UseHttps</span></span>

<span data-ttu-id="b7f65-600">Nakonfigurujte Kestrel tak, aby používal protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="b7f65-600">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="b7f65-601">rozšíření `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="b7f65-601">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="b7f65-602">`UseHttps` &ndash; nakonfigurovat Kestrel na používání protokolu HTTPS s výchozím certifikátem.</span><span class="sxs-lookup"><span data-stu-id="b7f65-602">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="b7f65-603">Vyvolá výjimku, pokud není nakonfigurován žádný výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="b7f65-603">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="b7f65-604">parametry `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="b7f65-604">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="b7f65-605">`filename` je cesta a název souboru certifikátu vzhledem k adresáři, který obsahuje soubory obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="b7f65-605">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="b7f65-606">`password` je heslo vyžadované pro přístup k datům certifikátu X. 509.</span><span class="sxs-lookup"><span data-stu-id="b7f65-606">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="b7f65-607">`configureOptions` je `Action` ke konfiguraci `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-607">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="b7f65-608">Vrátí `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-608">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="b7f65-609">`storeName` je úložiště certifikátů, ze kterého se má certifikát načíst.</span><span class="sxs-lookup"><span data-stu-id="b7f65-609">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="b7f65-610">`subject` je název subjektu certifikátu.</span><span class="sxs-lookup"><span data-stu-id="b7f65-610">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="b7f65-611">`allowInvalid` určuje, jestli by se měly brát v úvahu neplatné certifikáty, například certifikáty podepsané svým držitelem.</span><span class="sxs-lookup"><span data-stu-id="b7f65-611">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="b7f65-612">`location` je umístění úložiště, ze kterého se má načíst certifikát.</span><span class="sxs-lookup"><span data-stu-id="b7f65-612">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="b7f65-613">`serverCertificate` je certifikát X. 509.</span><span class="sxs-lookup"><span data-stu-id="b7f65-613">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="b7f65-614">V produkčním prostředí musí být HTTPS explicitně nakonfigurovaný.</span><span class="sxs-lookup"><span data-stu-id="b7f65-614">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="b7f65-615">Musí být zadán minimálně výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="b7f65-615">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="b7f65-616">Podporované konfigurace popsané dál:</span><span class="sxs-lookup"><span data-stu-id="b7f65-616">Supported configurations described next:</span></span>

* <span data-ttu-id="b7f65-617">Žádná konfigurace</span><span class="sxs-lookup"><span data-stu-id="b7f65-617">No configuration</span></span>
* <span data-ttu-id="b7f65-618">Nahradit výchozí certifikát z konfigurace</span><span class="sxs-lookup"><span data-stu-id="b7f65-618">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="b7f65-619">Změna výchozích hodnot v kódu</span><span class="sxs-lookup"><span data-stu-id="b7f65-619">Change the defaults in code</span></span>

<span data-ttu-id="b7f65-620">*Žádná konfigurace*</span><span class="sxs-lookup"><span data-stu-id="b7f65-620">*No configuration*</span></span>

<span data-ttu-id="b7f65-621">Kestrel naslouchá na `http://localhost:5000` a `https://localhost:5001` (Pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="b7f65-621">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="b7f65-622">*Nahradit výchozí certifikát z konfigurace*</span><span class="sxs-lookup"><span data-stu-id="b7f65-622">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="b7f65-623">`CreateDefaultBuilder` ve výchozím nastavení volá `Configure(context.Configuration.GetSection("Kestrel"))`, aby se načetla konfigurace Kestrel.</span><span class="sxs-lookup"><span data-stu-id="b7f65-623">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="b7f65-624">Pro Kestrel je k dispozici výchozí schéma konfigurace nastavení aplikace HTTPS.</span><span class="sxs-lookup"><span data-stu-id="b7f65-624">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="b7f65-625">Nakonfigurujte několik koncových bodů, včetně adres URL a certifikátů, které se mají použít, buď ze souboru na disku, nebo z úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="b7f65-625">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="b7f65-626">V následujícím příkladu *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="b7f65-626">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="b7f65-627">Nastavte **AllowInvalid** na `true`, aby bylo možné povolit použití neplatných certifikátů (například certifikátů podepsaných svým držitelem).</span><span class="sxs-lookup"><span data-stu-id="b7f65-627">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="b7f65-628">Libovolný koncový bod HTTPS, který neurčuje certifikát (**HttpsDefaultCert** v následujícím příkladu), se vrátí k certifikátu definovanému v části **certifikáty** > **výchozím** nebo vývojovým certifikátem.</span><span class="sxs-lookup"><span data-stu-id="b7f65-628">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="b7f65-629">Alternativou k použití **cesty** a **hesla** pro libovolný uzel certifikátu je zadání certifikátu pomocí polí úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="b7f65-629">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="b7f65-630">Například **certifikáty** > **výchozí** certifikát lze zadat jako:</span><span class="sxs-lookup"><span data-stu-id="b7f65-630">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="b7f65-631">Poznámky ke schématu:</span><span class="sxs-lookup"><span data-stu-id="b7f65-631">Schema notes:</span></span>

* <span data-ttu-id="b7f65-632">V názvech koncových bodů se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="b7f65-632">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="b7f65-633">Například `HTTPS` a `Https` jsou platné.</span><span class="sxs-lookup"><span data-stu-id="b7f65-633">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="b7f65-634">U každého koncového bodu je vyžadován parametr `Url`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-634">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="b7f65-635">Formát pro tento parametr je stejný jako konfigurační parametr `Urls` nejvyšší úrovně s tím rozdílem, že je omezen na jedinou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="b7f65-635">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="b7f65-636">Tyto koncové body nahrazují hodnoty definované v konfiguraci `Urls` na nejvyšší úrovni místo jejich přidávání do těchto koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="b7f65-636">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="b7f65-637">Koncové body definované v kódu prostřednictvím `Listen` jsou kumulativní s použitím koncových bodů definovaných v konfiguračním oddílu.</span><span class="sxs-lookup"><span data-stu-id="b7f65-637">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="b7f65-638">Oddíl `Certificate` je nepovinný.</span><span class="sxs-lookup"><span data-stu-id="b7f65-638">The `Certificate` section is optional.</span></span> <span data-ttu-id="b7f65-639">Pokud není zadaný oddíl `Certificate`, použijí se výchozí hodnoty definované v dřívějších scénářích.</span><span class="sxs-lookup"><span data-stu-id="b7f65-639">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="b7f65-640">Pokud nejsou k dispozici žádné výchozí hodnoty, Server vyvolá výjimku a nemůže se spustit.</span><span class="sxs-lookup"><span data-stu-id="b7f65-640">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="b7f65-641">Oddíl `Certificate` podporuje jak **cesty**&ndash;**hesla** , tak&ndash;ho **úložiště** **subjektu** .</span><span class="sxs-lookup"><span data-stu-id="b7f65-641">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="b7f65-642">V takovém případě může být definován libovolný počet koncových bodů, pokud to nezpůsobí konflikty portů.</span><span class="sxs-lookup"><span data-stu-id="b7f65-642">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="b7f65-643">`options.Configure(context.Configuration.GetSection("{SECTION}"))` vrátí `KestrelConfigurationLoader` s metodou `.Endpoint(string name, listenOptions => { })`, která se dá použít k doplnění nastavení nakonfigurovaného koncového bodu:</span><span class="sxs-lookup"><span data-stu-id="b7f65-643">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="b7f65-644">k pokračování v iteraci u stávajícího zavaděče, jako je třeba ten, který poskytuje <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, se dá `KestrelServerOptions.ConfigurationLoader` použít přímo.</span><span class="sxs-lookup"><span data-stu-id="b7f65-644">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="b7f65-645">Konfigurační oddíl pro každý koncový bod je k dispozici na možnostech v metodě `Endpoint`, aby bylo možné číst vlastní nastavení.</span><span class="sxs-lookup"><span data-stu-id="b7f65-645">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="b7f65-646">Více konfigurací může být načteno voláním `options.Configure(context.Configuration.GetSection("{SECTION}"))` znovu s jiným oddílem.</span><span class="sxs-lookup"><span data-stu-id="b7f65-646">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="b7f65-647">Použije se jenom poslední konfigurace, pokud se `Load` explicitně nevolá při předchozích instancích.</span><span class="sxs-lookup"><span data-stu-id="b7f65-647">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="b7f65-648">Metapackage nevolá `Load`, aby se jeho výchozí konfigurační oddíl mohl nahradit.</span><span class="sxs-lookup"><span data-stu-id="b7f65-648">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="b7f65-649">`KestrelConfigurationLoader` zrcadlí `Listen` rodinu rozhraní API `KestrelServerOptions` jako `Endpoint` přetížení, takže koncové body kódu a konfigurace lze konfigurovat na stejném místě.</span><span class="sxs-lookup"><span data-stu-id="b7f65-649">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="b7f65-650">Tato přetížení nepoužívají názvy a využívají pouze výchozí nastavení z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b7f65-650">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="b7f65-651">*Změna výchozích hodnot v kódu*</span><span class="sxs-lookup"><span data-stu-id="b7f65-651">*Change the defaults in code*</span></span>

<span data-ttu-id="b7f65-652">`ConfigureEndpointDefaults` a `ConfigureHttpsDefaults` lze použít ke změně výchozího nastavení pro `ListenOptions` a `HttpsConnectionAdapterOptions`, včetně přepsání výchozího certifikátu zadaného v předchozím scénáři.</span><span class="sxs-lookup"><span data-stu-id="b7f65-652">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="b7f65-653">`ConfigureEndpointDefaults` a `ConfigureHttpsDefaults` by měly být volány před konfigurací koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="b7f65-653">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="b7f65-654">*Podpora Kestrel pro SNI*</span><span class="sxs-lookup"><span data-stu-id="b7f65-654">*Kestrel support for SNI*</span></span>

<span data-ttu-id="b7f65-655">[Indikace názvu serveru (SNI)](https://tools.ietf.org/html/rfc6066#section-3) lze použít k hostování více domén na stejné IP adrese a portu.</span><span class="sxs-lookup"><span data-stu-id="b7f65-655">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="b7f65-656">Aby SNI fungovalo, klient pošle název hostitele pro zabezpečenou relaci serveru během metody handshake TLS, aby server mohl poskytovat správný certifikát.</span><span class="sxs-lookup"><span data-stu-id="b7f65-656">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="b7f65-657">Klient používá poskytnutý certifikát pro šifrovanou komunikaci se serverem během zabezpečené relace, která následuje za metodou handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="b7f65-657">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="b7f65-658">Kestrel podporuje SNI prostřednictvím zpětného volání `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-658">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="b7f65-659">Zpětné volání je vyvoláno jednou za připojení, které aplikaci umožní zkontrolovat název hostitele a vybrat příslušný certifikát.</span><span class="sxs-lookup"><span data-stu-id="b7f65-659">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="b7f65-660">Podpora SNI vyžaduje:</span><span class="sxs-lookup"><span data-stu-id="b7f65-660">SNI support requires:</span></span>

* <span data-ttu-id="b7f65-661">Spuštění v cílovém rozhraní `netcoreapp2.1` nebo novějším.</span><span class="sxs-lookup"><span data-stu-id="b7f65-661">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="b7f65-662">V `net461` nebo novějším je zpětné volání vyvoláno, ale `name` je vždy `null`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-662">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="b7f65-663">`name` taky `null`, pokud klient neposkytne parametr názvu hostitele v handshaki TLS.</span><span class="sxs-lookup"><span data-stu-id="b7f65-663">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="b7f65-664">Všechny weby běží na stejné instanci Kestrel.</span><span class="sxs-lookup"><span data-stu-id="b7f65-664">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="b7f65-665">Kestrel nepodporuje sdílení IP adresy a portu napříč několika instancemi bez reverzního proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="b7f65-665">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="b7f65-666">Protokolování připojení</span><span class="sxs-lookup"><span data-stu-id="b7f65-666">Connection logging</span></span>

<span data-ttu-id="b7f65-667">Volání <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> k vygenerování protokolů na úrovni ladění pro komunikaci na úrovni bajtů v rámci připojení.</span><span class="sxs-lookup"><span data-stu-id="b7f65-667">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="b7f65-668">Protokolování připojení je užitečné při řešení problémů v komunikaci nízké úrovně, například během šifrování TLS a za proxy servery.</span><span class="sxs-lookup"><span data-stu-id="b7f65-668">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="b7f65-669">Pokud je `UseConnectionLogging` před `UseHttps`, zaprotokoluje se zašifrovaný provoz.</span><span class="sxs-lookup"><span data-stu-id="b7f65-669">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="b7f65-670">Je-li po `UseHttps`umístěn `UseConnectionLogging`, zašifrovaný provoz se zaznamená do protokolu.</span><span class="sxs-lookup"><span data-stu-id="b7f65-670">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="b7f65-671">Vytvoření vazby na soket TCP</span><span class="sxs-lookup"><span data-stu-id="b7f65-671">Bind to a TCP socket</span></span>

<span data-ttu-id="b7f65-672">Metoda <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> se váže k soketu TCP a obslužná rutina možností umožňuje konfiguraci certifikátu X. 509:</span><span class="sxs-lookup"><span data-stu-id="b7f65-672">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="b7f65-673">Příklad konfiguruje HTTPS pro koncový bod s <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="b7f65-673">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="b7f65-674">Ke konfiguraci dalších nastavení Kestrel pro konkrétní koncové body použijte stejné rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="b7f65-674">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="b7f65-675">Vytvoření vazby na soket UNIX</span><span class="sxs-lookup"><span data-stu-id="b7f65-675">Bind to a Unix socket</span></span>

<span data-ttu-id="b7f65-676">Naslouchat na soketu UNIX pomocí <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> pro zlepšení výkonu pomocí Nginx, jak je znázorněno v tomto příkladu:</span><span class="sxs-lookup"><span data-stu-id="b7f65-676">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="b7f65-677">V souboru Nginx confiuguration nastavte  > `server``location`položka  > `proxy_pass`.`http://unix:/tmp/{KESTREL SOCKET}:/;`</span><span class="sxs-lookup"><span data-stu-id="b7f65-677">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="b7f65-678">`{KESTREL SOCKET}` je název soketu, který je k dispozici pro <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (například `kestrel-test.sock` v předchozím příkladu).</span><span class="sxs-lookup"><span data-stu-id="b7f65-678">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="b7f65-679">Zajistěte, aby byl soket zapisovatelný pomocí Nginx (například `chmod go+w /tmp/kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="b7f65-679">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="b7f65-680">Port 0</span><span class="sxs-lookup"><span data-stu-id="b7f65-680">Port 0</span></span>

<span data-ttu-id="b7f65-681">Když je zadané číslo portu `0`, Kestrel se dynamicky váže k dostupnému portu.</span><span class="sxs-lookup"><span data-stu-id="b7f65-681">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="b7f65-682">Následující příklad ukazuje, jak určit, který port Kestrel je skutečně vázaný za běhu:</span><span class="sxs-lookup"><span data-stu-id="b7f65-682">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="b7f65-683">Když se aplikace spustí, výstup okna konzoly indikuje dynamický port, na který se aplikace dá získat:</span><span class="sxs-lookup"><span data-stu-id="b7f65-683">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="b7f65-684">Omezení</span><span class="sxs-lookup"><span data-stu-id="b7f65-684">Limitations</span></span>

<span data-ttu-id="b7f65-685">Konfigurace koncových bodů pomocí následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="b7f65-685">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="b7f65-686">`--urls` argument příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="b7f65-686">`--urls` command-line argument</span></span>
* <span data-ttu-id="b7f65-687">`urls` konfigurační klíč hostitele</span><span class="sxs-lookup"><span data-stu-id="b7f65-687">`urls` host configuration key</span></span>
* <span data-ttu-id="b7f65-688">`ASPNETCORE_URLS` proměnná prostředí</span><span class="sxs-lookup"><span data-stu-id="b7f65-688">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="b7f65-689">Tyto metody jsou užitečné, pokud chcete, aby kód pracoval na jiných serverech než Kestrel.</span><span class="sxs-lookup"><span data-stu-id="b7f65-689">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="b7f65-690">Mějte ale na paměti následující omezení:</span><span class="sxs-lookup"><span data-stu-id="b7f65-690">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="b7f65-691">Protokol HTTPS nelze s těmito přístupy použít, pokud není k dispozici výchozí certifikát v konfiguraci koncového bodu HTTPS (například pomocí konfigurace `KestrelServerOptions` nebo konfiguračního souboru, jak je uvedeno výše v tomto tématu).</span><span class="sxs-lookup"><span data-stu-id="b7f65-691">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="b7f65-692">Při současném použití `Listen` i `UseUrls` se používají koncové body `Listen` `UseUrls` koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="b7f65-692">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="b7f65-693">Konfigurace koncového bodu služby IIS</span><span class="sxs-lookup"><span data-stu-id="b7f65-693">IIS endpoint configuration</span></span>

<span data-ttu-id="b7f65-694">Při použití služby IIS jsou vazby adresy URL pro přepsání vazby služby IIS nastaveny buď `Listen`, nebo `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-694">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="b7f65-695">Další informace najdete v tématu [ASP.NET Core modulu](xref:host-and-deploy/aspnet-core-module) .</span><span class="sxs-lookup"><span data-stu-id="b7f65-695">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="b7f65-696">ListenOptions. Protocols</span><span class="sxs-lookup"><span data-stu-id="b7f65-696">ListenOptions.Protocols</span></span>

<span data-ttu-id="b7f65-697">Vlastnost `Protocols` vytváří protokoly HTTP (`HttpProtocols`) povolené pro koncový bod připojení nebo pro server.</span><span class="sxs-lookup"><span data-stu-id="b7f65-697">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="b7f65-698">Přiřaďte hodnotu vlastnosti `Protocols` z výčtu `HttpProtocols`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-698">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="b7f65-699">hodnota výčtu `HttpProtocols`</span><span class="sxs-lookup"><span data-stu-id="b7f65-699">`HttpProtocols` enum value</span></span> | <span data-ttu-id="b7f65-700">Povolený protokol připojení</span><span class="sxs-lookup"><span data-stu-id="b7f65-700">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="b7f65-701">Pouze HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="b7f65-701">HTTP/1.1 only.</span></span> <span data-ttu-id="b7f65-702">Dá se použít s TLS nebo bez něj.</span><span class="sxs-lookup"><span data-stu-id="b7f65-702">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="b7f65-703">Pouze HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="b7f65-703">HTTP/2 only.</span></span> <span data-ttu-id="b7f65-704">Dá se použít bez TLS jenom v případě, že klient podporuje [předchozí režim znalostní báze](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="b7f65-704">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="b7f65-705">HTTP/1.1 a HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="b7f65-705">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="b7f65-706">HTTP/2 vyžaduje připojení TLS a [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) ; v opačném případě se připojení nastaví jako výchozí HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="b7f65-706">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="b7f65-707">Výchozí protokol HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="b7f65-707">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="b7f65-708">Omezení TLS pro HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="b7f65-708">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="b7f65-709">TLS verze 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="b7f65-709">TLS version 1.2 or later</span></span>
* <span data-ttu-id="b7f65-710">Opětovné vyjednávání je zakázané.</span><span class="sxs-lookup"><span data-stu-id="b7f65-710">Renegotiation disabled</span></span>
* <span data-ttu-id="b7f65-711">Komprese vypnuta</span><span class="sxs-lookup"><span data-stu-id="b7f65-711">Compression disabled</span></span>
* <span data-ttu-id="b7f65-712">Minimální velikosti dočasného výměny klíčů:</span><span class="sxs-lookup"><span data-stu-id="b7f65-712">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="b7f65-713">Eliptická křivka Diffie-Hellman (ECDH) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bity minima</span><span class="sxs-lookup"><span data-stu-id="b7f65-713">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum</span></span>
  * <span data-ttu-id="b7f65-714">Omezené pole Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bity minima</span><span class="sxs-lookup"><span data-stu-id="b7f65-714">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bits minimum</span></span>
* <span data-ttu-id="b7f65-715">Šifrovací sada není zakázaná.</span><span class="sxs-lookup"><span data-stu-id="b7f65-715">Cipher suite not blacklisted</span></span>

<span data-ttu-id="b7f65-716">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; s eliptickou křivkou P-256 &lbrack;`FIPS186`&rbrack; je ve výchozím nastavení podporován.</span><span class="sxs-lookup"><span data-stu-id="b7f65-716">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="b7f65-717">Následující příklad povoluje připojení HTTP/1.1 a HTTP/2 na portu 8000.</span><span class="sxs-lookup"><span data-stu-id="b7f65-717">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="b7f65-718">Připojení TLS zabezpečuje pomocí zadaného certifikátu:</span><span class="sxs-lookup"><span data-stu-id="b7f65-718">Connections are secured by TLS with a supplied certificate:</span></span>

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

<span data-ttu-id="b7f65-719">Volitelně můžete vytvořit `IConnectionAdapter` implementaci pro filtrování handshake TLS na jednotlivých připojeních pro konkrétní šifry:</span><span class="sxs-lookup"><span data-stu-id="b7f65-719">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

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

<span data-ttu-id="b7f65-720">*Nastavit protokol z konfigurace*</span><span class="sxs-lookup"><span data-stu-id="b7f65-720">*Set the protocol from configuration*</span></span>

<span data-ttu-id="b7f65-721"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> ve výchozím nastavení volá `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))`, aby se načetla konfigurace Kestrel.</span><span class="sxs-lookup"><span data-stu-id="b7f65-721"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="b7f65-722">V následujícím příkladu *appSettings. JSON* se pro všechny koncové body Kestrel vytvoří výchozí protokol připojení (http/1.1 a HTTP/2):</span><span class="sxs-lookup"><span data-stu-id="b7f65-722">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="b7f65-723">Následující příklad konfiguračního souboru vytvoří protokol připojení pro konkrétní koncový bod:</span><span class="sxs-lookup"><span data-stu-id="b7f65-723">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="b7f65-724">Protokoly zadané v hodnotách přepisu kódu nastavených podle konfigurace</span><span class="sxs-lookup"><span data-stu-id="b7f65-724">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="b7f65-725">Konfigurace přenosu</span><span class="sxs-lookup"><span data-stu-id="b7f65-725">Transport configuration</span></span>

<span data-ttu-id="b7f65-726">S vydáním ASP.NET Core 2,1 není výchozí přenos Kestrel založen na Libuv, ale na základě spravovaných soketů.</span><span class="sxs-lookup"><span data-stu-id="b7f65-726">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="b7f65-727">Jedná se o zásadní změnu pro aplikace ASP.NET Core 2,0 upgradované na 2,1, která volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> a závisí na jednom z následujících balíčků:</span><span class="sxs-lookup"><span data-stu-id="b7f65-727">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="b7f65-728">[Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (přímý odkaz na balíček)</span><span class="sxs-lookup"><span data-stu-id="b7f65-728">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="b7f65-729">Microsoft. AspNetCore. app</span><span class="sxs-lookup"><span data-stu-id="b7f65-729">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="b7f65-730">Pro projekty, které vyžadují použití Libuv:</span><span class="sxs-lookup"><span data-stu-id="b7f65-730">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="b7f65-731">Přidejte do souboru projektu aplikace závislost pro balíček [Microsoft. AspNetCore. Server. Kestrel. Transport. Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) :</span><span class="sxs-lookup"><span data-stu-id="b7f65-731">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="b7f65-732"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>volání:</span><span class="sxs-lookup"><span data-stu-id="b7f65-732">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="b7f65-733">Předpony adresy URL</span><span class="sxs-lookup"><span data-stu-id="b7f65-733">URL prefixes</span></span>

<span data-ttu-id="b7f65-734">Při použití `UseUrls`, `--urls` argumentu příkazového řádku, `urls` konfigurační klíč hostitele nebo proměnná prostředí `ASPNETCORE_URLS`, můžou být předpony adresy URL v některém z následujících formátů.</span><span class="sxs-lookup"><span data-stu-id="b7f65-734">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="b7f65-735">Platné jsou pouze předpony adresy URL protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="b7f65-735">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="b7f65-736">Kestrel nepodporuje protokol HTTPS při konfiguraci vazeb URL pomocí `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-736">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="b7f65-737">Adresa IPv4 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="b7f65-737">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="b7f65-738">`0.0.0.0` je zvláštní případ, který se váže ke všem adresám IPv4.</span><span class="sxs-lookup"><span data-stu-id="b7f65-738">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="b7f65-739">Adresa IPv6 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="b7f65-739">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="b7f65-740">`[::]` je ekvivalent protokolu IPv4 `0.0.0.0`protokolu IPv4.</span><span class="sxs-lookup"><span data-stu-id="b7f65-740">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="b7f65-741">Název hostitele s číslem portu</span><span class="sxs-lookup"><span data-stu-id="b7f65-741">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="b7f65-742">Názvy hostitelů, `*`a `+`nejsou speciální.</span><span class="sxs-lookup"><span data-stu-id="b7f65-742">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="b7f65-743">Cokoli se nerozpoznalo jako platná IP adresa nebo `localhost` se váže ke všem IP adresám IPv4 a IPv6.</span><span class="sxs-lookup"><span data-stu-id="b7f65-743">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="b7f65-744">Pokud chcete navazovat různé názvy hostitelů na různé ASP.NET Core aplikace na stejném portu, použijte [http. sys](xref:fundamentals/servers/httpsys) nebo reverzní proxy server, třeba IIS, Nginx nebo Apache.</span><span class="sxs-lookup"><span data-stu-id="b7f65-744">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="b7f65-745">Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="b7f65-745">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="b7f65-746">Název hostitele `localhost` s číslem portu nebo IP adresou zpětné smyčky s číslem portu</span><span class="sxs-lookup"><span data-stu-id="b7f65-746">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="b7f65-747">Při zadání `localhost` se Kestrel pokusí vytvořit propojení s rozhraními zpětné smyčky IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="b7f65-747">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="b7f65-748">Pokud je požadovaný port používán jinou službou na rozhraní zpětné smyčky, spuštění Kestrel se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="b7f65-748">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="b7f65-749">Pokud je rozhraní zpětné smyčky v žádném jiném případě nedostupné (většinou kvůli tomu, že protokol IPv6 není podporovaný), Kestrel zaznamená upozornění.</span><span class="sxs-lookup"><span data-stu-id="b7f65-749">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="b7f65-750">Filtrování hostitele</span><span class="sxs-lookup"><span data-stu-id="b7f65-750">Host filtering</span></span>

<span data-ttu-id="b7f65-751">I když Kestrel podporuje konfiguraci na základě prefixů, jako je například `http://example.com:5000`, Kestrel převážně ignoruje název hostitele.</span><span class="sxs-lookup"><span data-stu-id="b7f65-751">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="b7f65-752">Hostitelská `localhost` je zvláštní případ, který se používá pro vazbu na adresy zpětné smyčky.</span><span class="sxs-lookup"><span data-stu-id="b7f65-752">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="b7f65-753">Každý hostitel, který je jiný než explicitní IP adresa, se váže na všechny veřejné IP adresy.</span><span class="sxs-lookup"><span data-stu-id="b7f65-753">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="b7f65-754">`Host` hlavičky nejsou ověřeny.</span><span class="sxs-lookup"><span data-stu-id="b7f65-754">`Host` headers aren't validated.</span></span>

<span data-ttu-id="b7f65-755">Jako alternativní řešení použijte middleware pro filtrování hostitele.</span><span class="sxs-lookup"><span data-stu-id="b7f65-755">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="b7f65-756">Middleware pro filtrování hostitele poskytuje balíček [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , který je součástí [Microsoft. AspNetCore. App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 nebo 2,2).</span><span class="sxs-lookup"><span data-stu-id="b7f65-756">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="b7f65-757">Middleware je přidána pomocí <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, která volá <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="b7f65-757">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="b7f65-758">Middleware pro filtrování hostitele je ve výchozím nastavení zakázáno.</span><span class="sxs-lookup"><span data-stu-id="b7f65-758">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="b7f65-759">Chcete-li povolit middleware, definujte `AllowedHosts` klíč v souboru *appSettings. json*/*appSettings.\<prostředí >. JSON*.</span><span class="sxs-lookup"><span data-stu-id="b7f65-759">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="b7f65-760">Hodnota je seznam názvů hostitelů oddělených středníkem bez čísel portů:</span><span class="sxs-lookup"><span data-stu-id="b7f65-760">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="b7f65-761">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="b7f65-761">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="b7f65-762">[Middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer) má taky možnost <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="b7f65-762">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="b7f65-763">Middleware předávaných hlaviček a middleware pro filtrování hostitele mají podobné funkce pro různé scénáře.</span><span class="sxs-lookup"><span data-stu-id="b7f65-763">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="b7f65-764">Nastavení `AllowedHosts` pomocí předávaných middlewarových hlaviček je vhodné, pokud při předávání požadavků pomocí reverzního proxy server nebo nástroje pro vyrovnávání zatížení nejsou zachovány hlavičky `Host`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-764">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="b7f65-765">Nastavení `AllowedHosts` pomocí middlewaru pro filtrování hostitele je vhodné při použití Kestrel jako veřejného hraničního serveru nebo při přímém přeposílání hlavičky `Host`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-765">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="b7f65-766">Další informace o middlewaru předávaných hlaviček najdete v tématu <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="b7f65-766">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="b7f65-767">Kestrel je [webový server pro různé platformy pro ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="b7f65-767">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="b7f65-768">Kestrel je webový server, který je ve výchozím nastavení součástí ASP.NET Core šablon projektů.</span><span class="sxs-lookup"><span data-stu-id="b7f65-768">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="b7f65-769">Kestrel podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="b7f65-769">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="b7f65-770">HTTPS</span><span class="sxs-lookup"><span data-stu-id="b7f65-770">HTTPS</span></span>
* <span data-ttu-id="b7f65-771">Neprůhledný upgrade používaný k povolení [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="b7f65-771">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="b7f65-772">Technologie UNIX Sockets s vysokým výkonem za Nginx</span><span class="sxs-lookup"><span data-stu-id="b7f65-772">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="b7f65-773">Kestrel se podporuje na všech platformách a verzích, které podporuje .NET Core.</span><span class="sxs-lookup"><span data-stu-id="b7f65-773">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="b7f65-774">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="b7f65-774">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="b7f65-775">Kdy použít Kestrel s reverzním proxy serverem</span><span class="sxs-lookup"><span data-stu-id="b7f65-775">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="b7f65-776">Kestrel může používat sám sebe nebo s *reverzními proxy server*, jako je [Internetová informační služba (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="b7f65-776">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="b7f65-777">Reverzní proxy server přijímá požadavky HTTP ze sítě a přesměruje je na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="b7f65-777">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="b7f65-778">Kestrel použitý jako hraniční webový server (internetový):</span><span class="sxs-lookup"><span data-stu-id="b7f65-778">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel komunikuje přímo s internetem bez zpětného proxy server](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="b7f65-780">Kestrel se používá v konfiguraci reverzního proxy serveru:</span><span class="sxs-lookup"><span data-stu-id="b7f65-780">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel nepřímo komunikuje s internetem prostřednictvím reverzní proxy server, jako je IIS, Nginx nebo Apache.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="b7f65-782">Buď konfigurace, s zpětným proxy server nebo bez něj, je podporovaná konfigurace hostování.</span><span class="sxs-lookup"><span data-stu-id="b7f65-782">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="b7f65-783">Kestrel používané jako hraniční Server bez reverzních proxy server nepodporuje sdílení stejné IP adresy a portu mezi více procesy.</span><span class="sxs-lookup"><span data-stu-id="b7f65-783">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="b7f65-784">Když je Kestrel nakonfigurovaný k naslouchání na portu, Kestrel zpracovává veškerý provoz tohoto portu bez ohledu na hlavičky `Host`ch požadavků.</span><span class="sxs-lookup"><span data-stu-id="b7f65-784">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="b7f65-785">Reverzní proxy server, který může sdílet porty, má schopnost přesměrovat požadavky na Kestrel na jedinečné IP adresy a porty.</span><span class="sxs-lookup"><span data-stu-id="b7f65-785">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="b7f65-786">I v případě, že se zpětný proxy server nepožaduje, může být použití reverzní proxy server vhodnou volbou.</span><span class="sxs-lookup"><span data-stu-id="b7f65-786">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="b7f65-787">Reverzní proxy:</span><span class="sxs-lookup"><span data-stu-id="b7f65-787">A reverse proxy:</span></span>

* <span data-ttu-id="b7f65-788">Může omezit vystavenou veřejnou plochu v aplikacích, které hostuje.</span><span class="sxs-lookup"><span data-stu-id="b7f65-788">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="b7f65-789">Poskytněte další vrstvu konfigurace a obrany.</span><span class="sxs-lookup"><span data-stu-id="b7f65-789">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="b7f65-790">Může se lépe integrovat se stávající infrastrukturou.</span><span class="sxs-lookup"><span data-stu-id="b7f65-790">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="b7f65-791">Zjednodušte vyrovnávání zatížení a konfiguraci zabezpečené komunikace (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="b7f65-791">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="b7f65-792">Pouze reverzní proxy server vyžaduje certifikát X. 509 a tento server může komunikovat se servery aplikace v interní síti pomocí prostého protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="b7f65-792">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="b7f65-793">Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="b7f65-793">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="b7f65-794">Použití Kestrel v aplikacích ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="b7f65-794">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="b7f65-795">Balíček [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) je součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="b7f65-795">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="b7f65-796">ASP.NET Core šablony projektu ve výchozím nastavení používají Kestrel.</span><span class="sxs-lookup"><span data-stu-id="b7f65-796">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="b7f65-797">V *program.cs*kód šablony volá <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, která volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> na pozadí.</span><span class="sxs-lookup"><span data-stu-id="b7f65-797">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="b7f65-798">Chcete-li po volání `CreateDefaultBuilder`zadat další konfiguraci, zavolejte <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span><span class="sxs-lookup"><span data-stu-id="b7f65-798">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="b7f65-799">Další informace o `CreateDefaultBuilder` a vytváření hostitele najdete v části *Nastavení hostitele* v <xref:fundamentals/host/web-host#set-up-a-host>.</span><span class="sxs-lookup"><span data-stu-id="b7f65-799">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="b7f65-800">Kestrel možnosti</span><span class="sxs-lookup"><span data-stu-id="b7f65-800">Kestrel options</span></span>

<span data-ttu-id="b7f65-801">Webový server Kestrel má možnosti konfigurace omezení, které jsou zvláště užitečné v internetových nasazeních.</span><span class="sxs-lookup"><span data-stu-id="b7f65-801">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="b7f65-802">Nastavte omezení pro vlastnost <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> třídy <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="b7f65-802">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="b7f65-803">Vlastnost `Limits` obsahuje instanci třídy <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>.</span><span class="sxs-lookup"><span data-stu-id="b7f65-803">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="b7f65-804">V následujících příkladech je použit obor názvů <xref:Microsoft.AspNetCore.Server.Kestrel.Core>:</span><span class="sxs-lookup"><span data-stu-id="b7f65-804">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="b7f65-805">Možnosti Kestrel, které jsou konfigurovány C# v kódu v následujících příkladech, lze také nastavit pomocí [poskytovatele konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="b7f65-805">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="b7f65-806">Poskytovatel konfigurace souboru může například načíst konfiguraci Kestrel z objektu *appSettings. JSON* nebo *appSettings. { Soubor Environment}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="b7f65-806">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="b7f65-807">Použijte **jeden** z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="b7f65-807">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="b7f65-808">Konfigurace Kestrel v `Startup.ConfigureServices`:</span><span class="sxs-lookup"><span data-stu-id="b7f65-808">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="b7f65-809">Vloží instanci `IConfiguration` do `Startup` třídy.</span><span class="sxs-lookup"><span data-stu-id="b7f65-809">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="b7f65-810">Následující příklad předpokládá, že vložená konfigurace je přiřazena vlastnosti `Configuration`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-810">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="b7f65-811">V `Startup.ConfigureServices`načtěte oddíl `Kestrel` konfigurace do konfigurace Kestrel:</span><span class="sxs-lookup"><span data-stu-id="b7f65-811">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

     ```csharp
     using Microsoft.Extensions.Configuration
     
     public class Startup
     {
         public Startup(IConfiguration configuration)
         {
             Configuration = configuration;
         }

         public IConfiguration Configuration { get; }

         public void ConfigureServices(IServiceCollection services)
         {
             services.Configure<KestrelServerOptions>(
                 Configuration.GetSection("Kestrel"));
         }

         public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
         {
             ...
         }
     }
     ```

* <span data-ttu-id="b7f65-812">Nakonfigurovat Kestrel při sestavování hostitele:</span><span class="sxs-lookup"><span data-stu-id="b7f65-812">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="b7f65-813">V *program.cs*načtěte část `Kestrel` konfigurace do konfigurace Kestrel:</span><span class="sxs-lookup"><span data-stu-id="b7f65-813">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="b7f65-814">Obě předchozí přístupy fungují u libovolného [poskytovatele konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="b7f65-814">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="b7f65-815">Časový limit Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="b7f65-815">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="b7f65-816">Získá nebo nastaví [časový limit zachování](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="b7f65-816">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="b7f65-817">Výchozí hodnota je 2 minuty.</span><span class="sxs-lookup"><span data-stu-id="b7f65-817">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="b7f65-818">Maximální počet připojení klientů</span><span class="sxs-lookup"><span data-stu-id="b7f65-818">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="b7f65-819">Maximální počet souběžných otevřených připojení TCP lze pro celou aplikaci nastavit pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="b7f65-819">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="b7f65-820">Existuje zvláštní omezení pro připojení, která byla upgradována z protokolu HTTP nebo HTTPS na jiný protokol (například na žádost WebSockets).</span><span class="sxs-lookup"><span data-stu-id="b7f65-820">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="b7f65-821">Po upgradu připojení se nepočítá s limitem `MaxConcurrentConnections`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-821">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="b7f65-822">Ve výchozím nastavení je maximální počet připojení neomezené (null).</span><span class="sxs-lookup"><span data-stu-id="b7f65-822">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="b7f65-823">Maximální velikost textu požadavku</span><span class="sxs-lookup"><span data-stu-id="b7f65-823">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="b7f65-824">Výchozí maximální velikost textu požadavku je 30 000 000 bajtů, což je přibližně 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="b7f65-824">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="b7f65-825">Doporučený postup pro přepsání limitu v aplikaci ASP.NET Core MVC je použití atributu <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> u metody Action:</span><span class="sxs-lookup"><span data-stu-id="b7f65-825">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="b7f65-826">Tady je příklad, který ukazuje, jak nakonfigurovat omezení pro aplikaci na každém požadavku:</span><span class="sxs-lookup"><span data-stu-id="b7f65-826">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="b7f65-827">Přepsat nastavení u konkrétního požadavku v middleware:</span><span class="sxs-lookup"><span data-stu-id="b7f65-827">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="b7f65-828">Výjimka je vyvolána, pokud aplikace po zahájení aplikace nakonfiguruje limit na žádost.</span><span class="sxs-lookup"><span data-stu-id="b7f65-828">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="b7f65-829">Existuje vlastnost `IsReadOnly`, která určuje, zda je vlastnost `MaxRequestBodySize` ve stavu jen pro čtení, což znamená, že je příliš pozdě pro konfiguraci limitu.</span><span class="sxs-lookup"><span data-stu-id="b7f65-829">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="b7f65-830">Když je aplikace spouštěna [mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za [ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module), je omezení velikosti textu požadavku Kestrel zakázané, protože služba IIS tento limit již nastavuje.</span><span class="sxs-lookup"><span data-stu-id="b7f65-830">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="b7f65-831">Minimální rychlost těla zprávy požadavku</span><span class="sxs-lookup"><span data-stu-id="b7f65-831">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="b7f65-832">Kestrel kontroluje každou sekundu, pokud data přicházejí za zadanou rychlost v bajtech za sekundu.</span><span class="sxs-lookup"><span data-stu-id="b7f65-832">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="b7f65-833">Pokud frekvence klesne pod minimální hodnotu, vypršel časový limit připojení. Doba odkladu je doba, kterou Kestrel umožňuje klientovi zvýšit rychlost odesílání až na minimum; frekvence není během této doby kontrolována.</span><span class="sxs-lookup"><span data-stu-id="b7f65-833">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="b7f65-834">Doba odkladu pomáhá vyhnout se vyřazení připojení, která při počátečním posílání dat zpomalují rychlost, kvůli pomalému spuštění protokolu TCP.</span><span class="sxs-lookup"><span data-stu-id="b7f65-834">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="b7f65-835">Výchozí minimální sazba je 240 bajtů za sekundu s obdobím 5 sekund odkladu.</span><span class="sxs-lookup"><span data-stu-id="b7f65-835">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="b7f65-836">Pro odpověď platí také minimální sazba.</span><span class="sxs-lookup"><span data-stu-id="b7f65-836">A minimum rate also applies to the response.</span></span> <span data-ttu-id="b7f65-837">Kód pro nastavení limitu požadavku a limit odezvy je stejný, s výjimkou `RequestBody` nebo `Response` v názvech vlastností a rozhraní.</span><span class="sxs-lookup"><span data-stu-id="b7f65-837">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="b7f65-838">Tady je příklad, který ukazuje, jak nakonfigurovat minimální datové tarify v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="b7f65-838">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

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

### <a name="request-headers-timeout"></a><span data-ttu-id="b7f65-839">Časový limit hlaviček žádostí</span><span class="sxs-lookup"><span data-stu-id="b7f65-839">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="b7f65-840">Získá nebo nastaví maximální dobu, po kterou server stráví příjem hlaviček požadavků.</span><span class="sxs-lookup"><span data-stu-id="b7f65-840">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="b7f65-841">Výchozí hodnota je 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="b7f65-841">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="b7f65-842">Synchronní v/v</span><span class="sxs-lookup"><span data-stu-id="b7f65-842">Synchronous IO</span></span>

<span data-ttu-id="b7f65-843"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> určuje, zda je pro požadavek a odpověď povolena synchronní v/v.</span><span class="sxs-lookup"><span data-stu-id="b7f65-843"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous IO is allowed for the request and response.</span></span> <span data-ttu-id="b7f65-844">Výchozí hodnota je `true`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-844">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="b7f65-845">Velký počet blokování synchronních vstupně-výstupních operací může vést ke vyčerpání fondu vláken, což aplikaci nereaguje.</span><span class="sxs-lookup"><span data-stu-id="b7f65-845">A large number of blocking synchronous IO operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="b7f65-846">Povolit `AllowSynchronousIO` jenom při použití knihovny, která nepodporuje asynchronní vstupně-výstupní operace.</span><span class="sxs-lookup"><span data-stu-id="b7f65-846">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous IO.</span></span>

<span data-ttu-id="b7f65-847">Následující příklad zakáže synchronní v/v:</span><span class="sxs-lookup"><span data-stu-id="b7f65-847">The following example disables synchronous IO:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="b7f65-848">Informace o dalších možnostech a omezeních Kestrel najdete v tématech:</span><span class="sxs-lookup"><span data-stu-id="b7f65-848">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="b7f65-849">Konfigurace koncového bodu</span><span class="sxs-lookup"><span data-stu-id="b7f65-849">Endpoint configuration</span></span>

<span data-ttu-id="b7f65-850">Ve výchozím nastavení ASP.NET Core váže k:</span><span class="sxs-lookup"><span data-stu-id="b7f65-850">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="b7f65-851">`https://localhost:5001` (Pokud je k dispozici místní vývojový certifikát)</span><span class="sxs-lookup"><span data-stu-id="b7f65-851">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="b7f65-852">Zadejte adresy URL pomocí:</span><span class="sxs-lookup"><span data-stu-id="b7f65-852">Specify URLs using the:</span></span>

* <span data-ttu-id="b7f65-853">`ASPNETCORE_URLS` proměnnou prostředí.</span><span class="sxs-lookup"><span data-stu-id="b7f65-853">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="b7f65-854">`--urls` argument příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="b7f65-854">`--urls` command-line argument.</span></span>
* <span data-ttu-id="b7f65-855">`urls` konfigurační klíč hostitele.</span><span class="sxs-lookup"><span data-stu-id="b7f65-855">`urls` host configuration key.</span></span>
* <span data-ttu-id="b7f65-856">`UseUrls` metoda rozšíření.</span><span class="sxs-lookup"><span data-stu-id="b7f65-856">`UseUrls` extension method.</span></span>

<span data-ttu-id="b7f65-857">Hodnota zadaná pomocí těchto přístupů může být jeden nebo více koncových bodů HTTP a HTTPS (HTTPS, pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="b7f65-857">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="b7f65-858">Nakonfigurujte hodnotu jako seznam oddělený středníkem (například `"Urls": "http://localhost:8000; http://localhost:8001"`).</span><span class="sxs-lookup"><span data-stu-id="b7f65-858">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="b7f65-859">Další informace o těchto přístupůch najdete v tématu [adresy URL serveru](xref:fundamentals/host/web-host#server-urls) a [potlačení konfigurace](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="b7f65-859">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="b7f65-860">Vytvoří se vývojový certifikát:</span><span class="sxs-lookup"><span data-stu-id="b7f65-860">A development certificate is created:</span></span>

* <span data-ttu-id="b7f65-861">Po instalaci [.NET Core SDK](/dotnet/core/sdk) .</span><span class="sxs-lookup"><span data-stu-id="b7f65-861">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="b7f65-862">[Nástroj pro vývoj a certifikáty](xref:aspnetcore-2.1#https) slouží k vytvoření certifikátu.</span><span class="sxs-lookup"><span data-stu-id="b7f65-862">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="b7f65-863">Některé prohlížeče vyžadují udělení explicitního oprávnění pro důvěřování místního vývojového certifikátu.</span><span class="sxs-lookup"><span data-stu-id="b7f65-863">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="b7f65-864">Šablony projektů konfigurují aplikace tak, aby běžely ve výchozím nastavení HTTPS a zahrnovaly [přesměrování https a podporu HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="b7f65-864">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="b7f65-865">Pokud chcete nakonfigurovat předpony a porty adres URL pro Kestrel, volejte <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> nebo <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> metody na <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>.</span><span class="sxs-lookup"><span data-stu-id="b7f65-865">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="b7f65-866">`UseUrls`, argument příkazového řádku `--urls`, `urls` konfigurační klíč hostitele a proměnná prostředí `ASPNETCORE_URLS` fungují, ale mají omezení zaznamenaná později v této části (pro konfiguraci koncového bodu HTTPS musí být k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="b7f65-866">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="b7f65-867">Konfigurace `KestrelServerOptions`:</span><span class="sxs-lookup"><span data-stu-id="b7f65-867">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="b7f65-868">ConfigureEndpointDefaults (Action\<ListenOptions >)</span><span class="sxs-lookup"><span data-stu-id="b7f65-868">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="b7f65-869">Určuje `Action` konfigurace, která se má spustit pro každý zadaný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="b7f65-869">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="b7f65-870">Volání `ConfigureEndpointDefaults` několikrát nahrazuje předchozí `Action`s uvedením posledního `Action`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-870">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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

> [!NOTE]
> <span data-ttu-id="b7f65-871">Koncové body vytvořené voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **před** voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> nebudou použity výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="b7f65-871">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="b7f65-872">ConfigureHttpsDefaults (Action\<HttpsConnectionAdapterOptions >)</span><span class="sxs-lookup"><span data-stu-id="b7f65-872">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="b7f65-873">Určuje `Action` konfigurace, která se má spustit pro každý koncový bod HTTPS.</span><span class="sxs-lookup"><span data-stu-id="b7f65-873">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="b7f65-874">Volání `ConfigureHttpsDefaults` několikrát nahrazuje předchozí `Action`s uvedením posledního `Action`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-874">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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

> [!NOTE]
> <span data-ttu-id="b7f65-875">Koncové body vytvořené voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **před** voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> nebudou použity výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="b7f65-875">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="b7f65-876">Konfigurace (IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="b7f65-876">Configure(IConfiguration)</span></span>

<span data-ttu-id="b7f65-877">Vytvoří zavaděč konfigurace pro nastavení Kestrel, který jako vstup převezme <xref:Microsoft.Extensions.Configuration.IConfiguration>.</span><span class="sxs-lookup"><span data-stu-id="b7f65-877">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="b7f65-878">Konfigurace musí být vymezená na konfigurační oddíl pro Kestrel.</span><span class="sxs-lookup"><span data-stu-id="b7f65-878">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="b7f65-879">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="b7f65-879">ListenOptions.UseHttps</span></span>

<span data-ttu-id="b7f65-880">Nakonfigurujte Kestrel tak, aby používal protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="b7f65-880">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="b7f65-881">rozšíření `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="b7f65-881">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="b7f65-882">`UseHttps` &ndash; nakonfigurovat Kestrel na používání protokolu HTTPS s výchozím certifikátem.</span><span class="sxs-lookup"><span data-stu-id="b7f65-882">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="b7f65-883">Vyvolá výjimku, pokud není nakonfigurován žádný výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="b7f65-883">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="b7f65-884">parametry `ListenOptions.UseHttps`:</span><span class="sxs-lookup"><span data-stu-id="b7f65-884">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="b7f65-885">`filename` je cesta a název souboru certifikátu vzhledem k adresáři, který obsahuje soubory obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="b7f65-885">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="b7f65-886">`password` je heslo vyžadované pro přístup k datům certifikátu X. 509.</span><span class="sxs-lookup"><span data-stu-id="b7f65-886">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="b7f65-887">`configureOptions` je `Action` ke konfiguraci `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-887">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="b7f65-888">Vrátí `ListenOptions`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-888">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="b7f65-889">`storeName` je úložiště certifikátů, ze kterého se má certifikát načíst.</span><span class="sxs-lookup"><span data-stu-id="b7f65-889">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="b7f65-890">`subject` je název subjektu certifikátu.</span><span class="sxs-lookup"><span data-stu-id="b7f65-890">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="b7f65-891">`allowInvalid` určuje, jestli by se měly brát v úvahu neplatné certifikáty, například certifikáty podepsané svým držitelem.</span><span class="sxs-lookup"><span data-stu-id="b7f65-891">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="b7f65-892">`location` je umístění úložiště, ze kterého se má načíst certifikát.</span><span class="sxs-lookup"><span data-stu-id="b7f65-892">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="b7f65-893">`serverCertificate` je certifikát X. 509.</span><span class="sxs-lookup"><span data-stu-id="b7f65-893">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="b7f65-894">V produkčním prostředí musí být HTTPS explicitně nakonfigurovaný.</span><span class="sxs-lookup"><span data-stu-id="b7f65-894">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="b7f65-895">Musí být zadán minimálně výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="b7f65-895">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="b7f65-896">Podporované konfigurace popsané dál:</span><span class="sxs-lookup"><span data-stu-id="b7f65-896">Supported configurations described next:</span></span>

* <span data-ttu-id="b7f65-897">Žádná konfigurace</span><span class="sxs-lookup"><span data-stu-id="b7f65-897">No configuration</span></span>
* <span data-ttu-id="b7f65-898">Nahradit výchozí certifikát z konfigurace</span><span class="sxs-lookup"><span data-stu-id="b7f65-898">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="b7f65-899">Změna výchozích hodnot v kódu</span><span class="sxs-lookup"><span data-stu-id="b7f65-899">Change the defaults in code</span></span>

<span data-ttu-id="b7f65-900">*Žádná konfigurace*</span><span class="sxs-lookup"><span data-stu-id="b7f65-900">*No configuration*</span></span>

<span data-ttu-id="b7f65-901">Kestrel naslouchá na `http://localhost:5000` a `https://localhost:5001` (Pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="b7f65-901">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="b7f65-902">*Nahradit výchozí certifikát z konfigurace*</span><span class="sxs-lookup"><span data-stu-id="b7f65-902">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="b7f65-903">`CreateDefaultBuilder` ve výchozím nastavení volá `Configure(context.Configuration.GetSection("Kestrel"))`, aby se načetla konfigurace Kestrel.</span><span class="sxs-lookup"><span data-stu-id="b7f65-903">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="b7f65-904">Pro Kestrel je k dispozici výchozí schéma konfigurace nastavení aplikace HTTPS.</span><span class="sxs-lookup"><span data-stu-id="b7f65-904">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="b7f65-905">Nakonfigurujte několik koncových bodů, včetně adres URL a certifikátů, které se mají použít, buď ze souboru na disku, nebo z úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="b7f65-905">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="b7f65-906">V následujícím příkladu *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="b7f65-906">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="b7f65-907">Nastavte **AllowInvalid** na `true`, aby bylo možné povolit použití neplatných certifikátů (například certifikátů podepsaných svým držitelem).</span><span class="sxs-lookup"><span data-stu-id="b7f65-907">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="b7f65-908">Libovolný koncový bod HTTPS, který neurčuje certifikát (**HttpsDefaultCert** v následujícím příkladu), se vrátí k certifikátu definovanému v části **certifikáty** > **výchozím** nebo vývojovým certifikátem.</span><span class="sxs-lookup"><span data-stu-id="b7f65-908">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="b7f65-909">Alternativou k použití **cesty** a **hesla** pro libovolný uzel certifikátu je zadání certifikátu pomocí polí úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="b7f65-909">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="b7f65-910">Například **certifikáty** > **výchozí** certifikát lze zadat jako:</span><span class="sxs-lookup"><span data-stu-id="b7f65-910">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="b7f65-911">Poznámky ke schématu:</span><span class="sxs-lookup"><span data-stu-id="b7f65-911">Schema notes:</span></span>

* <span data-ttu-id="b7f65-912">V názvech koncových bodů se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="b7f65-912">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="b7f65-913">Například `HTTPS` a `Https` jsou platné.</span><span class="sxs-lookup"><span data-stu-id="b7f65-913">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="b7f65-914">U každého koncového bodu je vyžadován parametr `Url`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-914">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="b7f65-915">Formát pro tento parametr je stejný jako konfigurační parametr `Urls` nejvyšší úrovně s tím rozdílem, že je omezen na jedinou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="b7f65-915">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="b7f65-916">Tyto koncové body nahrazují hodnoty definované v konfiguraci `Urls` na nejvyšší úrovni místo jejich přidávání do těchto koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="b7f65-916">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="b7f65-917">Koncové body definované v kódu prostřednictvím `Listen` jsou kumulativní s použitím koncových bodů definovaných v konfiguračním oddílu.</span><span class="sxs-lookup"><span data-stu-id="b7f65-917">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="b7f65-918">Oddíl `Certificate` je nepovinný.</span><span class="sxs-lookup"><span data-stu-id="b7f65-918">The `Certificate` section is optional.</span></span> <span data-ttu-id="b7f65-919">Pokud není zadaný oddíl `Certificate`, použijí se výchozí hodnoty definované v dřívějších scénářích.</span><span class="sxs-lookup"><span data-stu-id="b7f65-919">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="b7f65-920">Pokud nejsou k dispozici žádné výchozí hodnoty, Server vyvolá výjimku a nemůže se spustit.</span><span class="sxs-lookup"><span data-stu-id="b7f65-920">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="b7f65-921">Oddíl `Certificate` podporuje jak **cesty**&ndash;**hesla** , tak&ndash;ho **úložiště** **subjektu** .</span><span class="sxs-lookup"><span data-stu-id="b7f65-921">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="b7f65-922">V takovém případě může být definován libovolný počet koncových bodů, pokud to nezpůsobí konflikty portů.</span><span class="sxs-lookup"><span data-stu-id="b7f65-922">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="b7f65-923">`options.Configure(context.Configuration.GetSection("{SECTION}"))` vrátí `KestrelConfigurationLoader` s metodou `.Endpoint(string name, listenOptions => { })`, která se dá použít k doplnění nastavení nakonfigurovaného koncového bodu:</span><span class="sxs-lookup"><span data-stu-id="b7f65-923">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="b7f65-924">k pokračování v iteraci u stávajícího zavaděče, jako je třeba ten, který poskytuje <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, se dá `KestrelServerOptions.ConfigurationLoader` použít přímo.</span><span class="sxs-lookup"><span data-stu-id="b7f65-924">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="b7f65-925">Konfigurační oddíl pro každý koncový bod je k dispozici na možnostech v metodě `Endpoint`, aby bylo možné číst vlastní nastavení.</span><span class="sxs-lookup"><span data-stu-id="b7f65-925">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="b7f65-926">Více konfigurací může být načteno voláním `options.Configure(context.Configuration.GetSection("{SECTION}"))` znovu s jiným oddílem.</span><span class="sxs-lookup"><span data-stu-id="b7f65-926">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="b7f65-927">Použije se jenom poslední konfigurace, pokud se `Load` explicitně nevolá při předchozích instancích.</span><span class="sxs-lookup"><span data-stu-id="b7f65-927">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="b7f65-928">Metapackage nevolá `Load`, aby se jeho výchozí konfigurační oddíl mohl nahradit.</span><span class="sxs-lookup"><span data-stu-id="b7f65-928">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="b7f65-929">`KestrelConfigurationLoader` zrcadlí `Listen` rodinu rozhraní API `KestrelServerOptions` jako `Endpoint` přetížení, takže koncové body kódu a konfigurace lze konfigurovat na stejném místě.</span><span class="sxs-lookup"><span data-stu-id="b7f65-929">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="b7f65-930">Tato přetížení nepoužívají názvy a využívají pouze výchozí nastavení z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="b7f65-930">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="b7f65-931">*Změna výchozích hodnot v kódu*</span><span class="sxs-lookup"><span data-stu-id="b7f65-931">*Change the defaults in code*</span></span>

<span data-ttu-id="b7f65-932">`ConfigureEndpointDefaults` a `ConfigureHttpsDefaults` lze použít ke změně výchozího nastavení pro `ListenOptions` a `HttpsConnectionAdapterOptions`, včetně přepsání výchozího certifikátu zadaného v předchozím scénáři.</span><span class="sxs-lookup"><span data-stu-id="b7f65-932">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="b7f65-933">`ConfigureEndpointDefaults` a `ConfigureHttpsDefaults` by měly být volány před konfigurací koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="b7f65-933">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="b7f65-934">*Podpora Kestrel pro SNI*</span><span class="sxs-lookup"><span data-stu-id="b7f65-934">*Kestrel support for SNI*</span></span>

<span data-ttu-id="b7f65-935">[Indikace názvu serveru (SNI)](https://tools.ietf.org/html/rfc6066#section-3) lze použít k hostování více domén na stejné IP adrese a portu.</span><span class="sxs-lookup"><span data-stu-id="b7f65-935">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="b7f65-936">Aby SNI fungovalo, klient pošle název hostitele pro zabezpečenou relaci serveru během metody handshake TLS, aby server mohl poskytovat správný certifikát.</span><span class="sxs-lookup"><span data-stu-id="b7f65-936">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="b7f65-937">Klient používá poskytnutý certifikát pro šifrovanou komunikaci se serverem během zabezpečené relace, která následuje za metodou handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="b7f65-937">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="b7f65-938">Kestrel podporuje SNI prostřednictvím zpětného volání `ServerCertificateSelector`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-938">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="b7f65-939">Zpětné volání je vyvoláno jednou za připojení, které aplikaci umožní zkontrolovat název hostitele a vybrat příslušný certifikát.</span><span class="sxs-lookup"><span data-stu-id="b7f65-939">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="b7f65-940">Podpora SNI vyžaduje:</span><span class="sxs-lookup"><span data-stu-id="b7f65-940">SNI support requires:</span></span>

* <span data-ttu-id="b7f65-941">Spuštění v cílovém rozhraní `netcoreapp2.1` nebo novějším.</span><span class="sxs-lookup"><span data-stu-id="b7f65-941">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="b7f65-942">V `net461` nebo novějším je zpětné volání vyvoláno, ale `name` je vždy `null`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-942">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="b7f65-943">`name` taky `null`, pokud klient neposkytne parametr názvu hostitele v handshaki TLS.</span><span class="sxs-lookup"><span data-stu-id="b7f65-943">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="b7f65-944">Všechny weby běží na stejné instanci Kestrel.</span><span class="sxs-lookup"><span data-stu-id="b7f65-944">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="b7f65-945">Kestrel nepodporuje sdílení IP adresy a portu napříč několika instancemi bez reverzního proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="b7f65-945">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="b7f65-946">Protokolování připojení</span><span class="sxs-lookup"><span data-stu-id="b7f65-946">Connection logging</span></span>

<span data-ttu-id="b7f65-947">Volání <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> k vygenerování protokolů na úrovni ladění pro komunikaci na úrovni bajtů v rámci připojení.</span><span class="sxs-lookup"><span data-stu-id="b7f65-947">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="b7f65-948">Protokolování připojení je užitečné při řešení problémů v komunikaci nízké úrovně, například během šifrování TLS a za proxy servery.</span><span class="sxs-lookup"><span data-stu-id="b7f65-948">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="b7f65-949">Pokud je `UseConnectionLogging` před `UseHttps`, zaprotokoluje se zašifrovaný provoz.</span><span class="sxs-lookup"><span data-stu-id="b7f65-949">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="b7f65-950">Je-li po `UseHttps`umístěn `UseConnectionLogging`, zašifrovaný provoz se zaznamená do protokolu.</span><span class="sxs-lookup"><span data-stu-id="b7f65-950">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="b7f65-951">Vytvoření vazby na soket TCP</span><span class="sxs-lookup"><span data-stu-id="b7f65-951">Bind to a TCP socket</span></span>

<span data-ttu-id="b7f65-952">Metoda <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> se váže k soketu TCP a obslužná rutina možností umožňuje konfiguraci certifikátu X. 509:</span><span class="sxs-lookup"><span data-stu-id="b7f65-952">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

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

<span data-ttu-id="b7f65-953">Příklad konfiguruje HTTPS pro koncový bod s <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span><span class="sxs-lookup"><span data-stu-id="b7f65-953">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="b7f65-954">Ke konfiguraci dalších nastavení Kestrel pro konkrétní koncové body použijte stejné rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="b7f65-954">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="b7f65-955">Vytvoření vazby na soket UNIX</span><span class="sxs-lookup"><span data-stu-id="b7f65-955">Bind to a Unix socket</span></span>

<span data-ttu-id="b7f65-956">Naslouchat na soketu UNIX pomocí <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> pro zlepšení výkonu pomocí Nginx, jak je znázorněno v tomto příkladu:</span><span class="sxs-lookup"><span data-stu-id="b7f65-956">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

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

* <span data-ttu-id="b7f65-957">V souboru Nginx confiuguration nastavte  > `server``location`položka  > `proxy_pass`.`http://unix:/tmp/{KESTREL SOCKET}:/;`</span><span class="sxs-lookup"><span data-stu-id="b7f65-957">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="b7f65-958">`{KESTREL SOCKET}` je název soketu, který je k dispozici pro <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (například `kestrel-test.sock` v předchozím příkladu).</span><span class="sxs-lookup"><span data-stu-id="b7f65-958">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="b7f65-959">Zajistěte, aby byl soket zapisovatelný pomocí Nginx (například `chmod go+w /tmp/kestrel-test.sock`).</span><span class="sxs-lookup"><span data-stu-id="b7f65-959">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="b7f65-960">Port 0</span><span class="sxs-lookup"><span data-stu-id="b7f65-960">Port 0</span></span>

<span data-ttu-id="b7f65-961">Když je zadané číslo portu `0`, Kestrel se dynamicky váže k dostupnému portu.</span><span class="sxs-lookup"><span data-stu-id="b7f65-961">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="b7f65-962">Následující příklad ukazuje, jak určit, který port Kestrel je skutečně vázaný za běhu:</span><span class="sxs-lookup"><span data-stu-id="b7f65-962">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="b7f65-963">Když se aplikace spustí, výstup okna konzoly indikuje dynamický port, na který se aplikace dá získat:</span><span class="sxs-lookup"><span data-stu-id="b7f65-963">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="b7f65-964">Omezení</span><span class="sxs-lookup"><span data-stu-id="b7f65-964">Limitations</span></span>

<span data-ttu-id="b7f65-965">Konfigurace koncových bodů pomocí následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="b7f65-965">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="b7f65-966">`--urls` argument příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="b7f65-966">`--urls` command-line argument</span></span>
* <span data-ttu-id="b7f65-967">`urls` konfigurační klíč hostitele</span><span class="sxs-lookup"><span data-stu-id="b7f65-967">`urls` host configuration key</span></span>
* <span data-ttu-id="b7f65-968">`ASPNETCORE_URLS` proměnná prostředí</span><span class="sxs-lookup"><span data-stu-id="b7f65-968">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="b7f65-969">Tyto metody jsou užitečné, pokud chcete, aby kód pracoval na jiných serverech než Kestrel.</span><span class="sxs-lookup"><span data-stu-id="b7f65-969">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="b7f65-970">Mějte ale na paměti následující omezení:</span><span class="sxs-lookup"><span data-stu-id="b7f65-970">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="b7f65-971">Protokol HTTPS nelze s těmito přístupy použít, pokud není k dispozici výchozí certifikát v konfiguraci koncového bodu HTTPS (například pomocí konfigurace `KestrelServerOptions` nebo konfiguračního souboru, jak je uvedeno výše v tomto tématu).</span><span class="sxs-lookup"><span data-stu-id="b7f65-971">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="b7f65-972">Při současném použití `Listen` i `UseUrls` se používají koncové body `Listen` `UseUrls` koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="b7f65-972">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="b7f65-973">Konfigurace koncového bodu služby IIS</span><span class="sxs-lookup"><span data-stu-id="b7f65-973">IIS endpoint configuration</span></span>

<span data-ttu-id="b7f65-974">Při použití služby IIS jsou vazby adresy URL pro přepsání vazby služby IIS nastaveny buď `Listen`, nebo `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-974">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="b7f65-975">Další informace najdete v tématu [ASP.NET Core modulu](xref:host-and-deploy/aspnet-core-module) .</span><span class="sxs-lookup"><span data-stu-id="b7f65-975">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="b7f65-976">Konfigurace přenosu</span><span class="sxs-lookup"><span data-stu-id="b7f65-976">Transport configuration</span></span>

<span data-ttu-id="b7f65-977">S vydáním ASP.NET Core 2,1 není výchozí přenos Kestrel založen na Libuv, ale na základě spravovaných soketů.</span><span class="sxs-lookup"><span data-stu-id="b7f65-977">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="b7f65-978">Jedná se o zásadní změnu pro aplikace ASP.NET Core 2,0 upgradované na 2,1, která volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> a závisí na jednom z následujících balíčků:</span><span class="sxs-lookup"><span data-stu-id="b7f65-978">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="b7f65-979">[Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (přímý odkaz na balíček)</span><span class="sxs-lookup"><span data-stu-id="b7f65-979">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="b7f65-980">Microsoft. AspNetCore. app</span><span class="sxs-lookup"><span data-stu-id="b7f65-980">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="b7f65-981">Pro projekty, které vyžadují použití Libuv:</span><span class="sxs-lookup"><span data-stu-id="b7f65-981">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="b7f65-982">Přidejte do souboru projektu aplikace závislost pro balíček [Microsoft. AspNetCore. Server. Kestrel. Transport. Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) :</span><span class="sxs-lookup"><span data-stu-id="b7f65-982">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="b7f65-983"><xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>volání:</span><span class="sxs-lookup"><span data-stu-id="b7f65-983">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="b7f65-984">Předpony adresy URL</span><span class="sxs-lookup"><span data-stu-id="b7f65-984">URL prefixes</span></span>

<span data-ttu-id="b7f65-985">Při použití `UseUrls`, `--urls` argumentu příkazového řádku, `urls` konfigurační klíč hostitele nebo proměnná prostředí `ASPNETCORE_URLS`, můžou být předpony adresy URL v některém z následujících formátů.</span><span class="sxs-lookup"><span data-stu-id="b7f65-985">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="b7f65-986">Platné jsou pouze předpony adresy URL protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="b7f65-986">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="b7f65-987">Kestrel nepodporuje protokol HTTPS při konfiguraci vazeb URL pomocí `UseUrls`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-987">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="b7f65-988">Adresa IPv4 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="b7f65-988">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="b7f65-989">`0.0.0.0` je zvláštní případ, který se váže ke všem adresám IPv4.</span><span class="sxs-lookup"><span data-stu-id="b7f65-989">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="b7f65-990">Adresa IPv6 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="b7f65-990">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="b7f65-991">`[::]` je ekvivalent protokolu IPv4 `0.0.0.0`protokolu IPv4.</span><span class="sxs-lookup"><span data-stu-id="b7f65-991">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="b7f65-992">Název hostitele s číslem portu</span><span class="sxs-lookup"><span data-stu-id="b7f65-992">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="b7f65-993">Názvy hostitelů, `*`a `+`nejsou speciální.</span><span class="sxs-lookup"><span data-stu-id="b7f65-993">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="b7f65-994">Cokoli se nerozpoznalo jako platná IP adresa nebo `localhost` se váže ke všem IP adresám IPv4 a IPv6.</span><span class="sxs-lookup"><span data-stu-id="b7f65-994">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="b7f65-995">Pokud chcete navazovat různé názvy hostitelů na různé ASP.NET Core aplikace na stejném portu, použijte [http. sys](xref:fundamentals/servers/httpsys) nebo reverzní proxy server, třeba IIS, Nginx nebo Apache.</span><span class="sxs-lookup"><span data-stu-id="b7f65-995">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="b7f65-996">Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="b7f65-996">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="b7f65-997">Název hostitele `localhost` s číslem portu nebo IP adresou zpětné smyčky s číslem portu</span><span class="sxs-lookup"><span data-stu-id="b7f65-997">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="b7f65-998">Při zadání `localhost` se Kestrel pokusí vytvořit propojení s rozhraními zpětné smyčky IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="b7f65-998">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="b7f65-999">Pokud je požadovaný port používán jinou službou na rozhraní zpětné smyčky, spuštění Kestrel se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="b7f65-999">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="b7f65-1000">Pokud je rozhraní zpětné smyčky v žádném jiném případě nedostupné (většinou kvůli tomu, že protokol IPv6 není podporovaný), Kestrel zaznamená upozornění.</span><span class="sxs-lookup"><span data-stu-id="b7f65-1000">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="b7f65-1001">Filtrování hostitele</span><span class="sxs-lookup"><span data-stu-id="b7f65-1001">Host filtering</span></span>

<span data-ttu-id="b7f65-1002">I když Kestrel podporuje konfiguraci na základě prefixů, jako je například `http://example.com:5000`, Kestrel převážně ignoruje název hostitele.</span><span class="sxs-lookup"><span data-stu-id="b7f65-1002">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="b7f65-1003">Hostitelská `localhost` je zvláštní případ, který se používá pro vazbu na adresy zpětné smyčky.</span><span class="sxs-lookup"><span data-stu-id="b7f65-1003">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="b7f65-1004">Každý hostitel, který je jiný než explicitní IP adresa, se váže na všechny veřejné IP adresy.</span><span class="sxs-lookup"><span data-stu-id="b7f65-1004">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="b7f65-1005">`Host` hlavičky nejsou ověřeny.</span><span class="sxs-lookup"><span data-stu-id="b7f65-1005">`Host` headers aren't validated.</span></span>

<span data-ttu-id="b7f65-1006">Jako alternativní řešení použijte middleware pro filtrování hostitele.</span><span class="sxs-lookup"><span data-stu-id="b7f65-1006">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="b7f65-1007">Middleware pro filtrování hostitele poskytuje balíček [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , který je součástí [Microsoft. AspNetCore. App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 nebo 2,2).</span><span class="sxs-lookup"><span data-stu-id="b7f65-1007">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="b7f65-1008">Middleware je přidána pomocí <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, která volá <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span><span class="sxs-lookup"><span data-stu-id="b7f65-1008">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="b7f65-1009">Middleware pro filtrování hostitele je ve výchozím nastavení zakázáno.</span><span class="sxs-lookup"><span data-stu-id="b7f65-1009">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="b7f65-1010">Chcete-li povolit middleware, definujte `AllowedHosts` klíč v souboru *appSettings. json*/*appSettings.\<prostředí >. JSON*.</span><span class="sxs-lookup"><span data-stu-id="b7f65-1010">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="b7f65-1011">Hodnota je seznam názvů hostitelů oddělených středníkem bez čísel portů:</span><span class="sxs-lookup"><span data-stu-id="b7f65-1011">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="b7f65-1012">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="b7f65-1012">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="b7f65-1013">[Middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer) má taky možnost <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts>.</span><span class="sxs-lookup"><span data-stu-id="b7f65-1013">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="b7f65-1014">Middleware předávaných hlaviček a middleware pro filtrování hostitele mají podobné funkce pro různé scénáře.</span><span class="sxs-lookup"><span data-stu-id="b7f65-1014">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="b7f65-1015">Nastavení `AllowedHosts` pomocí předávaných middlewarových hlaviček je vhodné, pokud při předávání požadavků pomocí reverzního proxy server nebo nástroje pro vyrovnávání zatížení nejsou zachovány hlavičky `Host`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-1015">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="b7f65-1016">Nastavení `AllowedHosts` pomocí middlewaru pro filtrování hostitele je vhodné při použití Kestrel jako veřejného hraničního serveru nebo při přímém přeposílání hlavičky `Host`.</span><span class="sxs-lookup"><span data-stu-id="b7f65-1016">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="b7f65-1017">Další informace o middlewaru předávaných hlaviček najdete v tématu <xref:host-and-deploy/proxy-load-balancer>.</span><span class="sxs-lookup"><span data-stu-id="b7f65-1017">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="b7f65-1018">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="b7f65-1018">Additional resources</span></span>

* <span data-ttu-id="b7f65-1019">Při použití soketů UNIX v systému Linux se soket při vypnutí aplikace automaticky neodstraní.</span><span class="sxs-lookup"><span data-stu-id="b7f65-1019">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="b7f65-1020">Další informace najdete v [tomto problému GitHubu](https://github.com/dotnet/aspnetcore/issues/14134).</span><span class="sxs-lookup"><span data-stu-id="b7f65-1020">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="b7f65-1021">RFC 7230: syntaxe zprávy a směrování (oddíl 5,4: hostitel)</span><span class="sxs-lookup"><span data-stu-id="b7f65-1021">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
