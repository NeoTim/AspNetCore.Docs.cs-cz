---
title: Implementace webového serveru Kestrel v ASP.NET Core
author: rick-anderson
description: Přečtěte si o Kestrel, webovém serveru pro různé platformy pro ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/04/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/servers/kestrel
ms.openlocfilehash: 03c25c103f03c3f9b17311f468d96907d2498641
ms.sourcegitcommit: fa89d6553378529ae86b388689ac2c6f38281bb9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2020
ms.locfileid: "86060381"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="6742a-103">Implementace webového serveru Kestrel v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6742a-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="6742a-104">Díky [Dykstra](https://github.com/tdykstra), [Novák Rossův](https://github.com/Tratcher)a zablokování [Stephen](https://twitter.com/halter73)</span><span class="sxs-lookup"><span data-stu-id="6742a-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="6742a-105">Kestrel je [webový server pro různé platformy pro ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="6742a-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="6742a-106">Kestrel je webový server, který je ve výchozím nastavení součástí ASP.NET Core šablon projektů.</span><span class="sxs-lookup"><span data-stu-id="6742a-106">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="6742a-107">Kestrel podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="6742a-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="6742a-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="6742a-108">HTTPS</span></span>
* <span data-ttu-id="6742a-109">Neprůhledný upgrade používaný k povolení [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="6742a-109">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="6742a-110">Technologie UNIX Sockets s vysokým výkonem za Nginx</span><span class="sxs-lookup"><span data-stu-id="6742a-110">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="6742a-111">HTTP/2 (kromě macOS &dagger; )</span><span class="sxs-lookup"><span data-stu-id="6742a-111">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="6742a-112">&dagger;HTTP/2 se v budoucí verzi podporuje v macOS.</span><span class="sxs-lookup"><span data-stu-id="6742a-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="6742a-113">Kestrel se podporuje na všech platformách a verzích, které podporuje .NET Core.</span><span class="sxs-lookup"><span data-stu-id="6742a-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="6742a-114">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6742a-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="6742a-115">Podpora HTTP/2</span><span class="sxs-lookup"><span data-stu-id="6742a-115">HTTP/2 support</span></span>

<span data-ttu-id="6742a-116">[Http/2](https://httpwg.org/specs/rfc7540.html) je k dispozici pro ASP.NET Core aplikace, pokud jsou splněné následující základní požadavky:</span><span class="sxs-lookup"><span data-stu-id="6742a-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="6742a-117">Operační systém&dagger;</span><span class="sxs-lookup"><span data-stu-id="6742a-117">Operating system&dagger;</span></span>
  * <span data-ttu-id="6742a-118">Windows Server 2016/Windows 10 nebo novější&Dagger;</span><span class="sxs-lookup"><span data-stu-id="6742a-118">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="6742a-119">Linux s OpenSSL 1.0.2 nebo novějším (například Ubuntu 16,04 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="6742a-119">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="6742a-120">Cílová architektura: .NET Core 2,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="6742a-120">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="6742a-121">Připojení [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="6742a-121">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="6742a-122">Připojení TLS 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="6742a-122">TLS 1.2 or later connection</span></span>

<span data-ttu-id="6742a-123">&dagger;HTTP/2 se v budoucí verzi podporuje v macOS.</span><span class="sxs-lookup"><span data-stu-id="6742a-123">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="6742a-124">&Dagger;Kestrel má omezená podpora HTTP/2 na Windows Serveru 2012 R2 a Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="6742a-124">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="6742a-125">Podpora je omezená, protože seznam podporovaných šifrovacích sad TLS, které jsou k dispozici v těchto operačních systémech, je omezený.</span><span class="sxs-lookup"><span data-stu-id="6742a-125">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="6742a-126">Pro zabezpečení připojení TLS může být vyžadován certifikát vygenerovaný pomocí algoritmu ECDSA (s připojením typu eliptická křivka).</span><span class="sxs-lookup"><span data-stu-id="6742a-126">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="6742a-127">Pokud je vytvořeno připojení HTTP/2, sestavy [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="6742a-127">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="6742a-128">HTTP/2 je ve výchozím nastavení zakázané.</span><span class="sxs-lookup"><span data-stu-id="6742a-128">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="6742a-129">Další informace o konfiguraci najdete v částech [Možnosti Kestrel](#kestrel-options) a [ListenOptions. Protocols](#listenoptionsprotocols) .</span><span class="sxs-lookup"><span data-stu-id="6742a-129">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="6742a-130">Kdy použít Kestrel s reverzním proxy serverem</span><span class="sxs-lookup"><span data-stu-id="6742a-130">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="6742a-131">Kestrel může používat sám sebe nebo s *reverzními proxy server*, jako je [Internetová informační služba (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="6742a-131">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="6742a-132">Reverzní proxy server přijímá požadavky HTTP ze sítě a přesměruje je na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6742a-132">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="6742a-133">Kestrel použitý jako hraniční webový server (internetový):</span><span class="sxs-lookup"><span data-stu-id="6742a-133">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel komunikuje přímo s internetem bez zpětného proxy server](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="6742a-135">Kestrel se používá v konfiguraci reverzního proxy serveru:</span><span class="sxs-lookup"><span data-stu-id="6742a-135">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel nepřímo komunikuje s internetem prostřednictvím reverzní proxy server, jako je IIS, Nginx nebo Apache.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="6742a-137">Buď konfigurace, s zpětným proxy server nebo bez něj, je podporovaná konfigurace hostování.</span><span class="sxs-lookup"><span data-stu-id="6742a-137">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="6742a-138">Kestrel používané jako hraniční Server bez reverzních proxy server nepodporuje sdílení stejné IP adresy a portu mezi více procesy.</span><span class="sxs-lookup"><span data-stu-id="6742a-138">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="6742a-139">Když je Kestrel nakonfigurovaný k naslouchání na portu, Kestrel zpracovává veškerý provoz tohoto portu bez ohledu na `Host` hlavičky požadavků.</span><span class="sxs-lookup"><span data-stu-id="6742a-139">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="6742a-140">Reverzní proxy server, který může sdílet porty, má schopnost přesměrovat požadavky na Kestrel na jedinečné IP adresy a porty.</span><span class="sxs-lookup"><span data-stu-id="6742a-140">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="6742a-141">I v případě, že se zpětný proxy server nepožaduje, může být použití reverzní proxy server vhodnou volbou.</span><span class="sxs-lookup"><span data-stu-id="6742a-141">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="6742a-142">Reverzní proxy:</span><span class="sxs-lookup"><span data-stu-id="6742a-142">A reverse proxy:</span></span>

* <span data-ttu-id="6742a-143">Může omezit vystavenou veřejnou plochu v aplikacích, které hostuje.</span><span class="sxs-lookup"><span data-stu-id="6742a-143">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="6742a-144">Poskytněte další vrstvu konfigurace a obrany.</span><span class="sxs-lookup"><span data-stu-id="6742a-144">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="6742a-145">Může se lépe integrovat se stávající infrastrukturou.</span><span class="sxs-lookup"><span data-stu-id="6742a-145">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="6742a-146">Zjednodušte vyrovnávání zatížení a konfiguraci zabezpečené komunikace (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="6742a-146">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="6742a-147">Pouze reverzní proxy server vyžaduje certifikát X. 509 a tento server může komunikovat se servery aplikace v interní síti pomocí prostého protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="6742a-147">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="6742a-148">Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="6742a-148">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="kestrel-in-aspnet-core-apps"></a><span data-ttu-id="6742a-149">Kestrel v aplikacích ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6742a-149">Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="6742a-150">ASP.NET Core šablony projektu ve výchozím nastavení používají Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6742a-150">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="6742a-151">V *program.cs* <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> metoda volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> :</span><span class="sxs-lookup"><span data-stu-id="6742a-151">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="6742a-152">Další informace o vytváření hostitele najdete v oddílech nastavení *hostitele* a *výchozí tvůrce nastavení* v tématu <xref:fundamentals/host/generic-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="6742a-152">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="6742a-153">Chcete-li po volání poskytnout další konfiguraci `ConfigureWebHostDefaults` , použijte `ConfigureKestrel` :</span><span class="sxs-lookup"><span data-stu-id="6742a-153">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="6742a-154">Kestrel možnosti</span><span class="sxs-lookup"><span data-stu-id="6742a-154">Kestrel options</span></span>

<span data-ttu-id="6742a-155">Webový server Kestrel má možnosti konfigurace omezení, které jsou zvláště užitečné v internetových nasazeních.</span><span class="sxs-lookup"><span data-stu-id="6742a-155">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="6742a-156">Nastavte omezení pro <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> vlastnost <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> třídy.</span><span class="sxs-lookup"><span data-stu-id="6742a-156">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="6742a-157">`Limits`Vlastnost obsahuje instanci <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> třídy.</span><span class="sxs-lookup"><span data-stu-id="6742a-157">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="6742a-158">V následujících příkladech se používá <xref:Microsoft.AspNetCore.Server.Kestrel.Core> obor názvů:</span><span class="sxs-lookup"><span data-stu-id="6742a-158">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="6742a-159">V příkladech, které jsou uvedeny dále v tomto článku, jsou možnosti Kestrel konfigurovány v kódu jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="6742a-159">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="6742a-160">Možnosti Kestrel lze také nastavit pomocí [poskytovatele konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="6742a-160">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="6742a-161">[Poskytovatel konfigurace souboru](xref:fundamentals/configuration/index#file-configuration-provider) může například načíst konfiguraci Kestrel z *appsettings.jsna* nebo *appSettings. { Soubor Environment}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="6742a-161">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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
> <span data-ttu-id="6742a-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>[Konfigurace koncových bodů](#endpoint-configuration) se dá konfigurovat z poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="6742a-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](#endpoint-configuration) are configurable from configuration providers.</span></span> <span data-ttu-id="6742a-163">Zbývající konfigurace Kestrel musí být nakonfigurovaná v kódu C#.</span><span class="sxs-lookup"><span data-stu-id="6742a-163">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="6742a-164">Použijte **jeden** z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="6742a-164">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="6742a-165">Nakonfigurovat Kestrel v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6742a-165">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="6742a-166">Vloží instanci `IConfiguration` do `Startup` třídy.</span><span class="sxs-lookup"><span data-stu-id="6742a-166">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="6742a-167">Následující příklad předpokládá, že vložená konfigurace je přiřazena `Configuration` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="6742a-167">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="6742a-168">V aplikaci `Startup.ConfigureServices` načtěte `Kestrel` část konfigurace do konfigurace Kestrel:</span><span class="sxs-lookup"><span data-stu-id="6742a-168">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="6742a-169">Nakonfigurovat Kestrel při sestavování hostitele:</span><span class="sxs-lookup"><span data-stu-id="6742a-169">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="6742a-170">V *program.cs*načtěte `Kestrel` část konfigurace do konfigurace Kestrel:</span><span class="sxs-lookup"><span data-stu-id="6742a-170">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="6742a-171">Obě předchozí přístupy fungují u libovolného [poskytovatele konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="6742a-171">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="6742a-172">Časový limit Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="6742a-172">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="6742a-173">Získá nebo nastaví [časový limit zachování](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="6742a-173">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="6742a-174">Výchozí hodnota je 2 minuty.</span><span class="sxs-lookup"><span data-stu-id="6742a-174">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="6742a-175">Maximální počet připojení klientů</span><span class="sxs-lookup"><span data-stu-id="6742a-175">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="6742a-176">Maximální počet souběžných otevřených připojení TCP lze pro celou aplikaci nastavit pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="6742a-176">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="6742a-177">Existuje zvláštní omezení pro připojení, která byla upgradována z protokolu HTTP nebo HTTPS na jiný protokol (například na žádost WebSockets).</span><span class="sxs-lookup"><span data-stu-id="6742a-177">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="6742a-178">Po upgradu připojení se nepočítá na základě `MaxConcurrentConnections` limitu.</span><span class="sxs-lookup"><span data-stu-id="6742a-178">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="6742a-179">Ve výchozím nastavení je maximální počet připojení neomezené (null).</span><span class="sxs-lookup"><span data-stu-id="6742a-179">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="6742a-180">Maximální velikost textu požadavku</span><span class="sxs-lookup"><span data-stu-id="6742a-180">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="6742a-181">Výchozí maximální velikost textu požadavku je 30 000 000 bajtů, což je přibližně 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="6742a-181">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="6742a-182">Doporučený postup pro přepsání limitu v aplikaci ASP.NET Core MVC je použití <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> atributu pro metodu akce:</span><span class="sxs-lookup"><span data-stu-id="6742a-182">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="6742a-183">Tady je příklad, který ukazuje, jak nakonfigurovat omezení pro aplikaci na každém požadavku:</span><span class="sxs-lookup"><span data-stu-id="6742a-183">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="6742a-184">Přepsat nastavení u konkrétního požadavku v middleware:</span><span class="sxs-lookup"><span data-stu-id="6742a-184">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="6742a-185">Výjimka je vyvolána, pokud aplikace po zahájení aplikace nakonfiguruje limit na žádost.</span><span class="sxs-lookup"><span data-stu-id="6742a-185">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="6742a-186">Existuje `IsReadOnly` vlastnost, která určuje, zda `MaxRequestBodySize` je vlastnost ve stavu jen pro čtení, což znamená, že je příliš pozdě pro konfiguraci limitu.</span><span class="sxs-lookup"><span data-stu-id="6742a-186">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="6742a-187">Když je aplikace spouštěna [mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za [ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module), je omezení velikosti textu požadavku Kestrel zakázané, protože služba IIS tento limit již nastavuje.</span><span class="sxs-lookup"><span data-stu-id="6742a-187">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="6742a-188">Minimální rychlost těla zprávy požadavku</span><span class="sxs-lookup"><span data-stu-id="6742a-188">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="6742a-189">Kestrel kontroluje každou sekundu, pokud data přicházejí za zadanou rychlost v bajtech za sekundu.</span><span class="sxs-lookup"><span data-stu-id="6742a-189">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="6742a-190">Pokud frekvence klesne pod minimální hodnotu, vypršel časový limit připojení. Doba odkladu je doba, kterou Kestrel umožňuje klientovi zvýšit rychlost odesílání až na minimum; frekvence není během této doby kontrolována.</span><span class="sxs-lookup"><span data-stu-id="6742a-190">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="6742a-191">Doba odkladu pomáhá vyhnout se vyřazení připojení, která při počátečním posílání dat zpomalují rychlost, kvůli pomalému spuštění protokolu TCP.</span><span class="sxs-lookup"><span data-stu-id="6742a-191">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="6742a-192">Výchozí minimální sazba je 240 bajtů za sekundu s obdobím 5 sekund odkladu.</span><span class="sxs-lookup"><span data-stu-id="6742a-192">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="6742a-193">Pro odpověď platí také minimální sazba.</span><span class="sxs-lookup"><span data-stu-id="6742a-193">A minimum rate also applies to the response.</span></span> <span data-ttu-id="6742a-194">Kód pro nastavení limitu požadavku a limit odezvy je stejný, s výjimkou, `RequestBody` že existují nebo `Response` v názvech vlastností a rozhraní.</span><span class="sxs-lookup"><span data-stu-id="6742a-194">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="6742a-195">Tady je příklad, který ukazuje, jak nakonfigurovat minimální datové tarify v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="6742a-195">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="6742a-196">Přepsat minimální limity přenosové rychlosti na žádost v middlewaru:</span><span class="sxs-lookup"><span data-stu-id="6742a-196">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="6742a-197"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature>Odkazovaná v předchozí ukázce není k dispozici `HttpContext.Features` pro požadavky HTTP/2, protože Změna omezení přenosové rychlosti u jednotlivých požadavků je obecně Nepodporovaná pro HTTP/2 kvůli podpoře protokolu pro multiplexování požadavků.</span><span class="sxs-lookup"><span data-stu-id="6742a-197">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="6742a-198"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> `HttpContext.Features` Pro požadavky HTTP/2 se ale stále používá, protože omezení četnosti čtení může být pořád *zcela zakázané* na základě jednotlivých požadavků nastavením `IHttpMinRequestBodyDataRateFeature.MinDataRate` na `null` i pro požadavek HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="6742a-198">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="6742a-199">Když se pokusíte o jeho navýšení `IHttpMinRequestBodyDataRateFeature.MinDataRate` nebo pokus o jeho nastavení na jinou hodnotu než, `null` vygeneruje se `NotSupportedException` mu požadavek HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="6742a-199">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="6742a-200">Omezení přenosové rychlosti pro všechny servery jsou nakonfigurovaná přes `KestrelServerOptions.Limits` protokol HTTP/1. x i pro připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="6742a-200">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="6742a-201">Časový limit hlaviček žádostí</span><span class="sxs-lookup"><span data-stu-id="6742a-201">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="6742a-202">Získá nebo nastaví maximální dobu, po kterou server stráví příjem hlaviček požadavků.</span><span class="sxs-lookup"><span data-stu-id="6742a-202">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="6742a-203">Výchozí hodnota je 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="6742a-203">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="6742a-204">Maximální počet proudů na připojení</span><span class="sxs-lookup"><span data-stu-id="6742a-204">Maximum streams per connection</span></span>

<span data-ttu-id="6742a-205">`Http2.MaxStreamsPerConnection`omezuje počet souběžných datových proudů požadavků na připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="6742a-205">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="6742a-206">Nadbytečné proudy jsou odmítnuty.</span><span class="sxs-lookup"><span data-stu-id="6742a-206">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="6742a-207">Výchozí hodnota je 100.</span><span class="sxs-lookup"><span data-stu-id="6742a-207">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="6742a-208">Velikost tabulky hlaviček</span><span class="sxs-lookup"><span data-stu-id="6742a-208">Header table size</span></span>

<span data-ttu-id="6742a-209">Dekodér HPACK dekomprimuje hlavičky HTTP pro připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="6742a-209">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="6742a-210">`Http2.HeaderTableSize`omezuje velikost kompresní tabulky hlaviček, kterou používá dekodér HPACK.</span><span class="sxs-lookup"><span data-stu-id="6742a-210">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="6742a-211">Hodnota je uvedena v oktetech a musí být větší než nula (0).</span><span class="sxs-lookup"><span data-stu-id="6742a-211">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="6742a-212">Výchozí hodnota je 4096.</span><span class="sxs-lookup"><span data-stu-id="6742a-212">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="6742a-213">Maximální velikost rámce</span><span class="sxs-lookup"><span data-stu-id="6742a-213">Maximum frame size</span></span>

<span data-ttu-id="6742a-214">`Http2.MaxFrameSize`Určuje maximální povolenou velikost datové části rámce připojení HTTP/2 přijatého nebo odesílaného serverem.</span><span class="sxs-lookup"><span data-stu-id="6742a-214">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="6742a-215">Hodnota je uvedena v oktetech a musí být v rozmezí 2 ^ 14 (16 384) až 2 ^ 24-1 (16 777 215).</span><span class="sxs-lookup"><span data-stu-id="6742a-215">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="6742a-216">Výchozí hodnota je 2 ^ 14 (16 384).</span><span class="sxs-lookup"><span data-stu-id="6742a-216">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="6742a-217">Maximální velikost hlavičky požadavku</span><span class="sxs-lookup"><span data-stu-id="6742a-217">Maximum request header size</span></span>

<span data-ttu-id="6742a-218">`Http2.MaxRequestHeaderFieldSize`Určuje maximální povolenou velikost v oktetech hodnot hlaviček požadavku.</span><span class="sxs-lookup"><span data-stu-id="6742a-218">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="6742a-219">Toto omezení se vztahuje na název i hodnotu v jejich komprimovaných a nekomprimovaných reprezentujcích.</span><span class="sxs-lookup"><span data-stu-id="6742a-219">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="6742a-220">Hodnota musí být větší než nula (0).</span><span class="sxs-lookup"><span data-stu-id="6742a-220">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="6742a-221">Výchozí hodnota je 8 192.</span><span class="sxs-lookup"><span data-stu-id="6742a-221">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="6742a-222">Velikost okna počátečního připojení</span><span class="sxs-lookup"><span data-stu-id="6742a-222">Initial connection window size</span></span>

<span data-ttu-id="6742a-223">`Http2.InitialConnectionWindowSize`Určuje maximální velikost textu požadavku v bajtech, které jsou v jednom okamžiku agregovány v rámci všech požadavků (datových proudů) na připojení.</span><span class="sxs-lookup"><span data-stu-id="6742a-223">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="6742a-224">Žádosti jsou také omezené pomocí `Http2.InitialStreamWindowSize` .</span><span class="sxs-lookup"><span data-stu-id="6742a-224">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="6742a-225">Hodnota musí být větší než nebo rovna 65 535 a menší než 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="6742a-225">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="6742a-226">Výchozí hodnota je 128 KB (131 072).</span><span class="sxs-lookup"><span data-stu-id="6742a-226">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="6742a-227">Počáteční velikost okna datového proudu</span><span class="sxs-lookup"><span data-stu-id="6742a-227">Initial stream window size</span></span>

<span data-ttu-id="6742a-228">`Http2.InitialStreamWindowSize`Určuje maximální velikost textu požadavku v bajtech na jednu žádost (Stream) na jednom místě.</span><span class="sxs-lookup"><span data-stu-id="6742a-228">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="6742a-229">Žádosti jsou také omezené pomocí `Http2.InitialConnectionWindowSize` .</span><span class="sxs-lookup"><span data-stu-id="6742a-229">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="6742a-230">Hodnota musí být větší než nebo rovna 65 535 a menší než 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="6742a-230">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="6742a-231">Výchozí hodnota je 96 KB (98 304).</span><span class="sxs-lookup"><span data-stu-id="6742a-231">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="6742a-232">Synchronní vstupně-výstupní operace</span><span class="sxs-lookup"><span data-stu-id="6742a-232">Synchronous I/O</span></span>

<span data-ttu-id="6742a-233"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>Určuje, jestli je pro požadavek a odpověď povolený synchronní vstup/výstup.</span><span class="sxs-lookup"><span data-stu-id="6742a-233"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="6742a-234">Výchozí hodnota je `false`.</span><span class="sxs-lookup"><span data-stu-id="6742a-234">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="6742a-235">Velký počet blokujících synchronních vstupně-výstupních operací může vést ke vyčerpání fondu vláken, což aplikaci nereaguje.</span><span class="sxs-lookup"><span data-stu-id="6742a-235">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="6742a-236">Povolit pouze `AllowSynchronousIO` při použití knihovny, která nepodporuje asynchronní vstupně-výstupní operace.</span><span class="sxs-lookup"><span data-stu-id="6742a-236">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="6742a-237">Následující příklad povoluje synchronní vstupně-výstupní operace:</span><span class="sxs-lookup"><span data-stu-id="6742a-237">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="6742a-238">Informace o dalších možnostech a omezeních Kestrel najdete v tématech:</span><span class="sxs-lookup"><span data-stu-id="6742a-238">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="6742a-239">Konfigurace koncového bodu</span><span class="sxs-lookup"><span data-stu-id="6742a-239">Endpoint configuration</span></span>

<span data-ttu-id="6742a-240">Ve výchozím nastavení ASP.NET Core váže k:</span><span class="sxs-lookup"><span data-stu-id="6742a-240">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="6742a-241">`https://localhost:5001`(Pokud je k dispozici místní vývojový certifikát)</span><span class="sxs-lookup"><span data-stu-id="6742a-241">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="6742a-242">Zadejte adresy URL pomocí:</span><span class="sxs-lookup"><span data-stu-id="6742a-242">Specify URLs using the:</span></span>

* <span data-ttu-id="6742a-243">`ASPNETCORE_URLS`Proměnná prostředí.</span><span class="sxs-lookup"><span data-stu-id="6742a-243">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="6742a-244">`--urls`argument příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="6742a-244">`--urls` command-line argument.</span></span>
* <span data-ttu-id="6742a-245">`urls`konfigurační klíč hostitele.</span><span class="sxs-lookup"><span data-stu-id="6742a-245">`urls` host configuration key.</span></span>
* <span data-ttu-id="6742a-246">`UseUrls`Metoda rozšíření</span><span class="sxs-lookup"><span data-stu-id="6742a-246">`UseUrls` extension method.</span></span>

<span data-ttu-id="6742a-247">Hodnota zadaná pomocí těchto přístupů může být jeden nebo více koncových bodů HTTP a HTTPS (HTTPS, pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="6742a-247">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="6742a-248">Nakonfigurujte hodnotu jako seznam oddělený středníkem (například `"Urls": "http://localhost:8000;http://localhost:8001"` ).</span><span class="sxs-lookup"><span data-stu-id="6742a-248">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="6742a-249">Další informace o těchto přístupůch najdete v tématu [adresy URL serveru](xref:fundamentals/host/web-host#server-urls) a [potlačení konfigurace](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="6742a-249">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="6742a-250">Vytvoří se vývojový certifikát:</span><span class="sxs-lookup"><span data-stu-id="6742a-250">A development certificate is created:</span></span>

* <span data-ttu-id="6742a-251">Po instalaci [.NET Core SDK](/dotnet/core/sdk) .</span><span class="sxs-lookup"><span data-stu-id="6742a-251">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="6742a-252">[Nástroj pro vývoj a certifikáty](xref:aspnetcore-2.1#https) slouží k vytvoření certifikátu.</span><span class="sxs-lookup"><span data-stu-id="6742a-252">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="6742a-253">Některé prohlížeče vyžadují udělení explicitního oprávnění pro důvěřování místního vývojového certifikátu.</span><span class="sxs-lookup"><span data-stu-id="6742a-253">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="6742a-254">Šablony projektů konfigurují aplikace tak, aby běžely ve výchozím nastavení HTTPS a zahrnovaly [přesměrování https a podporu HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="6742a-254">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="6742a-255">Volání <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> nebo <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> metody pro <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> konfiguraci předpon adres URL a portů pro Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6742a-255">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="6742a-256">`UseUrls`, `--urls` argument příkazového řádku, `urls` konfigurační klíč hostitele a `ASPNETCORE_URLS` Proměnná prostředí fungují také, ale mají omezení zaznamenaná později v této části (pro konfiguraci koncového bodu https musí být k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="6742a-256">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="6742a-257">`KestrelServerOptions`rozšířeného</span><span class="sxs-lookup"><span data-stu-id="6742a-257">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="6742a-258">ConfigureEndpointDefaults (akce \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="6742a-258">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="6742a-259">Určuje konfiguraci `Action` , která se spustí pro každý zadaný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="6742a-259">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="6742a-260">Volání několikrát `ConfigureEndpointDefaults` nahrazuje předchozí `Action` `Action` zadanou parametr s.</span><span class="sxs-lookup"><span data-stu-id="6742a-260">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="6742a-261">Koncové body vytvořené voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **před** voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> nebudou použity výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="6742a-261">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="6742a-262">ConfigureHttpsDefaults (akce \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="6742a-262">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="6742a-263">Určuje konfiguraci `Action` , která se má spustit pro každý koncový bod HTTPS.</span><span class="sxs-lookup"><span data-stu-id="6742a-263">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="6742a-264">Volání několikrát `ConfigureHttpsDefaults` nahrazuje předchozí `Action` `Action` zadanou parametr s.</span><span class="sxs-lookup"><span data-stu-id="6742a-264">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="6742a-265">Koncové body vytvořené voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **před** voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> nebudou použity výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="6742a-265">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="6742a-266">Konfigurace (IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="6742a-266">Configure(IConfiguration)</span></span>

<span data-ttu-id="6742a-267">Vytvoří zavaděč konfigurace pro nastavení Kestrel, který přebírá <xref:Microsoft.Extensions.Configuration.IConfiguration> jako vstup.</span><span class="sxs-lookup"><span data-stu-id="6742a-267">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="6742a-268">Konfigurace musí být vymezená na konfigurační oddíl pro Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6742a-268">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="6742a-269">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="6742a-269">ListenOptions.UseHttps</span></span>

<span data-ttu-id="6742a-270">Nakonfigurujte Kestrel tak, aby používal protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="6742a-270">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="6742a-271">`ListenOptions.UseHttps`SND</span><span class="sxs-lookup"><span data-stu-id="6742a-271">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="6742a-272">`UseHttps`: Nakonfigurujte Kestrel tak, aby používal HTTPS s výchozím certifikátem.</span><span class="sxs-lookup"><span data-stu-id="6742a-272">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="6742a-273">Vyvolá výjimku, pokud není nakonfigurován žádný výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="6742a-273">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="6742a-274">`ListenOptions.UseHttps`ukazatelů</span><span class="sxs-lookup"><span data-stu-id="6742a-274">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="6742a-275">`filename`je cesta a název souboru certifikátu vzhledem k adresáři, který obsahuje soubory obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="6742a-275">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="6742a-276">`password`je vyžadováno heslo pro přístup k datům certifikátu X. 509.</span><span class="sxs-lookup"><span data-stu-id="6742a-276">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="6742a-277">`configureOptions`je `Action` ke konfiguraci `HttpsConnectionAdapterOptions` .</span><span class="sxs-lookup"><span data-stu-id="6742a-277">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="6742a-278">Vrátí `ListenOptions` .</span><span class="sxs-lookup"><span data-stu-id="6742a-278">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="6742a-279">`storeName`je úložiště certifikátů, ze kterého se má certifikát načíst.</span><span class="sxs-lookup"><span data-stu-id="6742a-279">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="6742a-280">`subject`je název subjektu certifikátu.</span><span class="sxs-lookup"><span data-stu-id="6742a-280">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="6742a-281">`allowInvalid`Určuje, zda by měly být zváženy neplatné certifikáty, například certifikáty podepsané svým držitelem.</span><span class="sxs-lookup"><span data-stu-id="6742a-281">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="6742a-282">`location`je umístěním úložiště, ze kterého se má certifikát načíst.</span><span class="sxs-lookup"><span data-stu-id="6742a-282">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="6742a-283">`serverCertificate`je certifikát X. 509.</span><span class="sxs-lookup"><span data-stu-id="6742a-283">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="6742a-284">V produkčním prostředí musí být HTTPS explicitně nakonfigurovaný.</span><span class="sxs-lookup"><span data-stu-id="6742a-284">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="6742a-285">Musí být zadán minimálně výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="6742a-285">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="6742a-286">Podporované konfigurace popsané dál:</span><span class="sxs-lookup"><span data-stu-id="6742a-286">Supported configurations described next:</span></span>

* <span data-ttu-id="6742a-287">Žádná konfigurace</span><span class="sxs-lookup"><span data-stu-id="6742a-287">No configuration</span></span>
* <span data-ttu-id="6742a-288">Nahradit výchozí certifikát z konfigurace</span><span class="sxs-lookup"><span data-stu-id="6742a-288">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="6742a-289">Změna výchozích hodnot v kódu</span><span class="sxs-lookup"><span data-stu-id="6742a-289">Change the defaults in code</span></span>

<span data-ttu-id="6742a-290">*Žádná konfigurace*</span><span class="sxs-lookup"><span data-stu-id="6742a-290">*No configuration*</span></span>

<span data-ttu-id="6742a-291">Kestrel naslouchá na `http://localhost:5000` a `https://localhost:5001` (Pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="6742a-291">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="6742a-292">*Nahradit výchozí certifikát z konfigurace*</span><span class="sxs-lookup"><span data-stu-id="6742a-292">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="6742a-293">`CreateDefaultBuilder`Při `Configure(context.Configuration.GetSection("Kestrel"))` načítání konfigurace Kestrel ve výchozím nastavení volá.</span><span class="sxs-lookup"><span data-stu-id="6742a-293">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="6742a-294">Pro Kestrel je k dispozici výchozí schéma konfigurace nastavení aplikace HTTPS.</span><span class="sxs-lookup"><span data-stu-id="6742a-294">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="6742a-295">Nakonfigurujte několik koncových bodů, včetně adres URL a certifikátů, které se mají použít, buď ze souboru na disku, nebo z úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="6742a-295">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="6742a-296">V následujícím *appsettings.js* například:</span><span class="sxs-lookup"><span data-stu-id="6742a-296">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="6742a-297">Nastavte **AllowInvalid** na `true` , aby povolovala použití neplatných certifikátů (například certifikátů podepsaných svým držitelem).</span><span class="sxs-lookup"><span data-stu-id="6742a-297">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="6742a-298">Libovolný koncový bod HTTPS, který neurčuje certifikát (**HttpsDefaultCert** v následujícím příkladu), se vrátí k certifikátu definovanému v části **certifikáty** > **výchozí** nebo vývojový certifikát.</span><span class="sxs-lookup"><span data-stu-id="6742a-298">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="6742a-299">Alternativou k použití **cesty** a **hesla** pro libovolný uzel certifikátu je zadání certifikátu pomocí polí úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="6742a-299">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="6742a-300">Například **Certificates**  >  **výchozí** certifikát může být zadán jako:</span><span class="sxs-lookup"><span data-stu-id="6742a-300">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="6742a-301">Poznámky ke schématu:</span><span class="sxs-lookup"><span data-stu-id="6742a-301">Schema notes:</span></span>

* <span data-ttu-id="6742a-302">V názvech koncových bodů se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="6742a-302">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="6742a-303">Například `HTTPS` a `Https` jsou platné.</span><span class="sxs-lookup"><span data-stu-id="6742a-303">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="6742a-304">`Url`U každého koncového bodu je vyžadován parametr.</span><span class="sxs-lookup"><span data-stu-id="6742a-304">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="6742a-305">Formát pro tento parametr je stejný jako `Urls` konfigurační parametr nejvyšší úrovně s tím rozdílem, že je omezen na jedinou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="6742a-305">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="6742a-306">Tyto koncové body nahrazují hodnoty definované v konfiguraci nejvyšší úrovně `Urls` místo jejich přidání do těchto koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="6742a-306">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="6742a-307">Koncové body definované v kódu prostřednictvím `Listen` jsou kumulativní pomocí koncových bodů definovaných v konfiguračním oddílu.</span><span class="sxs-lookup"><span data-stu-id="6742a-307">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="6742a-308">`Certificate`Oddíl je nepovinný.</span><span class="sxs-lookup"><span data-stu-id="6742a-308">The `Certificate` section is optional.</span></span> <span data-ttu-id="6742a-309">Pokud `Certificate` není oddíl zadaný, použijí se výchozí hodnoty definované v dřívějších scénářích.</span><span class="sxs-lookup"><span data-stu-id="6742a-309">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="6742a-310">Pokud nejsou k dispozici žádné výchozí hodnoty, Server vyvolá výjimku a nemůže se spustit.</span><span class="sxs-lookup"><span data-stu-id="6742a-310">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="6742a-311">`Certificate`Oddíl podporuje jak heslo **cesty** &ndash; **Password** , tak **Subject** &ndash; certifikáty**úložiště** subjektu.</span><span class="sxs-lookup"><span data-stu-id="6742a-311">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="6742a-312">V takovém případě může být definován libovolný počet koncových bodů, pokud to nezpůsobí konflikty portů.</span><span class="sxs-lookup"><span data-stu-id="6742a-312">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="6742a-313">`options.Configure(context.Configuration.GetSection("{SECTION}"))`Vrátí `KestrelConfigurationLoader` `.Endpoint(string name, listenOptions => { })` metodu s metodou, kterou lze použít k doplnění nastavení nakonfigurovaného koncového bodu:</span><span class="sxs-lookup"><span data-stu-id="6742a-313">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="6742a-314">`KestrelServerOptions.ConfigurationLoader`k pokračování v iteraci u stávajícího zavaděče se dá získat přímý odkaz, jako je třeba ten, který poskytuje <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="6742a-314">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="6742a-315">Konfigurační oddíl pro každý koncový bod je k dispozici na možnostech v `Endpoint` metodě, aby bylo možné číst vlastní nastavení.</span><span class="sxs-lookup"><span data-stu-id="6742a-315">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="6742a-316">Více konfigurací může být načteno voláním `options.Configure(context.Configuration.GetSection("{SECTION}"))` znovu s jiným oddílem.</span><span class="sxs-lookup"><span data-stu-id="6742a-316">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="6742a-317">Je použita pouze poslední konfigurace, pokud `Load` není explicitně volána při předchozích instancích.</span><span class="sxs-lookup"><span data-stu-id="6742a-317">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="6742a-318">Metapackage nevolá `Load` , aby se jeho výchozí konfigurační oddíl mohl nahradit.</span><span class="sxs-lookup"><span data-stu-id="6742a-318">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="6742a-319">`KestrelConfigurationLoader`zrcadlí `Listen` rodinu rozhraní API z `KestrelServerOptions` as `Endpoint` přetížení, takže koncové body kódu a konfigurace lze konfigurovat na stejném místě.</span><span class="sxs-lookup"><span data-stu-id="6742a-319">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="6742a-320">Tato přetížení nepoužívají názvy a využívají pouze výchozí nastavení z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="6742a-320">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="6742a-321">*Změna výchozích hodnot v kódu*</span><span class="sxs-lookup"><span data-stu-id="6742a-321">*Change the defaults in code*</span></span>

<span data-ttu-id="6742a-322">`ConfigureEndpointDefaults`a `ConfigureHttpsDefaults` dá se použít ke změně výchozího nastavení pro `ListenOptions` a `HttpsConnectionAdapterOptions` , včetně přepsání výchozího certifikátu zadaného v předchozím scénáři.</span><span class="sxs-lookup"><span data-stu-id="6742a-322">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="6742a-323">`ConfigureEndpointDefaults`a `ConfigureHttpsDefaults` měla by být volána před konfigurací koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="6742a-323">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="6742a-324">*Podpora Kestrel pro SNI*</span><span class="sxs-lookup"><span data-stu-id="6742a-324">*Kestrel support for SNI*</span></span>

<span data-ttu-id="6742a-325">[Indikace názvu serveru (SNI)](https://tools.ietf.org/html/rfc6066#section-3) lze použít k hostování více domén na stejné IP adrese a portu.</span><span class="sxs-lookup"><span data-stu-id="6742a-325">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="6742a-326">Aby SNI fungovalo, klient pošle název hostitele pro zabezpečenou relaci serveru během metody handshake TLS, aby server mohl poskytovat správný certifikát.</span><span class="sxs-lookup"><span data-stu-id="6742a-326">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="6742a-327">Klient používá poskytnutý certifikát pro šifrovanou komunikaci se serverem během zabezpečené relace, která následuje za metodou handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="6742a-327">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="6742a-328">Kestrel podporuje SNI prostřednictvím `ServerCertificateSelector` zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="6742a-328">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="6742a-329">Zpětné volání je vyvoláno jednou za připojení, které aplikaci umožní zkontrolovat název hostitele a vybrat příslušný certifikát.</span><span class="sxs-lookup"><span data-stu-id="6742a-329">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="6742a-330">Podpora SNI vyžaduje:</span><span class="sxs-lookup"><span data-stu-id="6742a-330">SNI support requires:</span></span>

* <span data-ttu-id="6742a-331">Běží v cílovém rozhraní `netcoreapp2.1` nebo novějším.</span><span class="sxs-lookup"><span data-stu-id="6742a-331">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="6742a-332">V systému `net461` nebo novějším je zpětné volání vyvoláno, ale `name` je vždy `null` .</span><span class="sxs-lookup"><span data-stu-id="6742a-332">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="6742a-333">`name`Je také v `null` případě, že klient v TLS handshake neposkytne parametr názvu hostitele.</span><span class="sxs-lookup"><span data-stu-id="6742a-333">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="6742a-334">Všechny weby běží na stejné instanci Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6742a-334">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="6742a-335">Kestrel nepodporuje sdílení IP adresy a portu napříč několika instancemi bez reverzního proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="6742a-335">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="6742a-336">Protokolování připojení</span><span class="sxs-lookup"><span data-stu-id="6742a-336">Connection logging</span></span>

<span data-ttu-id="6742a-337">Volá <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> se, aby se vygenerovaly protokoly na úrovni ladění pro komunikaci na úrovni bajtového připojení.</span><span class="sxs-lookup"><span data-stu-id="6742a-337">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="6742a-338">Protokolování připojení je užitečné při řešení problémů v komunikaci nízké úrovně, například během šifrování TLS a za proxy servery.</span><span class="sxs-lookup"><span data-stu-id="6742a-338">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="6742a-339">Pokud `UseConnectionLogging` je umístěn před `UseHttps` , zaprotokoluje se zašifrovaný provoz.</span><span class="sxs-lookup"><span data-stu-id="6742a-339">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="6742a-340">Pokud `UseConnectionLogging` je umístěn po `UseHttps` , zaprotokoluje se dešifrovaný provoz.</span><span class="sxs-lookup"><span data-stu-id="6742a-340">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="6742a-341">Vytvoření vazby na soket TCP</span><span class="sxs-lookup"><span data-stu-id="6742a-341">Bind to a TCP socket</span></span>

<span data-ttu-id="6742a-342"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>Metoda se váže k soketu TCP a pomocí lambda možností povolíte konfiguraci certifikátu X. 509:</span><span class="sxs-lookup"><span data-stu-id="6742a-342">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="6742a-343">Příklad nakonfiguruje HTTPS pro koncový bod pomocí <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> .</span><span class="sxs-lookup"><span data-stu-id="6742a-343">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="6742a-344">Ke konfiguraci dalších nastavení Kestrel pro konkrétní koncové body použijte stejné rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="6742a-344">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="6742a-345">Vytvoření vazby na soket UNIX</span><span class="sxs-lookup"><span data-stu-id="6742a-345">Bind to a Unix socket</span></span>

<span data-ttu-id="6742a-346">Naslouchat na soketu se systémem UNIX <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> , aby se zlepšil výkon pomocí Nginx, jak je znázorněno v tomto příkladu:</span><span class="sxs-lookup"><span data-stu-id="6742a-346">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="6742a-347">V konfiguračním souboru Nginx nastavte `server`  >  `location`  >  `proxy_pass` položku na `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="6742a-347">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="6742a-348">`{KESTREL SOCKET}`je název soketu poskytnutý <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (například `kestrel-test.sock` v předchozím příkladu).</span><span class="sxs-lookup"><span data-stu-id="6742a-348">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="6742a-349">Zajistěte, aby byl soket zapisovatelný pomocí Nginx (například `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="6742a-349">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

### <a name="port-0"></a><span data-ttu-id="6742a-350">Port 0</span><span class="sxs-lookup"><span data-stu-id="6742a-350">Port 0</span></span>

<span data-ttu-id="6742a-351">Po zadání čísla portu se `0` Kestrel dynamicky váže k dostupnému portu.</span><span class="sxs-lookup"><span data-stu-id="6742a-351">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="6742a-352">Následující příklad ukazuje, jak určit, který port Kestrel je skutečně vázaný za běhu:</span><span class="sxs-lookup"><span data-stu-id="6742a-352">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="6742a-353">Když se aplikace spustí, výstup okna konzoly indikuje dynamický port, na který se aplikace dá získat:</span><span class="sxs-lookup"><span data-stu-id="6742a-353">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="6742a-354">Omezení</span><span class="sxs-lookup"><span data-stu-id="6742a-354">Limitations</span></span>

<span data-ttu-id="6742a-355">Konfigurace koncových bodů pomocí následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="6742a-355">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="6742a-356">`--urls`argument příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="6742a-356">`--urls` command-line argument</span></span>
* <span data-ttu-id="6742a-357">`urls`konfigurační klíč hostitele</span><span class="sxs-lookup"><span data-stu-id="6742a-357">`urls` host configuration key</span></span>
* <span data-ttu-id="6742a-358">`ASPNETCORE_URLS`Proměnná prostředí</span><span class="sxs-lookup"><span data-stu-id="6742a-358">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="6742a-359">Tyto metody jsou užitečné, pokud chcete, aby kód pracoval na jiných serverech než Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6742a-359">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="6742a-360">Mějte ale na paměti následující omezení:</span><span class="sxs-lookup"><span data-stu-id="6742a-360">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="6742a-361">Protokol HTTPS nelze s těmito přístupy použít, pokud není k dispozici výchozí certifikát v konfiguraci koncového bodu HTTPS (například pomocí `KestrelServerOptions` Konfigurace nebo konfiguračního souboru, jak je uvedeno výše v tomto tématu).</span><span class="sxs-lookup"><span data-stu-id="6742a-361">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="6742a-362">Při `Listen` `UseUrls` současném použití obou přístupů a `Listen` koncových bodů se koncové body přepisují `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="6742a-362">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="6742a-363">Konfigurace koncového bodu služby IIS</span><span class="sxs-lookup"><span data-stu-id="6742a-363">IIS endpoint configuration</span></span>

<span data-ttu-id="6742a-364">Při použití služby IIS jsou vazby adresy URL pro přepsání vazby služby IIS nastaveny buď `Listen` nebo `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="6742a-364">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="6742a-365">Další informace najdete v tématu [ASP.NET Core modulu](xref:host-and-deploy/aspnet-core-module) .</span><span class="sxs-lookup"><span data-stu-id="6742a-365">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="6742a-366">ListenOptions. Protocols</span><span class="sxs-lookup"><span data-stu-id="6742a-366">ListenOptions.Protocols</span></span>

<span data-ttu-id="6742a-367">`Protocols`Vlastnost určuje protokoly HTTP ( `HttpProtocols` ) povolené pro koncový bod připojení nebo pro server.</span><span class="sxs-lookup"><span data-stu-id="6742a-367">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="6742a-368">Přiřaďte hodnotu `Protocols` vlastnosti z `HttpProtocols` výčtu.</span><span class="sxs-lookup"><span data-stu-id="6742a-368">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="6742a-369">`HttpProtocols`hodnota výčtu</span><span class="sxs-lookup"><span data-stu-id="6742a-369">`HttpProtocols` enum value</span></span> | <span data-ttu-id="6742a-370">Povolený protokol připojení</span><span class="sxs-lookup"><span data-stu-id="6742a-370">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="6742a-371">Pouze HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="6742a-371">HTTP/1.1 only.</span></span> <span data-ttu-id="6742a-372">Dá se použít s TLS nebo bez něj.</span><span class="sxs-lookup"><span data-stu-id="6742a-372">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="6742a-373">Pouze HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="6742a-373">HTTP/2 only.</span></span> <span data-ttu-id="6742a-374">Dá se použít bez TLS jenom v případě, že klient podporuje [předchozí režim znalostní báze](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="6742a-374">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="6742a-375">HTTP/1.1 a HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="6742a-375">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="6742a-376">HTTP/2 vyžaduje, aby klient v rámci metody handshake protokolu TLS [(ALPN)](https://tools.ietf.org/html/rfc7301#section-3) vybral http/2. v opačném případě se připojení nastaví jako výchozí HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="6742a-376">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="6742a-377">Výchozí `ListenOptions.Protocols` hodnota pro libovolný koncový bod je `HttpProtocols.Http1AndHttp2` .</span><span class="sxs-lookup"><span data-stu-id="6742a-377">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="6742a-378">Omezení TLS pro HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="6742a-378">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="6742a-379">TLS verze 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="6742a-379">TLS version 1.2 or later</span></span>
* <span data-ttu-id="6742a-380">Opětovné vyjednávání je zakázané.</span><span class="sxs-lookup"><span data-stu-id="6742a-380">Renegotiation disabled</span></span>
* <span data-ttu-id="6742a-381">Komprese vypnuta</span><span class="sxs-lookup"><span data-stu-id="6742a-381">Compression disabled</span></span>
* <span data-ttu-id="6742a-382">Minimální velikosti dočasného výměny klíčů:</span><span class="sxs-lookup"><span data-stu-id="6742a-382">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="6742a-383">Eliptická křivka Diffie-Hellman (ECDH) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; : 224 bity minima</span><span class="sxs-lookup"><span data-stu-id="6742a-383">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="6742a-384">Omezené pole Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; : minimální počet bitů 2048</span><span class="sxs-lookup"><span data-stu-id="6742a-384">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="6742a-385">Šifrovací sada není zakázaná.</span><span class="sxs-lookup"><span data-stu-id="6742a-385">Cipher suite not prohibited.</span></span> 

<span data-ttu-id="6742a-386">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack;`TLS-ECDHE`&rbrack; &lbrack; `FIPS186` &rbrack; ve výchozím nastavení je podporována eliptická křivka P-256.</span><span class="sxs-lookup"><span data-stu-id="6742a-386">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="6742a-387">Následující příklad povoluje připojení HTTP/1.1 a HTTP/2 na portu 8000.</span><span class="sxs-lookup"><span data-stu-id="6742a-387">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="6742a-388">Připojení TLS zabezpečuje pomocí zadaného certifikátu:</span><span class="sxs-lookup"><span data-stu-id="6742a-388">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="6742a-389">Pomocí middleware připojení můžete v případě potřeby filtrovat handshake na základě jednotlivých připojení pro konkrétní šifry.</span><span class="sxs-lookup"><span data-stu-id="6742a-389">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="6742a-390">Následující příklad vyvolá <xref:System.NotSupportedException> jakýkoli šifrovací algoritmus, který aplikace nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="6742a-390">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="6742a-391">Případně můžete definovat a porovnat [ITlsHandshakeFeature. CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) se seznamem přijatelných šifrovacích sad.</span><span class="sxs-lookup"><span data-stu-id="6742a-391">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="6742a-392">Pro šifrovací algoritmus [CipherAlgorithmType. null](xref:System.Security.Authentication.CipherAlgorithmType) se nepoužívá žádné šifrování.</span><span class="sxs-lookup"><span data-stu-id="6742a-392">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

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

<span data-ttu-id="6742a-393">Filtrování připojení lze také nakonfigurovat prostřednictvím <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span><span class="sxs-lookup"><span data-stu-id="6742a-393">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

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

<span data-ttu-id="6742a-394">V systému Linux se <xref:System.Net.Security.CipherSuitesPolicy> dá použít k filtrování ověřování TLS metodou handshake na základě jednotlivých připojení:</span><span class="sxs-lookup"><span data-stu-id="6742a-394">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

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

<span data-ttu-id="6742a-395">*Nastavit protokol z konfigurace*</span><span class="sxs-lookup"><span data-stu-id="6742a-395">*Set the protocol from configuration*</span></span>

<span data-ttu-id="6742a-396">`CreateDefaultBuilder`Při `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` načítání konfigurace Kestrel ve výchozím nastavení volá.</span><span class="sxs-lookup"><span data-stu-id="6742a-396">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="6742a-397">Následující *appsettings.jsv* příkladě vytvoří protokol HTTP/1.1 jako výchozí protokol připojení pro všechny koncové body:</span><span class="sxs-lookup"><span data-stu-id="6742a-397">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="6742a-398">Následující *appsettings.jsv* příkladu vytvoří protokol připojení HTTP/1.1 pro konkrétní koncový bod:</span><span class="sxs-lookup"><span data-stu-id="6742a-398">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="6742a-399">Protokoly zadané v hodnotách přepisu kódu nastavených podle konfigurace</span><span class="sxs-lookup"><span data-stu-id="6742a-399">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="6742a-400">Konfigurace přenosu</span><span class="sxs-lookup"><span data-stu-id="6742a-400">Transport configuration</span></span>

<span data-ttu-id="6742a-401">Pro projekty, které vyžadují použití Libuv ( <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> ):</span><span class="sxs-lookup"><span data-stu-id="6742a-401">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span></span>

* <span data-ttu-id="6742a-402">Přidejte do souboru projektu aplikace závislost pro balíček [Microsoft. AspNetCore. Server. Kestrel. Transport. Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) :</span><span class="sxs-lookup"><span data-stu-id="6742a-402">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* <span data-ttu-id="6742a-403">Zavolat <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> na `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="6742a-403">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> on the `IWebHostBuilder`:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="6742a-404">Předpony adresy URL</span><span class="sxs-lookup"><span data-stu-id="6742a-404">URL prefixes</span></span>

<span data-ttu-id="6742a-405">Při použití `UseUrls` , `--urls` argumentu příkazového řádku, `urls` konfiguračního klíče hostitele nebo `ASPNETCORE_URLS` proměnné prostředí můžou být předpony adresy URL v některém z následujících formátů.</span><span class="sxs-lookup"><span data-stu-id="6742a-405">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="6742a-406">Platné jsou pouze předpony adresy URL protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="6742a-406">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="6742a-407">Kestrel nepodporuje protokol HTTPS při konfiguraci vazeb URL pomocí `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="6742a-407">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="6742a-408">Adresa IPv4 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="6742a-408">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="6742a-409">`0.0.0.0`je zvláštní případ, který se váže ke všem adresám IPv4.</span><span class="sxs-lookup"><span data-stu-id="6742a-409">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="6742a-410">Adresa IPv6 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="6742a-410">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="6742a-411">`[::]`je ekvivalentem protokolu IPv4 pro protokol IPv6 `0.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="6742a-411">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="6742a-412">Název hostitele s číslem portu</span><span class="sxs-lookup"><span data-stu-id="6742a-412">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="6742a-413">Názvy hostitelů, `*` a `+` nejsou speciální.</span><span class="sxs-lookup"><span data-stu-id="6742a-413">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="6742a-414">Cokoli se nerozpoznalo jako platná IP adresa nebo `localhost` se váže ke všem IP adresám IPv4 a IPv6.</span><span class="sxs-lookup"><span data-stu-id="6742a-414">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="6742a-415">Pokud chcete navazovat různé názvy hostitelů na různé ASP.NET Core aplikace na stejném portu, použijte [HTTP.sys](xref:fundamentals/servers/httpsys) nebo reverzní proxy server, jako je IIS, Nginx nebo Apache.</span><span class="sxs-lookup"><span data-stu-id="6742a-415">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="6742a-416">Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="6742a-416">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="6742a-417">`localhost`Název hostitele s číslem portu nebo IP adresou zpětné smyčky s číslem portu</span><span class="sxs-lookup"><span data-stu-id="6742a-417">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="6742a-418">Když `localhost` je zadaný, Kestrel se pokusí vytvořit propojení s rozhraními zpětné smyčky IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="6742a-418">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="6742a-419">Pokud je požadovaný port používán jinou službou na rozhraní zpětné smyčky, spuštění Kestrel se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="6742a-419">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="6742a-420">Pokud je rozhraní zpětné smyčky v žádném jiném případě nedostupné (většinou kvůli tomu, že protokol IPv6 není podporovaný), Kestrel zaznamená upozornění.</span><span class="sxs-lookup"><span data-stu-id="6742a-420">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="6742a-421">Filtrování hostitele</span><span class="sxs-lookup"><span data-stu-id="6742a-421">Host filtering</span></span>

<span data-ttu-id="6742a-422">I když Kestrel podporuje konfiguraci na základě prefixů `http://example.com:5000` , jako je, Kestrel převážně ignoruje název hostitele.</span><span class="sxs-lookup"><span data-stu-id="6742a-422">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="6742a-423">Hostitel `localhost` je zvláštní případ, který se používá pro vazbu na adresy zpětné smyčky.</span><span class="sxs-lookup"><span data-stu-id="6742a-423">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="6742a-424">Každý hostitel, který je jiný než explicitní IP adresa, se váže na všechny veřejné IP adresy.</span><span class="sxs-lookup"><span data-stu-id="6742a-424">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="6742a-425">`Host`hlavičky nejsou ověřené.</span><span class="sxs-lookup"><span data-stu-id="6742a-425">`Host` headers aren't validated.</span></span>

<span data-ttu-id="6742a-426">Jako alternativní řešení použijte middleware pro filtrování hostitele.</span><span class="sxs-lookup"><span data-stu-id="6742a-426">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="6742a-427">Middleware pro filtrování hostitele poskytuje balíček [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , který je implicitně k dispozici pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="6742a-427">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="6742a-428">Middleware je přidána pomocí <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> , který volá <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> :</span><span class="sxs-lookup"><span data-stu-id="6742a-428">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="6742a-429">Middleware pro filtrování hostitele je ve výchozím nastavení zakázáno.</span><span class="sxs-lookup"><span data-stu-id="6742a-429">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="6742a-430">Chcete-li povolit middleware, definujte `AllowedHosts` klíč v *appsettings.jsna* / *appSettings. \<EnvironmentName> . JSON*.</span><span class="sxs-lookup"><span data-stu-id="6742a-430">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="6742a-431">Hodnota je seznam názvů hostitelů oddělených středníkem bez čísel portů:</span><span class="sxs-lookup"><span data-stu-id="6742a-431">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="6742a-432">*appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="6742a-432">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="6742a-433">[Middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer) má také <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> možnost.</span><span class="sxs-lookup"><span data-stu-id="6742a-433">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="6742a-434">Middleware předávaných hlaviček a middleware pro filtrování hostitele mají podobné funkce pro různé scénáře.</span><span class="sxs-lookup"><span data-stu-id="6742a-434">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="6742a-435">Nastavení `AllowedHosts` pomocí předávaných middlewarových hlaviček je vhodné v případě, že `Host` Hlavička není zachována při předávání požadavků pomocí reverzního proxy server nebo nástroje pro vyrovnávání zatížení.</span><span class="sxs-lookup"><span data-stu-id="6742a-435">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="6742a-436">Nastavení `AllowedHosts` pomocí middlewaru pro filtrování hostitele je vhodné, když se Kestrel používá jako veřejný hraniční Server nebo když se `Host` Hlavička přímo přepošle.</span><span class="sxs-lookup"><span data-stu-id="6742a-436">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="6742a-437">Další informace o middlewaru předávaných hlaviček najdete v tématu <xref:host-and-deploy/proxy-load-balancer> .</span><span class="sxs-lookup"><span data-stu-id="6742a-437">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="6742a-438">Kestrel je [webový server pro různé platformy pro ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="6742a-438">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="6742a-439">Kestrel je webový server, který je ve výchozím nastavení součástí ASP.NET Core šablon projektů.</span><span class="sxs-lookup"><span data-stu-id="6742a-439">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="6742a-440">Kestrel podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="6742a-440">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="6742a-441">HTTPS</span><span class="sxs-lookup"><span data-stu-id="6742a-441">HTTPS</span></span>
* <span data-ttu-id="6742a-442">Neprůhledný upgrade používaný k povolení [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="6742a-442">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="6742a-443">Technologie UNIX Sockets s vysokým výkonem za Nginx</span><span class="sxs-lookup"><span data-stu-id="6742a-443">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="6742a-444">HTTP/2 (kromě macOS &dagger; )</span><span class="sxs-lookup"><span data-stu-id="6742a-444">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="6742a-445">&dagger;HTTP/2 se v budoucí verzi podporuje v macOS.</span><span class="sxs-lookup"><span data-stu-id="6742a-445">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="6742a-446">Kestrel se podporuje na všech platformách a verzích, které podporuje .NET Core.</span><span class="sxs-lookup"><span data-stu-id="6742a-446">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="6742a-447">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6742a-447">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="6742a-448">Podpora HTTP/2</span><span class="sxs-lookup"><span data-stu-id="6742a-448">HTTP/2 support</span></span>

<span data-ttu-id="6742a-449">[Http/2](https://httpwg.org/specs/rfc7540.html) je k dispozici pro ASP.NET Core aplikace, pokud jsou splněné následující základní požadavky:</span><span class="sxs-lookup"><span data-stu-id="6742a-449">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="6742a-450">Operační systém&dagger;</span><span class="sxs-lookup"><span data-stu-id="6742a-450">Operating system&dagger;</span></span>
  * <span data-ttu-id="6742a-451">Windows Server 2016/Windows 10 nebo novější&Dagger;</span><span class="sxs-lookup"><span data-stu-id="6742a-451">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="6742a-452">Linux s OpenSSL 1.0.2 nebo novějším (například Ubuntu 16,04 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="6742a-452">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="6742a-453">Cílová architektura: .NET Core 2,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="6742a-453">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="6742a-454">Připojení [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="6742a-454">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="6742a-455">Připojení TLS 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="6742a-455">TLS 1.2 or later connection</span></span>

<span data-ttu-id="6742a-456">&dagger;HTTP/2 se v budoucí verzi podporuje v macOS.</span><span class="sxs-lookup"><span data-stu-id="6742a-456">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="6742a-457">&Dagger;Kestrel má omezená podpora HTTP/2 na Windows Serveru 2012 R2 a Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="6742a-457">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="6742a-458">Podpora je omezená, protože seznam podporovaných šifrovacích sad TLS, které jsou k dispozici v těchto operačních systémech, je omezený.</span><span class="sxs-lookup"><span data-stu-id="6742a-458">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="6742a-459">Pro zabezpečení připojení TLS může být vyžadován certifikát vygenerovaný pomocí algoritmu ECDSA (s připojením typu eliptická křivka).</span><span class="sxs-lookup"><span data-stu-id="6742a-459">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="6742a-460">Pokud je vytvořeno připojení HTTP/2, sestavy [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="6742a-460">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="6742a-461">HTTP/2 je ve výchozím nastavení zakázané.</span><span class="sxs-lookup"><span data-stu-id="6742a-461">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="6742a-462">Další informace o konfiguraci najdete v částech [Možnosti Kestrel](#kestrel-options) a [ListenOptions. Protocols](#listenoptionsprotocols) .</span><span class="sxs-lookup"><span data-stu-id="6742a-462">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="6742a-463">Kdy použít Kestrel s reverzním proxy serverem</span><span class="sxs-lookup"><span data-stu-id="6742a-463">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="6742a-464">Kestrel může používat sám sebe nebo s *reverzními proxy server*, jako je [Internetová informační služba (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="6742a-464">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="6742a-465">Reverzní proxy server přijímá požadavky HTTP ze sítě a přesměruje je na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6742a-465">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="6742a-466">Kestrel použitý jako hraniční webový server (internetový):</span><span class="sxs-lookup"><span data-stu-id="6742a-466">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel komunikuje přímo s internetem bez zpětného proxy server](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="6742a-468">Kestrel se používá v konfiguraci reverzního proxy serveru:</span><span class="sxs-lookup"><span data-stu-id="6742a-468">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel nepřímo komunikuje s internetem prostřednictvím reverzní proxy server, jako je IIS, Nginx nebo Apache.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="6742a-470">Buď konfigurace, s zpětným proxy server nebo bez něj, je podporovaná konfigurace hostování.</span><span class="sxs-lookup"><span data-stu-id="6742a-470">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="6742a-471">Kestrel používané jako hraniční Server bez reverzních proxy server nepodporuje sdílení stejné IP adresy a portu mezi více procesy.</span><span class="sxs-lookup"><span data-stu-id="6742a-471">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="6742a-472">Když je Kestrel nakonfigurovaný k naslouchání na portu, Kestrel zpracovává veškerý provoz tohoto portu bez ohledu na `Host` hlavičky požadavků.</span><span class="sxs-lookup"><span data-stu-id="6742a-472">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="6742a-473">Reverzní proxy server, který může sdílet porty, má schopnost přesměrovat požadavky na Kestrel na jedinečné IP adresy a porty.</span><span class="sxs-lookup"><span data-stu-id="6742a-473">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="6742a-474">I v případě, že se zpětný proxy server nepožaduje, může být použití reverzní proxy server vhodnou volbou.</span><span class="sxs-lookup"><span data-stu-id="6742a-474">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="6742a-475">Reverzní proxy:</span><span class="sxs-lookup"><span data-stu-id="6742a-475">A reverse proxy:</span></span>

* <span data-ttu-id="6742a-476">Může omezit vystavenou veřejnou plochu v aplikacích, které hostuje.</span><span class="sxs-lookup"><span data-stu-id="6742a-476">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="6742a-477">Poskytněte další vrstvu konfigurace a obrany.</span><span class="sxs-lookup"><span data-stu-id="6742a-477">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="6742a-478">Může se lépe integrovat se stávající infrastrukturou.</span><span class="sxs-lookup"><span data-stu-id="6742a-478">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="6742a-479">Zjednodušte vyrovnávání zatížení a konfiguraci zabezpečené komunikace (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="6742a-479">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="6742a-480">Pouze reverzní proxy server vyžaduje certifikát X. 509 a tento server může komunikovat se servery aplikace v interní síti pomocí prostého protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="6742a-480">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="6742a-481">Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="6742a-481">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="6742a-482">Použití Kestrel v aplikacích ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6742a-482">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="6742a-483">Balíček [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) je součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="6742a-483">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="6742a-484">ASP.NET Core šablony projektu ve výchozím nastavení používají Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6742a-484">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="6742a-485">V *program.cs*kód šablony volá <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> , který volá na pozadí <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> .</span><span class="sxs-lookup"><span data-stu-id="6742a-485">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="6742a-486">Další informace o `CreateDefaultBuilder` a sestavení hostitele naleznete v části *Nastavení hostitele* v tématu <xref:fundamentals/host/web-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="6742a-486">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="6742a-487">Chcete-li po volání poskytnout další konfiguraci `CreateDefaultBuilder` , použijte `ConfigureKestrel` :</span><span class="sxs-lookup"><span data-stu-id="6742a-487">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="6742a-488">Pokud aplikace nevolá `CreateDefaultBuilder` pro nastavení hostitele, zavolejte <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **před** voláním `ConfigureKestrel` :</span><span class="sxs-lookup"><span data-stu-id="6742a-488">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="6742a-489">Kestrel možnosti</span><span class="sxs-lookup"><span data-stu-id="6742a-489">Kestrel options</span></span>

<span data-ttu-id="6742a-490">Webový server Kestrel má možnosti konfigurace omezení, které jsou zvláště užitečné v internetových nasazeních.</span><span class="sxs-lookup"><span data-stu-id="6742a-490">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="6742a-491">Nastavte omezení pro <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> vlastnost <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> třídy.</span><span class="sxs-lookup"><span data-stu-id="6742a-491">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="6742a-492">`Limits`Vlastnost obsahuje instanci <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> třídy.</span><span class="sxs-lookup"><span data-stu-id="6742a-492">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="6742a-493">V následujících příkladech se používá <xref:Microsoft.AspNetCore.Server.Kestrel.Core> obor názvů:</span><span class="sxs-lookup"><span data-stu-id="6742a-493">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="6742a-494">Možnosti Kestrel, které jsou konfigurovány v kódu jazyka C# v následujících příkladech, lze také nastavit pomocí [poskytovatele konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="6742a-494">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="6742a-495">Poskytovatel konfigurace souboru může například načíst konfiguraci Kestrel z *appsettings.jsna* nebo *appSettings. { Soubor Environment}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="6742a-495">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="6742a-496">Použijte **jeden** z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="6742a-496">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="6742a-497">Nakonfigurovat Kestrel v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6742a-497">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="6742a-498">Vloží instanci `IConfiguration` do `Startup` třídy.</span><span class="sxs-lookup"><span data-stu-id="6742a-498">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="6742a-499">Následující příklad předpokládá, že vložená konfigurace je přiřazena `Configuration` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="6742a-499">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="6742a-500">V aplikaci `Startup.ConfigureServices` načtěte `Kestrel` část konfigurace do konfigurace Kestrel:</span><span class="sxs-lookup"><span data-stu-id="6742a-500">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="6742a-501">Nakonfigurovat Kestrel při sestavování hostitele:</span><span class="sxs-lookup"><span data-stu-id="6742a-501">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="6742a-502">V *program.cs*načtěte `Kestrel` část konfigurace do konfigurace Kestrel:</span><span class="sxs-lookup"><span data-stu-id="6742a-502">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="6742a-503">Obě předchozí přístupy fungují u libovolného [poskytovatele konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="6742a-503">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="6742a-504">Časový limit Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="6742a-504">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="6742a-505">Získá nebo nastaví [časový limit zachování](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="6742a-505">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="6742a-506">Výchozí hodnota je 2 minuty.</span><span class="sxs-lookup"><span data-stu-id="6742a-506">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="6742a-507">Maximální počet připojení klientů</span><span class="sxs-lookup"><span data-stu-id="6742a-507">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="6742a-508">Maximální počet souběžných otevřených připojení TCP lze pro celou aplikaci nastavit pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="6742a-508">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="6742a-509">Existuje zvláštní omezení pro připojení, která byla upgradována z protokolu HTTP nebo HTTPS na jiný protokol (například na žádost WebSockets).</span><span class="sxs-lookup"><span data-stu-id="6742a-509">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="6742a-510">Po upgradu připojení se nepočítá na základě `MaxConcurrentConnections` limitu.</span><span class="sxs-lookup"><span data-stu-id="6742a-510">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="6742a-511">Ve výchozím nastavení je maximální počet připojení neomezené (null).</span><span class="sxs-lookup"><span data-stu-id="6742a-511">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="6742a-512">Maximální velikost textu požadavku</span><span class="sxs-lookup"><span data-stu-id="6742a-512">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="6742a-513">Výchozí maximální velikost textu požadavku je 30 000 000 bajtů, což je přibližně 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="6742a-513">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="6742a-514">Doporučený postup pro přepsání limitu v aplikaci ASP.NET Core MVC je použití <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> atributu pro metodu akce:</span><span class="sxs-lookup"><span data-stu-id="6742a-514">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="6742a-515">Tady je příklad, který ukazuje, jak nakonfigurovat omezení pro aplikaci na každém požadavku:</span><span class="sxs-lookup"><span data-stu-id="6742a-515">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="6742a-516">Přepsat nastavení u konkrétního požadavku v middleware:</span><span class="sxs-lookup"><span data-stu-id="6742a-516">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="6742a-517">Výjimka je vyvolána, pokud aplikace po zahájení aplikace nakonfiguruje limit na žádost.</span><span class="sxs-lookup"><span data-stu-id="6742a-517">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="6742a-518">Existuje `IsReadOnly` vlastnost, která určuje, zda `MaxRequestBodySize` je vlastnost ve stavu jen pro čtení, což znamená, že je příliš pozdě pro konfiguraci limitu.</span><span class="sxs-lookup"><span data-stu-id="6742a-518">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="6742a-519">Když je aplikace spouštěna [mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za [ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module), je omezení velikosti textu požadavku Kestrel zakázané, protože služba IIS tento limit již nastavuje.</span><span class="sxs-lookup"><span data-stu-id="6742a-519">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="6742a-520">Minimální rychlost těla zprávy požadavku</span><span class="sxs-lookup"><span data-stu-id="6742a-520">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="6742a-521">Kestrel kontroluje každou sekundu, pokud data přicházejí za zadanou rychlost v bajtech za sekundu.</span><span class="sxs-lookup"><span data-stu-id="6742a-521">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="6742a-522">Pokud frekvence klesne pod minimální hodnotu, vypršel časový limit připojení. Doba odkladu je doba, kterou Kestrel umožňuje klientovi zvýšit rychlost odesílání až na minimum; frekvence není během této doby kontrolována.</span><span class="sxs-lookup"><span data-stu-id="6742a-522">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="6742a-523">Doba odkladu pomáhá vyhnout se vyřazení připojení, která při počátečním posílání dat zpomalují rychlost, kvůli pomalému spuštění protokolu TCP.</span><span class="sxs-lookup"><span data-stu-id="6742a-523">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="6742a-524">Výchozí minimální sazba je 240 bajtů za sekundu s obdobím 5 sekund odkladu.</span><span class="sxs-lookup"><span data-stu-id="6742a-524">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="6742a-525">Pro odpověď platí také minimální sazba.</span><span class="sxs-lookup"><span data-stu-id="6742a-525">A minimum rate also applies to the response.</span></span> <span data-ttu-id="6742a-526">Kód pro nastavení limitu požadavku a limit odezvy je stejný, s výjimkou, `RequestBody` že existují nebo `Response` v názvech vlastností a rozhraní.</span><span class="sxs-lookup"><span data-stu-id="6742a-526">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="6742a-527">Tady je příklad, který ukazuje, jak nakonfigurovat minimální datové tarify v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="6742a-527">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="6742a-528">Přepsat minimální limity přenosové rychlosti na žádost v middlewaru:</span><span class="sxs-lookup"><span data-stu-id="6742a-528">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="6742a-529">Pro požadavky HTTP/2 se v předchozí ukázce neodkazuje žádná míra funkce, `HttpContext.Features` protože Změna omezení přenosové rychlosti na základě jednotlivých požadavků není podporovaná pro HTTP/2 kvůli podpoře protokolu pro multiplexování požadavků.</span><span class="sxs-lookup"><span data-stu-id="6742a-529">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="6742a-530">Omezení přenosové rychlosti pro všechny servery jsou nakonfigurovaná přes `KestrelServerOptions.Limits` protokol HTTP/1. x i pro připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="6742a-530">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="6742a-531">Časový limit hlaviček žádostí</span><span class="sxs-lookup"><span data-stu-id="6742a-531">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="6742a-532">Získá nebo nastaví maximální dobu, po kterou server stráví příjem hlaviček požadavků.</span><span class="sxs-lookup"><span data-stu-id="6742a-532">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="6742a-533">Výchozí hodnota je 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="6742a-533">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="6742a-534">Maximální počet proudů na připojení</span><span class="sxs-lookup"><span data-stu-id="6742a-534">Maximum streams per connection</span></span>

<span data-ttu-id="6742a-535">`Http2.MaxStreamsPerConnection`omezuje počet souběžných datových proudů požadavků na připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="6742a-535">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="6742a-536">Nadbytečné proudy jsou odmítnuty.</span><span class="sxs-lookup"><span data-stu-id="6742a-536">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="6742a-537">Výchozí hodnota je 100.</span><span class="sxs-lookup"><span data-stu-id="6742a-537">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="6742a-538">Velikost tabulky hlaviček</span><span class="sxs-lookup"><span data-stu-id="6742a-538">Header table size</span></span>

<span data-ttu-id="6742a-539">Dekodér HPACK dekomprimuje hlavičky HTTP pro připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="6742a-539">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="6742a-540">`Http2.HeaderTableSize`omezuje velikost kompresní tabulky hlaviček, kterou používá dekodér HPACK.</span><span class="sxs-lookup"><span data-stu-id="6742a-540">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="6742a-541">Hodnota je uvedena v oktetech a musí být větší než nula (0).</span><span class="sxs-lookup"><span data-stu-id="6742a-541">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="6742a-542">Výchozí hodnota je 4096.</span><span class="sxs-lookup"><span data-stu-id="6742a-542">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="6742a-543">Maximální velikost rámce</span><span class="sxs-lookup"><span data-stu-id="6742a-543">Maximum frame size</span></span>

<span data-ttu-id="6742a-544">`Http2.MaxFrameSize`Určuje maximální velikost datové části rámce připojení HTTP/2, která se má přijmout.</span><span class="sxs-lookup"><span data-stu-id="6742a-544">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="6742a-545">Hodnota je uvedena v oktetech a musí být v rozmezí 2 ^ 14 (16 384) až 2 ^ 24-1 (16 777 215).</span><span class="sxs-lookup"><span data-stu-id="6742a-545">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="6742a-546">Výchozí hodnota je 2 ^ 14 (16 384).</span><span class="sxs-lookup"><span data-stu-id="6742a-546">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="6742a-547">Maximální velikost hlavičky požadavku</span><span class="sxs-lookup"><span data-stu-id="6742a-547">Maximum request header size</span></span>

<span data-ttu-id="6742a-548">`Http2.MaxRequestHeaderFieldSize`Určuje maximální povolenou velikost v oktetech hodnot hlaviček požadavku.</span><span class="sxs-lookup"><span data-stu-id="6742a-548">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="6742a-549">Toto omezení platí pro názvy i hodnoty společně v komprimovaných a nekomprimovaných reprezentujcích.</span><span class="sxs-lookup"><span data-stu-id="6742a-549">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="6742a-550">Hodnota musí být větší než nula (0).</span><span class="sxs-lookup"><span data-stu-id="6742a-550">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="6742a-551">Výchozí hodnota je 8 192.</span><span class="sxs-lookup"><span data-stu-id="6742a-551">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="6742a-552">Velikost okna počátečního připojení</span><span class="sxs-lookup"><span data-stu-id="6742a-552">Initial connection window size</span></span>

<span data-ttu-id="6742a-553">`Http2.InitialConnectionWindowSize`Určuje maximální velikost textu požadavku v bajtech, které jsou v jednom okamžiku agregovány v rámci všech požadavků (datových proudů) na připojení.</span><span class="sxs-lookup"><span data-stu-id="6742a-553">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="6742a-554">Žádosti jsou také omezené pomocí `Http2.InitialStreamWindowSize` .</span><span class="sxs-lookup"><span data-stu-id="6742a-554">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="6742a-555">Hodnota musí být větší než nebo rovna 65 535 a menší než 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="6742a-555">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="6742a-556">Výchozí hodnota je 128 KB (131 072).</span><span class="sxs-lookup"><span data-stu-id="6742a-556">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="6742a-557">Počáteční velikost okna datového proudu</span><span class="sxs-lookup"><span data-stu-id="6742a-557">Initial stream window size</span></span>

<span data-ttu-id="6742a-558">`Http2.InitialStreamWindowSize`Určuje maximální velikost textu požadavku v bajtech na jednu žádost (Stream) na jednom místě.</span><span class="sxs-lookup"><span data-stu-id="6742a-558">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="6742a-559">Žádosti jsou také omezené pomocí `Http2.InitialStreamWindowSize` .</span><span class="sxs-lookup"><span data-stu-id="6742a-559">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="6742a-560">Hodnota musí být větší než nebo rovna 65 535 a menší než 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="6742a-560">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="6742a-561">Výchozí hodnota je 96 KB (98 304).</span><span class="sxs-lookup"><span data-stu-id="6742a-561">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="6742a-562">Synchronní vstupně-výstupní operace</span><span class="sxs-lookup"><span data-stu-id="6742a-562">Synchronous I/O</span></span>

<span data-ttu-id="6742a-563"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>Určuje, jestli je pro požadavek a odpověď povolený synchronní vstup/výstup.</span><span class="sxs-lookup"><span data-stu-id="6742a-563"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="6742a-564">Výchozí hodnota je `true` .</span><span class="sxs-lookup"><span data-stu-id="6742a-564">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="6742a-565">Velký počet blokujících synchronních vstupně-výstupních operací může vést ke vyčerpání fondu vláken, což aplikaci nereaguje.</span><span class="sxs-lookup"><span data-stu-id="6742a-565">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="6742a-566">Povolit pouze `AllowSynchronousIO` při použití knihovny, která nepodporuje asynchronní vstupně-výstupní operace.</span><span class="sxs-lookup"><span data-stu-id="6742a-566">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="6742a-567">Následující příklad povoluje synchronní vstupně-výstupní operace:</span><span class="sxs-lookup"><span data-stu-id="6742a-567">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="6742a-568">Informace o dalších možnostech a omezeních Kestrel najdete v tématech:</span><span class="sxs-lookup"><span data-stu-id="6742a-568">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="6742a-569">Konfigurace koncového bodu</span><span class="sxs-lookup"><span data-stu-id="6742a-569">Endpoint configuration</span></span>

<span data-ttu-id="6742a-570">Ve výchozím nastavení ASP.NET Core váže k:</span><span class="sxs-lookup"><span data-stu-id="6742a-570">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="6742a-571">`https://localhost:5001`(Pokud je k dispozici místní vývojový certifikát)</span><span class="sxs-lookup"><span data-stu-id="6742a-571">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="6742a-572">Zadejte adresy URL pomocí:</span><span class="sxs-lookup"><span data-stu-id="6742a-572">Specify URLs using the:</span></span>

* <span data-ttu-id="6742a-573">`ASPNETCORE_URLS`Proměnná prostředí.</span><span class="sxs-lookup"><span data-stu-id="6742a-573">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="6742a-574">`--urls`argument příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="6742a-574">`--urls` command-line argument.</span></span>
* <span data-ttu-id="6742a-575">`urls`konfigurační klíč hostitele.</span><span class="sxs-lookup"><span data-stu-id="6742a-575">`urls` host configuration key.</span></span>
* <span data-ttu-id="6742a-576">`UseUrls`Metoda rozšíření</span><span class="sxs-lookup"><span data-stu-id="6742a-576">`UseUrls` extension method.</span></span>

<span data-ttu-id="6742a-577">Hodnota zadaná pomocí těchto přístupů může být jeden nebo více koncových bodů HTTP a HTTPS (HTTPS, pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="6742a-577">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="6742a-578">Nakonfigurujte hodnotu jako seznam oddělený středníkem (například `"Urls": "http://localhost:8000;http://localhost:8001"` ).</span><span class="sxs-lookup"><span data-stu-id="6742a-578">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="6742a-579">Další informace o těchto přístupůch najdete v tématu [adresy URL serveru](xref:fundamentals/host/web-host#server-urls) a [potlačení konfigurace](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="6742a-579">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="6742a-580">Vytvoří se vývojový certifikát:</span><span class="sxs-lookup"><span data-stu-id="6742a-580">A development certificate is created:</span></span>

* <span data-ttu-id="6742a-581">Po instalaci [.NET Core SDK](/dotnet/core/sdk) .</span><span class="sxs-lookup"><span data-stu-id="6742a-581">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="6742a-582">[Nástroj pro vývoj a certifikáty](xref:aspnetcore-2.1#https) slouží k vytvoření certifikátu.</span><span class="sxs-lookup"><span data-stu-id="6742a-582">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="6742a-583">Některé prohlížeče vyžadují udělení explicitního oprávnění pro důvěřování místního vývojového certifikátu.</span><span class="sxs-lookup"><span data-stu-id="6742a-583">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="6742a-584">Šablony projektů konfigurují aplikace tak, aby běžely ve výchozím nastavení HTTPS a zahrnovaly [přesměrování https a podporu HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="6742a-584">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="6742a-585">Volání <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> nebo <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> metody pro <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> konfiguraci předpon adres URL a portů pro Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6742a-585">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="6742a-586">`UseUrls`, `--urls` argument příkazového řádku, `urls` konfigurační klíč hostitele a `ASPNETCORE_URLS` Proměnná prostředí fungují také, ale mají omezení zaznamenaná později v této části (pro konfiguraci koncového bodu https musí být k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="6742a-586">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="6742a-587">`KestrelServerOptions`rozšířeného</span><span class="sxs-lookup"><span data-stu-id="6742a-587">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="6742a-588">ConfigureEndpointDefaults (akce \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="6742a-588">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="6742a-589">Určuje konfiguraci `Action` , která se spustí pro každý zadaný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="6742a-589">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="6742a-590">Volání několikrát `ConfigureEndpointDefaults` nahrazuje předchozí `Action` `Action` zadanou parametr s.</span><span class="sxs-lookup"><span data-stu-id="6742a-590">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="6742a-591">Koncové body vytvořené voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **před** voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> nebudou použity výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="6742a-591">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="6742a-592">ConfigureHttpsDefaults (akce \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="6742a-592">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="6742a-593">Určuje konfiguraci `Action` , která se má spustit pro každý koncový bod HTTPS.</span><span class="sxs-lookup"><span data-stu-id="6742a-593">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="6742a-594">Volání několikrát `ConfigureHttpsDefaults` nahrazuje předchozí `Action` `Action` zadanou parametr s.</span><span class="sxs-lookup"><span data-stu-id="6742a-594">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="6742a-595">Koncové body vytvořené voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **před** voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> nebudou použity výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="6742a-595">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>


### <a name="configureiconfiguration"></a><span data-ttu-id="6742a-596">Konfigurace (IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="6742a-596">Configure(IConfiguration)</span></span>

<span data-ttu-id="6742a-597">Vytvoří zavaděč konfigurace pro nastavení Kestrel, který přebírá <xref:Microsoft.Extensions.Configuration.IConfiguration> jako vstup.</span><span class="sxs-lookup"><span data-stu-id="6742a-597">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="6742a-598">Konfigurace musí být vymezená na konfigurační oddíl pro Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6742a-598">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="6742a-599">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="6742a-599">ListenOptions.UseHttps</span></span>

<span data-ttu-id="6742a-600">Nakonfigurujte Kestrel tak, aby používal protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="6742a-600">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="6742a-601">`ListenOptions.UseHttps`SND</span><span class="sxs-lookup"><span data-stu-id="6742a-601">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="6742a-602">`UseHttps`: Nakonfigurujte Kestrel tak, aby používal HTTPS s výchozím certifikátem.</span><span class="sxs-lookup"><span data-stu-id="6742a-602">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="6742a-603">Vyvolá výjimku, pokud není nakonfigurován žádný výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="6742a-603">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="6742a-604">`ListenOptions.UseHttps`ukazatelů</span><span class="sxs-lookup"><span data-stu-id="6742a-604">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="6742a-605">`filename`je cesta a název souboru certifikátu vzhledem k adresáři, který obsahuje soubory obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="6742a-605">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="6742a-606">`password`je vyžadováno heslo pro přístup k datům certifikátu X. 509.</span><span class="sxs-lookup"><span data-stu-id="6742a-606">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="6742a-607">`configureOptions`je `Action` ke konfiguraci `HttpsConnectionAdapterOptions` .</span><span class="sxs-lookup"><span data-stu-id="6742a-607">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="6742a-608">Vrátí `ListenOptions` .</span><span class="sxs-lookup"><span data-stu-id="6742a-608">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="6742a-609">`storeName`je úložiště certifikátů, ze kterého se má certifikát načíst.</span><span class="sxs-lookup"><span data-stu-id="6742a-609">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="6742a-610">`subject`je název subjektu certifikátu.</span><span class="sxs-lookup"><span data-stu-id="6742a-610">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="6742a-611">`allowInvalid`Určuje, zda by měly být zváženy neplatné certifikáty, například certifikáty podepsané svým držitelem.</span><span class="sxs-lookup"><span data-stu-id="6742a-611">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="6742a-612">`location`je umístěním úložiště, ze kterého se má certifikát načíst.</span><span class="sxs-lookup"><span data-stu-id="6742a-612">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="6742a-613">`serverCertificate`je certifikát X. 509.</span><span class="sxs-lookup"><span data-stu-id="6742a-613">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="6742a-614">V produkčním prostředí musí být HTTPS explicitně nakonfigurovaný.</span><span class="sxs-lookup"><span data-stu-id="6742a-614">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="6742a-615">Musí být zadán minimálně výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="6742a-615">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="6742a-616">Podporované konfigurace popsané dál:</span><span class="sxs-lookup"><span data-stu-id="6742a-616">Supported configurations described next:</span></span>

* <span data-ttu-id="6742a-617">Žádná konfigurace</span><span class="sxs-lookup"><span data-stu-id="6742a-617">No configuration</span></span>
* <span data-ttu-id="6742a-618">Nahradit výchozí certifikát z konfigurace</span><span class="sxs-lookup"><span data-stu-id="6742a-618">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="6742a-619">Změna výchozích hodnot v kódu</span><span class="sxs-lookup"><span data-stu-id="6742a-619">Change the defaults in code</span></span>

<span data-ttu-id="6742a-620">*Žádná konfigurace*</span><span class="sxs-lookup"><span data-stu-id="6742a-620">*No configuration*</span></span>

<span data-ttu-id="6742a-621">Kestrel naslouchá na `http://localhost:5000` a `https://localhost:5001` (Pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="6742a-621">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="6742a-622">*Nahradit výchozí certifikát z konfigurace*</span><span class="sxs-lookup"><span data-stu-id="6742a-622">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="6742a-623">`CreateDefaultBuilder`Při `Configure(context.Configuration.GetSection("Kestrel"))` načítání konfigurace Kestrel ve výchozím nastavení volá.</span><span class="sxs-lookup"><span data-stu-id="6742a-623">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="6742a-624">Pro Kestrel je k dispozici výchozí schéma konfigurace nastavení aplikace HTTPS.</span><span class="sxs-lookup"><span data-stu-id="6742a-624">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="6742a-625">Nakonfigurujte několik koncových bodů, včetně adres URL a certifikátů, které se mají použít, buď ze souboru na disku, nebo z úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="6742a-625">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="6742a-626">V následujícím *appsettings.js* například:</span><span class="sxs-lookup"><span data-stu-id="6742a-626">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="6742a-627">Nastavte **AllowInvalid** na `true` , aby povolovala použití neplatných certifikátů (například certifikátů podepsaných svým držitelem).</span><span class="sxs-lookup"><span data-stu-id="6742a-627">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="6742a-628">Libovolný koncový bod HTTPS, který neurčuje certifikát (**HttpsDefaultCert** v následujícím příkladu), se vrátí k certifikátu definovanému v části **certifikáty** > **výchozí** nebo vývojový certifikát.</span><span class="sxs-lookup"><span data-stu-id="6742a-628">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="6742a-629">Alternativou k použití **cesty** a **hesla** pro libovolný uzel certifikátu je zadání certifikátu pomocí polí úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="6742a-629">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="6742a-630">Například **Certificates**  >  **výchozí** certifikát může být zadán jako:</span><span class="sxs-lookup"><span data-stu-id="6742a-630">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="6742a-631">Poznámky ke schématu:</span><span class="sxs-lookup"><span data-stu-id="6742a-631">Schema notes:</span></span>

* <span data-ttu-id="6742a-632">V názvech koncových bodů se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="6742a-632">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="6742a-633">Například `HTTPS` a `Https` jsou platné.</span><span class="sxs-lookup"><span data-stu-id="6742a-633">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="6742a-634">`Url`U každého koncového bodu je vyžadován parametr.</span><span class="sxs-lookup"><span data-stu-id="6742a-634">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="6742a-635">Formát pro tento parametr je stejný jako `Urls` konfigurační parametr nejvyšší úrovně s tím rozdílem, že je omezen na jedinou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="6742a-635">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="6742a-636">Tyto koncové body nahrazují hodnoty definované v konfiguraci nejvyšší úrovně `Urls` místo jejich přidání do těchto koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="6742a-636">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="6742a-637">Koncové body definované v kódu prostřednictvím `Listen` jsou kumulativní pomocí koncových bodů definovaných v konfiguračním oddílu.</span><span class="sxs-lookup"><span data-stu-id="6742a-637">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="6742a-638">`Certificate`Oddíl je nepovinný.</span><span class="sxs-lookup"><span data-stu-id="6742a-638">The `Certificate` section is optional.</span></span> <span data-ttu-id="6742a-639">Pokud `Certificate` není oddíl zadaný, použijí se výchozí hodnoty definované v dřívějších scénářích.</span><span class="sxs-lookup"><span data-stu-id="6742a-639">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="6742a-640">Pokud nejsou k dispozici žádné výchozí hodnoty, Server vyvolá výjimku a nemůže se spustit.</span><span class="sxs-lookup"><span data-stu-id="6742a-640">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="6742a-641">`Certificate`Oddíl podporuje jak heslo **cesty** &ndash; **Password** , tak **Subject** &ndash; certifikáty**úložiště** subjektu.</span><span class="sxs-lookup"><span data-stu-id="6742a-641">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="6742a-642">V takovém případě může být definován libovolný počet koncových bodů, pokud to nezpůsobí konflikty portů.</span><span class="sxs-lookup"><span data-stu-id="6742a-642">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="6742a-643">`options.Configure(context.Configuration.GetSection("{SECTION}"))`Vrátí `KestrelConfigurationLoader` `.Endpoint(string name, listenOptions => { })` metodu s metodou, kterou lze použít k doplnění nastavení nakonfigurovaného koncového bodu:</span><span class="sxs-lookup"><span data-stu-id="6742a-643">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="6742a-644">`KestrelServerOptions.ConfigurationLoader`k pokračování v iteraci u stávajícího zavaděče se dá získat přímý odkaz, jako je třeba ten, který poskytuje <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="6742a-644">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="6742a-645">Konfigurační oddíl pro každý koncový bod je k dispozici na možnostech v `Endpoint` metodě, aby bylo možné číst vlastní nastavení.</span><span class="sxs-lookup"><span data-stu-id="6742a-645">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="6742a-646">Více konfigurací může být načteno voláním `options.Configure(context.Configuration.GetSection("{SECTION}"))` znovu s jiným oddílem.</span><span class="sxs-lookup"><span data-stu-id="6742a-646">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="6742a-647">Je použita pouze poslední konfigurace, pokud `Load` není explicitně volána při předchozích instancích.</span><span class="sxs-lookup"><span data-stu-id="6742a-647">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="6742a-648">Metapackage nevolá `Load` , aby se jeho výchozí konfigurační oddíl mohl nahradit.</span><span class="sxs-lookup"><span data-stu-id="6742a-648">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="6742a-649">`KestrelConfigurationLoader`zrcadlí `Listen` rodinu rozhraní API z `KestrelServerOptions` as `Endpoint` přetížení, takže koncové body kódu a konfigurace lze konfigurovat na stejném místě.</span><span class="sxs-lookup"><span data-stu-id="6742a-649">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="6742a-650">Tato přetížení nepoužívají názvy a využívají pouze výchozí nastavení z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="6742a-650">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="6742a-651">*Změna výchozích hodnot v kódu*</span><span class="sxs-lookup"><span data-stu-id="6742a-651">*Change the defaults in code*</span></span>

<span data-ttu-id="6742a-652">`ConfigureEndpointDefaults`a `ConfigureHttpsDefaults` dá se použít ke změně výchozího nastavení pro `ListenOptions` a `HttpsConnectionAdapterOptions` , včetně přepsání výchozího certifikátu zadaného v předchozím scénáři.</span><span class="sxs-lookup"><span data-stu-id="6742a-652">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="6742a-653">`ConfigureEndpointDefaults`a `ConfigureHttpsDefaults` měla by být volána před konfigurací koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="6742a-653">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="6742a-654">*Podpora Kestrel pro SNI*</span><span class="sxs-lookup"><span data-stu-id="6742a-654">*Kestrel support for SNI*</span></span>

<span data-ttu-id="6742a-655">[Indikace názvu serveru (SNI)](https://tools.ietf.org/html/rfc6066#section-3) lze použít k hostování více domén na stejné IP adrese a portu.</span><span class="sxs-lookup"><span data-stu-id="6742a-655">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="6742a-656">Aby SNI fungovalo, klient pošle název hostitele pro zabezpečenou relaci serveru během metody handshake TLS, aby server mohl poskytovat správný certifikát.</span><span class="sxs-lookup"><span data-stu-id="6742a-656">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="6742a-657">Klient používá poskytnutý certifikát pro šifrovanou komunikaci se serverem během zabezpečené relace, která následuje za metodou handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="6742a-657">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="6742a-658">Kestrel podporuje SNI prostřednictvím `ServerCertificateSelector` zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="6742a-658">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="6742a-659">Zpětné volání je vyvoláno jednou za připojení, které aplikaci umožní zkontrolovat název hostitele a vybrat příslušný certifikát.</span><span class="sxs-lookup"><span data-stu-id="6742a-659">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="6742a-660">Podpora SNI vyžaduje:</span><span class="sxs-lookup"><span data-stu-id="6742a-660">SNI support requires:</span></span>

* <span data-ttu-id="6742a-661">Běží v cílovém rozhraní `netcoreapp2.1` nebo novějším.</span><span class="sxs-lookup"><span data-stu-id="6742a-661">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="6742a-662">V systému `net461` nebo novějším je zpětné volání vyvoláno, ale `name` je vždy `null` .</span><span class="sxs-lookup"><span data-stu-id="6742a-662">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="6742a-663">`name`Je také v `null` případě, že klient v TLS handshake neposkytne parametr názvu hostitele.</span><span class="sxs-lookup"><span data-stu-id="6742a-663">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="6742a-664">Všechny weby běží na stejné instanci Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6742a-664">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="6742a-665">Kestrel nepodporuje sdílení IP adresy a portu napříč několika instancemi bez reverzního proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="6742a-665">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="6742a-666">Protokolování připojení</span><span class="sxs-lookup"><span data-stu-id="6742a-666">Connection logging</span></span>

<span data-ttu-id="6742a-667">Volá <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> se, aby se vygenerovaly protokoly na úrovni ladění pro komunikaci na úrovni bajtového připojení.</span><span class="sxs-lookup"><span data-stu-id="6742a-667">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="6742a-668">Protokolování připojení je užitečné při řešení problémů v komunikaci nízké úrovně, například během šifrování TLS a za proxy servery.</span><span class="sxs-lookup"><span data-stu-id="6742a-668">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="6742a-669">Pokud `UseConnectionLogging` je umístěn před `UseHttps` , zaprotokoluje se zašifrovaný provoz.</span><span class="sxs-lookup"><span data-stu-id="6742a-669">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="6742a-670">Pokud `UseConnectionLogging` je umístěn po `UseHttps` , zaprotokoluje se dešifrovaný provoz.</span><span class="sxs-lookup"><span data-stu-id="6742a-670">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="6742a-671">Vytvoření vazby na soket TCP</span><span class="sxs-lookup"><span data-stu-id="6742a-671">Bind to a TCP socket</span></span>

<span data-ttu-id="6742a-672"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>Metoda se váže k soketu TCP a pomocí lambda možností povolíte konfiguraci certifikátu X. 509:</span><span class="sxs-lookup"><span data-stu-id="6742a-672">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="6742a-673">Příklad nakonfiguruje HTTPS pro koncový bod pomocí <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> .</span><span class="sxs-lookup"><span data-stu-id="6742a-673">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="6742a-674">Ke konfiguraci dalších nastavení Kestrel pro konkrétní koncové body použijte stejné rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="6742a-674">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="6742a-675">Vytvoření vazby na soket UNIX</span><span class="sxs-lookup"><span data-stu-id="6742a-675">Bind to a Unix socket</span></span>

<span data-ttu-id="6742a-676">Naslouchat na soketu se systémem UNIX <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> , aby se zlepšil výkon pomocí Nginx, jak je znázorněno v tomto příkladu:</span><span class="sxs-lookup"><span data-stu-id="6742a-676">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="6742a-677">V souboru Nginx confiuguration nastavte `server`  >  `location`  >  `proxy_pass` položku na `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="6742a-677">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="6742a-678">`{KESTREL SOCKET}`je název soketu poskytnutý <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (například `kestrel-test.sock` v předchozím příkladu).</span><span class="sxs-lookup"><span data-stu-id="6742a-678">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="6742a-679">Zajistěte, aby byl soket zapisovatelný pomocí Nginx (například `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="6742a-679">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="6742a-680">Port 0</span><span class="sxs-lookup"><span data-stu-id="6742a-680">Port 0</span></span>

<span data-ttu-id="6742a-681">Po zadání čísla portu se `0` Kestrel dynamicky váže k dostupnému portu.</span><span class="sxs-lookup"><span data-stu-id="6742a-681">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="6742a-682">Následující příklad ukazuje, jak určit, který port Kestrel je skutečně vázaný za běhu:</span><span class="sxs-lookup"><span data-stu-id="6742a-682">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="6742a-683">Když se aplikace spustí, výstup okna konzoly indikuje dynamický port, na který se aplikace dá získat:</span><span class="sxs-lookup"><span data-stu-id="6742a-683">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="6742a-684">Omezení</span><span class="sxs-lookup"><span data-stu-id="6742a-684">Limitations</span></span>

<span data-ttu-id="6742a-685">Konfigurace koncových bodů pomocí následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="6742a-685">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="6742a-686">`--urls`argument příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="6742a-686">`--urls` command-line argument</span></span>
* <span data-ttu-id="6742a-687">`urls`konfigurační klíč hostitele</span><span class="sxs-lookup"><span data-stu-id="6742a-687">`urls` host configuration key</span></span>
* <span data-ttu-id="6742a-688">`ASPNETCORE_URLS`Proměnná prostředí</span><span class="sxs-lookup"><span data-stu-id="6742a-688">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="6742a-689">Tyto metody jsou užitečné, pokud chcete, aby kód pracoval na jiných serverech než Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6742a-689">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="6742a-690">Mějte ale na paměti následující omezení:</span><span class="sxs-lookup"><span data-stu-id="6742a-690">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="6742a-691">Protokol HTTPS nelze s těmito přístupy použít, pokud není k dispozici výchozí certifikát v konfiguraci koncového bodu HTTPS (například pomocí `KestrelServerOptions` Konfigurace nebo konfiguračního souboru, jak je uvedeno výše v tomto tématu).</span><span class="sxs-lookup"><span data-stu-id="6742a-691">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="6742a-692">Při `Listen` `UseUrls` současném použití obou přístupů a `Listen` koncových bodů se koncové body přepisují `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="6742a-692">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="6742a-693">Konfigurace koncového bodu služby IIS</span><span class="sxs-lookup"><span data-stu-id="6742a-693">IIS endpoint configuration</span></span>

<span data-ttu-id="6742a-694">Při použití služby IIS jsou vazby adresy URL pro přepsání vazby služby IIS nastaveny buď `Listen` nebo `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="6742a-694">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="6742a-695">Další informace najdete v tématu [ASP.NET Core modulu](xref:host-and-deploy/aspnet-core-module) .</span><span class="sxs-lookup"><span data-stu-id="6742a-695">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="6742a-696">ListenOptions. Protocols</span><span class="sxs-lookup"><span data-stu-id="6742a-696">ListenOptions.Protocols</span></span>

<span data-ttu-id="6742a-697">`Protocols`Vlastnost určuje protokoly HTTP ( `HttpProtocols` ) povolené pro koncový bod připojení nebo pro server.</span><span class="sxs-lookup"><span data-stu-id="6742a-697">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="6742a-698">Přiřaďte hodnotu `Protocols` vlastnosti z `HttpProtocols` výčtu.</span><span class="sxs-lookup"><span data-stu-id="6742a-698">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="6742a-699">`HttpProtocols`hodnota výčtu</span><span class="sxs-lookup"><span data-stu-id="6742a-699">`HttpProtocols` enum value</span></span> | <span data-ttu-id="6742a-700">Povolený protokol připojení</span><span class="sxs-lookup"><span data-stu-id="6742a-700">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="6742a-701">Pouze HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="6742a-701">HTTP/1.1 only.</span></span> <span data-ttu-id="6742a-702">Dá se použít s TLS nebo bez něj.</span><span class="sxs-lookup"><span data-stu-id="6742a-702">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="6742a-703">Pouze HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="6742a-703">HTTP/2 only.</span></span> <span data-ttu-id="6742a-704">Dá se použít bez TLS jenom v případě, že klient podporuje [předchozí režim znalostní báze](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="6742a-704">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="6742a-705">HTTP/1.1 a HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="6742a-705">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="6742a-706">HTTP/2 vyžaduje připojení TLS a [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) ; v opačném případě se připojení nastaví jako výchozí HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="6742a-706">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="6742a-707">Výchozí protokol HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="6742a-707">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="6742a-708">Omezení TLS pro HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="6742a-708">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="6742a-709">TLS verze 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="6742a-709">TLS version 1.2 or later</span></span>
* <span data-ttu-id="6742a-710">Opětovné vyjednávání je zakázané.</span><span class="sxs-lookup"><span data-stu-id="6742a-710">Renegotiation disabled</span></span>
* <span data-ttu-id="6742a-711">Komprese vypnuta</span><span class="sxs-lookup"><span data-stu-id="6742a-711">Compression disabled</span></span>
* <span data-ttu-id="6742a-712">Minimální velikosti dočasného výměny klíčů:</span><span class="sxs-lookup"><span data-stu-id="6742a-712">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="6742a-713">Eliptická křivka Diffie-Hellman (ECDH) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; : 224 bity minima</span><span class="sxs-lookup"><span data-stu-id="6742a-713">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="6742a-714">Omezené pole Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; : minimální počet bitů 2048</span><span class="sxs-lookup"><span data-stu-id="6742a-714">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="6742a-715">Šifrovací sada není zablokovaná.</span><span class="sxs-lookup"><span data-stu-id="6742a-715">Cipher suite not blocked</span></span>

<span data-ttu-id="6742a-716">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack;`TLS-ECDHE`&rbrack; &lbrack; `FIPS186` &rbrack; ve výchozím nastavení je podporována eliptická křivka P-256.</span><span class="sxs-lookup"><span data-stu-id="6742a-716">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="6742a-717">Následující příklad povoluje připojení HTTP/1.1 a HTTP/2 na portu 8000.</span><span class="sxs-lookup"><span data-stu-id="6742a-717">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="6742a-718">Připojení TLS zabezpečuje pomocí zadaného certifikátu:</span><span class="sxs-lookup"><span data-stu-id="6742a-718">Connections are secured by TLS with a supplied certificate:</span></span>

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

<span data-ttu-id="6742a-719">Volitelně můžete vytvořit `IConnectionAdapter` implementaci pro filtrování ověřování TLS metodou handshake na základě jednotlivých připojení pro konkrétní šifry:</span><span class="sxs-lookup"><span data-stu-id="6742a-719">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

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

<span data-ttu-id="6742a-720">*Nastavit protokol z konfigurace*</span><span class="sxs-lookup"><span data-stu-id="6742a-720">*Set the protocol from configuration*</span></span>

<span data-ttu-id="6742a-721"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>Při `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` načítání konfigurace Kestrel ve výchozím nastavení volá.</span><span class="sxs-lookup"><span data-stu-id="6742a-721"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="6742a-722">V následujících *appsettings.js* jsou například pro všechny koncové body Kestrel zavedeny výchozí protokoly připojení (http/1.1 a HTTP/2):</span><span class="sxs-lookup"><span data-stu-id="6742a-722">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="6742a-723">Následující příklad konfiguračního souboru vytvoří protokol připojení pro konkrétní koncový bod:</span><span class="sxs-lookup"><span data-stu-id="6742a-723">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="6742a-724">Protokoly zadané v hodnotách přepisu kódu nastavených podle konfigurace</span><span class="sxs-lookup"><span data-stu-id="6742a-724">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="6742a-725">Konfigurace přenosu</span><span class="sxs-lookup"><span data-stu-id="6742a-725">Transport configuration</span></span>

<span data-ttu-id="6742a-726">S vydáním ASP.NET Core 2,1 není výchozí přenos Kestrel založen na Libuv, ale na základě spravovaných soketů.</span><span class="sxs-lookup"><span data-stu-id="6742a-726">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="6742a-727">Jedná se o zásadní změnu pro aplikace ASP.NET Core 2,0 upgradované na 2,1, které volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> a závisí na jednom z následujících balíčků:</span><span class="sxs-lookup"><span data-stu-id="6742a-727">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="6742a-728">[Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (přímý odkaz na balíček)</span><span class="sxs-lookup"><span data-stu-id="6742a-728">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="6742a-729">Microsoft. AspNetCore. app</span><span class="sxs-lookup"><span data-stu-id="6742a-729">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="6742a-730">Pro projekty, které vyžadují použití Libuv:</span><span class="sxs-lookup"><span data-stu-id="6742a-730">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="6742a-731">Přidejte do souboru projektu aplikace závislost pro balíček [Microsoft. AspNetCore. Server. Kestrel. Transport. Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) :</span><span class="sxs-lookup"><span data-stu-id="6742a-731">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="6742a-732">Volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> :</span><span class="sxs-lookup"><span data-stu-id="6742a-732">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="6742a-733">Předpony adresy URL</span><span class="sxs-lookup"><span data-stu-id="6742a-733">URL prefixes</span></span>

<span data-ttu-id="6742a-734">Při použití `UseUrls` , `--urls` argumentu příkazového řádku, `urls` konfiguračního klíče hostitele nebo `ASPNETCORE_URLS` proměnné prostředí můžou být předpony adresy URL v některém z následujících formátů.</span><span class="sxs-lookup"><span data-stu-id="6742a-734">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="6742a-735">Platné jsou pouze předpony adresy URL protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="6742a-735">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="6742a-736">Kestrel nepodporuje protokol HTTPS při konfiguraci vazeb URL pomocí `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="6742a-736">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="6742a-737">Adresa IPv4 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="6742a-737">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="6742a-738">`0.0.0.0`je zvláštní případ, který se váže ke všem adresám IPv4.</span><span class="sxs-lookup"><span data-stu-id="6742a-738">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="6742a-739">Adresa IPv6 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="6742a-739">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="6742a-740">`[::]`je ekvivalentem protokolu IPv4 pro protokol IPv6 `0.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="6742a-740">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="6742a-741">Název hostitele s číslem portu</span><span class="sxs-lookup"><span data-stu-id="6742a-741">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="6742a-742">Názvy hostitelů, `*` a `+` nejsou speciální.</span><span class="sxs-lookup"><span data-stu-id="6742a-742">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="6742a-743">Cokoli se nerozpoznalo jako platná IP adresa nebo `localhost` se váže ke všem IP adresám IPv4 a IPv6.</span><span class="sxs-lookup"><span data-stu-id="6742a-743">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="6742a-744">Pokud chcete navazovat různé názvy hostitelů na různé ASP.NET Core aplikace na stejném portu, použijte [HTTP.sys](xref:fundamentals/servers/httpsys) nebo reverzní proxy server, jako je IIS, Nginx nebo Apache.</span><span class="sxs-lookup"><span data-stu-id="6742a-744">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="6742a-745">Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="6742a-745">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="6742a-746">`localhost`Název hostitele s číslem portu nebo IP adresou zpětné smyčky s číslem portu</span><span class="sxs-lookup"><span data-stu-id="6742a-746">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="6742a-747">Když `localhost` je zadaný, Kestrel se pokusí vytvořit propojení s rozhraními zpětné smyčky IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="6742a-747">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="6742a-748">Pokud je požadovaný port používán jinou službou na rozhraní zpětné smyčky, spuštění Kestrel se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="6742a-748">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="6742a-749">Pokud je rozhraní zpětné smyčky v žádném jiném případě nedostupné (většinou kvůli tomu, že protokol IPv6 není podporovaný), Kestrel zaznamená upozornění.</span><span class="sxs-lookup"><span data-stu-id="6742a-749">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="6742a-750">Filtrování hostitele</span><span class="sxs-lookup"><span data-stu-id="6742a-750">Host filtering</span></span>

<span data-ttu-id="6742a-751">I když Kestrel podporuje konfiguraci na základě prefixů `http://example.com:5000` , jako je, Kestrel převážně ignoruje název hostitele.</span><span class="sxs-lookup"><span data-stu-id="6742a-751">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="6742a-752">Hostitel `localhost` je zvláštní případ, který se používá pro vazbu na adresy zpětné smyčky.</span><span class="sxs-lookup"><span data-stu-id="6742a-752">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="6742a-753">Každý hostitel, který je jiný než explicitní IP adresa, se váže na všechny veřejné IP adresy.</span><span class="sxs-lookup"><span data-stu-id="6742a-753">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="6742a-754">`Host`hlavičky nejsou ověřené.</span><span class="sxs-lookup"><span data-stu-id="6742a-754">`Host` headers aren't validated.</span></span>

<span data-ttu-id="6742a-755">Jako alternativní řešení použijte middleware pro filtrování hostitele.</span><span class="sxs-lookup"><span data-stu-id="6742a-755">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="6742a-756">Middleware pro filtrování hostitele poskytuje balíček [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , který je součástí [Microsoft. AspNetCore. App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 nebo 2,2).</span><span class="sxs-lookup"><span data-stu-id="6742a-756">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="6742a-757">Middleware je přidána pomocí <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> , který volá <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> :</span><span class="sxs-lookup"><span data-stu-id="6742a-757">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="6742a-758">Middleware pro filtrování hostitele je ve výchozím nastavení zakázáno.</span><span class="sxs-lookup"><span data-stu-id="6742a-758">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="6742a-759">Chcete-li povolit middleware, definujte `AllowedHosts` klíč v *appsettings.jsna* / *appSettings. \<EnvironmentName> . JSON*.</span><span class="sxs-lookup"><span data-stu-id="6742a-759">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="6742a-760">Hodnota je seznam názvů hostitelů oddělených středníkem bez čísel portů:</span><span class="sxs-lookup"><span data-stu-id="6742a-760">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="6742a-761">*appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="6742a-761">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="6742a-762">[Middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer) má také <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> možnost.</span><span class="sxs-lookup"><span data-stu-id="6742a-762">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="6742a-763">Middleware předávaných hlaviček a middleware pro filtrování hostitele mají podobné funkce pro různé scénáře.</span><span class="sxs-lookup"><span data-stu-id="6742a-763">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="6742a-764">Nastavení `AllowedHosts` pomocí předávaných middlewarových hlaviček je vhodné v případě, že `Host` Hlavička není zachována při předávání požadavků pomocí reverzního proxy server nebo nástroje pro vyrovnávání zatížení.</span><span class="sxs-lookup"><span data-stu-id="6742a-764">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="6742a-765">Nastavení `AllowedHosts` pomocí middlewaru pro filtrování hostitele je vhodné, když se Kestrel používá jako veřejný hraniční Server nebo když se `Host` Hlavička přímo přepošle.</span><span class="sxs-lookup"><span data-stu-id="6742a-765">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="6742a-766">Další informace o middlewaru předávaných hlaviček najdete v tématu <xref:host-and-deploy/proxy-load-balancer> .</span><span class="sxs-lookup"><span data-stu-id="6742a-766">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="6742a-767">Kestrel je [webový server pro různé platformy pro ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="6742a-767">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="6742a-768">Kestrel je webový server, který je ve výchozím nastavení součástí ASP.NET Core šablon projektů.</span><span class="sxs-lookup"><span data-stu-id="6742a-768">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="6742a-769">Kestrel podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="6742a-769">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="6742a-770">HTTPS</span><span class="sxs-lookup"><span data-stu-id="6742a-770">HTTPS</span></span>
* <span data-ttu-id="6742a-771">Neprůhledný upgrade používaný k povolení [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="6742a-771">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="6742a-772">Technologie UNIX Sockets s vysokým výkonem za Nginx</span><span class="sxs-lookup"><span data-stu-id="6742a-772">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="6742a-773">Kestrel se podporuje na všech platformách a verzích, které podporuje .NET Core.</span><span class="sxs-lookup"><span data-stu-id="6742a-773">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="6742a-774">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="6742a-774">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="6742a-775">Kdy použít Kestrel s reverzním proxy serverem</span><span class="sxs-lookup"><span data-stu-id="6742a-775">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="6742a-776">Kestrel může používat sám sebe nebo s *reverzními proxy server*, jako je [Internetová informační služba (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="6742a-776">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="6742a-777">Reverzní proxy server přijímá požadavky HTTP ze sítě a přesměruje je na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6742a-777">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="6742a-778">Kestrel použitý jako hraniční webový server (internetový):</span><span class="sxs-lookup"><span data-stu-id="6742a-778">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel komunikuje přímo s internetem bez zpětného proxy server](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="6742a-780">Kestrel se používá v konfiguraci reverzního proxy serveru:</span><span class="sxs-lookup"><span data-stu-id="6742a-780">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel nepřímo komunikuje s internetem prostřednictvím reverzní proxy server, jako je IIS, Nginx nebo Apache.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="6742a-782">Buď konfigurace, s zpětným proxy server nebo bez něj, je podporovaná konfigurace hostování.</span><span class="sxs-lookup"><span data-stu-id="6742a-782">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="6742a-783">Kestrel používané jako hraniční Server bez reverzních proxy server nepodporuje sdílení stejné IP adresy a portu mezi více procesy.</span><span class="sxs-lookup"><span data-stu-id="6742a-783">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="6742a-784">Když je Kestrel nakonfigurovaný k naslouchání na portu, Kestrel zpracovává veškerý provoz tohoto portu bez ohledu na `Host` hlavičky požadavků.</span><span class="sxs-lookup"><span data-stu-id="6742a-784">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="6742a-785">Reverzní proxy server, který může sdílet porty, má schopnost přesměrovat požadavky na Kestrel na jedinečné IP adresy a porty.</span><span class="sxs-lookup"><span data-stu-id="6742a-785">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="6742a-786">I v případě, že se zpětný proxy server nepožaduje, může být použití reverzní proxy server vhodnou volbou.</span><span class="sxs-lookup"><span data-stu-id="6742a-786">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="6742a-787">Reverzní proxy:</span><span class="sxs-lookup"><span data-stu-id="6742a-787">A reverse proxy:</span></span>

* <span data-ttu-id="6742a-788">Může omezit vystavenou veřejnou plochu v aplikacích, které hostuje.</span><span class="sxs-lookup"><span data-stu-id="6742a-788">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="6742a-789">Poskytněte další vrstvu konfigurace a obrany.</span><span class="sxs-lookup"><span data-stu-id="6742a-789">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="6742a-790">Může se lépe integrovat se stávající infrastrukturou.</span><span class="sxs-lookup"><span data-stu-id="6742a-790">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="6742a-791">Zjednodušte vyrovnávání zatížení a konfiguraci zabezpečené komunikace (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="6742a-791">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="6742a-792">Pouze reverzní proxy server vyžaduje certifikát X. 509 a tento server může komunikovat se servery aplikace v interní síti pomocí prostého protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="6742a-792">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="6742a-793">Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="6742a-793">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="6742a-794">Použití Kestrel v aplikacích ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="6742a-794">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="6742a-795">Balíček [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) je součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="6742a-795">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="6742a-796">ASP.NET Core šablony projektu ve výchozím nastavení používají Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6742a-796">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="6742a-797">V *program.cs*kód šablony volá <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> , který volá na pozadí <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> .</span><span class="sxs-lookup"><span data-stu-id="6742a-797">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="6742a-798">Chcete-li po volání poskytnout další konfiguraci `CreateDefaultBuilder` , zavolejte <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> :</span><span class="sxs-lookup"><span data-stu-id="6742a-798">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="6742a-799">Další informace o `CreateDefaultBuilder` a sestavení hostitele naleznete v části *Nastavení hostitele* v tématu <xref:fundamentals/host/web-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="6742a-799">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="6742a-800">Kestrel možnosti</span><span class="sxs-lookup"><span data-stu-id="6742a-800">Kestrel options</span></span>

<span data-ttu-id="6742a-801">Webový server Kestrel má možnosti konfigurace omezení, které jsou zvláště užitečné v internetových nasazeních.</span><span class="sxs-lookup"><span data-stu-id="6742a-801">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="6742a-802">Nastavte omezení pro <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> vlastnost <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> třídy.</span><span class="sxs-lookup"><span data-stu-id="6742a-802">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="6742a-803">`Limits`Vlastnost obsahuje instanci <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> třídy.</span><span class="sxs-lookup"><span data-stu-id="6742a-803">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="6742a-804">V následujících příkladech se používá <xref:Microsoft.AspNetCore.Server.Kestrel.Core> obor názvů:</span><span class="sxs-lookup"><span data-stu-id="6742a-804">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="6742a-805">Možnosti Kestrel, které jsou konfigurovány v kódu jazyka C# v následujících příkladech, lze také nastavit pomocí [poskytovatele konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="6742a-805">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="6742a-806">Poskytovatel konfigurace souboru může například načíst konfiguraci Kestrel z *appsettings.jsna* nebo *appSettings. { Soubor Environment}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="6742a-806">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="6742a-807">Použijte **jeden** z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="6742a-807">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="6742a-808">Nakonfigurovat Kestrel v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="6742a-808">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="6742a-809">Vloží instanci `IConfiguration` do `Startup` třídy.</span><span class="sxs-lookup"><span data-stu-id="6742a-809">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="6742a-810">Následující příklad předpokládá, že vložená konfigurace je přiřazena `Configuration` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="6742a-810">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="6742a-811">V aplikaci `Startup.ConfigureServices` načtěte `Kestrel` část konfigurace do konfigurace Kestrel:</span><span class="sxs-lookup"><span data-stu-id="6742a-811">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="6742a-812">Nakonfigurovat Kestrel při sestavování hostitele:</span><span class="sxs-lookup"><span data-stu-id="6742a-812">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="6742a-813">V *program.cs*načtěte `Kestrel` část konfigurace do konfigurace Kestrel:</span><span class="sxs-lookup"><span data-stu-id="6742a-813">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="6742a-814">Obě předchozí přístupy fungují u libovolného [poskytovatele konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="6742a-814">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="6742a-815">Časový limit Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="6742a-815">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="6742a-816">Získá nebo nastaví [časový limit zachování](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="6742a-816">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="6742a-817">Výchozí hodnota je 2 minuty.</span><span class="sxs-lookup"><span data-stu-id="6742a-817">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="6742a-818">Maximální počet připojení klientů</span><span class="sxs-lookup"><span data-stu-id="6742a-818">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="6742a-819">Maximální počet souběžných otevřených připojení TCP lze pro celou aplikaci nastavit pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="6742a-819">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="6742a-820">Existuje zvláštní omezení pro připojení, která byla upgradována z protokolu HTTP nebo HTTPS na jiný protokol (například na žádost WebSockets).</span><span class="sxs-lookup"><span data-stu-id="6742a-820">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="6742a-821">Po upgradu připojení se nepočítá na základě `MaxConcurrentConnections` limitu.</span><span class="sxs-lookup"><span data-stu-id="6742a-821">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="6742a-822">Ve výchozím nastavení je maximální počet připojení neomezené (null).</span><span class="sxs-lookup"><span data-stu-id="6742a-822">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="6742a-823">Maximální velikost textu požadavku</span><span class="sxs-lookup"><span data-stu-id="6742a-823">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="6742a-824">Výchozí maximální velikost textu požadavku je 30 000 000 bajtů, což je přibližně 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="6742a-824">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="6742a-825">Doporučený postup pro přepsání limitu v aplikaci ASP.NET Core MVC je použití <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> atributu pro metodu akce:</span><span class="sxs-lookup"><span data-stu-id="6742a-825">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="6742a-826">Tady je příklad, který ukazuje, jak nakonfigurovat omezení pro aplikaci na každém požadavku:</span><span class="sxs-lookup"><span data-stu-id="6742a-826">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="6742a-827">Přepsat nastavení u konkrétního požadavku v middleware:</span><span class="sxs-lookup"><span data-stu-id="6742a-827">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="6742a-828">Výjimka je vyvolána, pokud aplikace po zahájení aplikace nakonfiguruje limit na žádost.</span><span class="sxs-lookup"><span data-stu-id="6742a-828">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="6742a-829">Existuje `IsReadOnly` vlastnost, která určuje, zda `MaxRequestBodySize` je vlastnost ve stavu jen pro čtení, což znamená, že je příliš pozdě pro konfiguraci limitu.</span><span class="sxs-lookup"><span data-stu-id="6742a-829">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="6742a-830">Když je aplikace spouštěna [mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za [ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module), je omezení velikosti textu požadavku Kestrel zakázané, protože služba IIS tento limit již nastavuje.</span><span class="sxs-lookup"><span data-stu-id="6742a-830">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="6742a-831">Minimální rychlost těla zprávy požadavku</span><span class="sxs-lookup"><span data-stu-id="6742a-831">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="6742a-832">Kestrel kontroluje každou sekundu, pokud data přicházejí za zadanou rychlost v bajtech za sekundu.</span><span class="sxs-lookup"><span data-stu-id="6742a-832">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="6742a-833">Pokud frekvence klesne pod minimální hodnotu, vypršel časový limit připojení. Doba odkladu je doba, kterou Kestrel umožňuje klientovi zvýšit rychlost odesílání až na minimum; frekvence není během této doby kontrolována.</span><span class="sxs-lookup"><span data-stu-id="6742a-833">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="6742a-834">Doba odkladu pomáhá vyhnout se vyřazení připojení, která při počátečním posílání dat zpomalují rychlost, kvůli pomalému spuštění protokolu TCP.</span><span class="sxs-lookup"><span data-stu-id="6742a-834">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="6742a-835">Výchozí minimální sazba je 240 bajtů za sekundu s obdobím 5 sekund odkladu.</span><span class="sxs-lookup"><span data-stu-id="6742a-835">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="6742a-836">Pro odpověď platí také minimální sazba.</span><span class="sxs-lookup"><span data-stu-id="6742a-836">A minimum rate also applies to the response.</span></span> <span data-ttu-id="6742a-837">Kód pro nastavení limitu požadavku a limit odezvy je stejný, s výjimkou, `RequestBody` že existují nebo `Response` v názvech vlastností a rozhraní.</span><span class="sxs-lookup"><span data-stu-id="6742a-837">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="6742a-838">Tady je příklad, který ukazuje, jak nakonfigurovat minimální datové tarify v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="6742a-838">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

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

### <a name="request-headers-timeout"></a><span data-ttu-id="6742a-839">Časový limit hlaviček žádostí</span><span class="sxs-lookup"><span data-stu-id="6742a-839">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="6742a-840">Získá nebo nastaví maximální dobu, po kterou server stráví příjem hlaviček požadavků.</span><span class="sxs-lookup"><span data-stu-id="6742a-840">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="6742a-841">Výchozí hodnota je 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="6742a-841">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="6742a-842">Synchronní vstupně-výstupní operace</span><span class="sxs-lookup"><span data-stu-id="6742a-842">Synchronous I/O</span></span>

<span data-ttu-id="6742a-843"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>Určuje, jestli je pro požadavek a odpověď povolený synchronní vstup/výstup.</span><span class="sxs-lookup"><span data-stu-id="6742a-843"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="6742a-844">Výchozí hodnota je `true` .</span><span class="sxs-lookup"><span data-stu-id="6742a-844">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="6742a-845">Velký počet blokujících synchronních vstupně-výstupních operací může vést ke vyčerpání fondu vláken, což aplikaci nereaguje.</span><span class="sxs-lookup"><span data-stu-id="6742a-845">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="6742a-846">Povolit pouze `AllowSynchronousIO` při použití knihovny, která nepodporuje asynchronní vstupně-výstupní operace.</span><span class="sxs-lookup"><span data-stu-id="6742a-846">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="6742a-847">Následující příklad zakáže synchronní vstupně-výstupní operace:</span><span class="sxs-lookup"><span data-stu-id="6742a-847">The following example disables synchronous I/O:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="6742a-848">Informace o dalších možnostech a omezeních Kestrel najdete v tématech:</span><span class="sxs-lookup"><span data-stu-id="6742a-848">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="6742a-849">Konfigurace koncového bodu</span><span class="sxs-lookup"><span data-stu-id="6742a-849">Endpoint configuration</span></span>

<span data-ttu-id="6742a-850">Ve výchozím nastavení ASP.NET Core váže k:</span><span class="sxs-lookup"><span data-stu-id="6742a-850">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="6742a-851">`https://localhost:5001`(Pokud je k dispozici místní vývojový certifikát)</span><span class="sxs-lookup"><span data-stu-id="6742a-851">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="6742a-852">Zadejte adresy URL pomocí:</span><span class="sxs-lookup"><span data-stu-id="6742a-852">Specify URLs using the:</span></span>

* <span data-ttu-id="6742a-853">`ASPNETCORE_URLS`Proměnná prostředí.</span><span class="sxs-lookup"><span data-stu-id="6742a-853">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="6742a-854">`--urls`argument příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="6742a-854">`--urls` command-line argument.</span></span>
* <span data-ttu-id="6742a-855">`urls`konfigurační klíč hostitele.</span><span class="sxs-lookup"><span data-stu-id="6742a-855">`urls` host configuration key.</span></span>
* <span data-ttu-id="6742a-856">`UseUrls`Metoda rozšíření</span><span class="sxs-lookup"><span data-stu-id="6742a-856">`UseUrls` extension method.</span></span>

<span data-ttu-id="6742a-857">Hodnota zadaná pomocí těchto přístupů může být jeden nebo více koncových bodů HTTP a HTTPS (HTTPS, pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="6742a-857">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="6742a-858">Nakonfigurujte hodnotu jako seznam oddělený středníkem (například `"Urls": "http://localhost:8000;http://localhost:8001"` ).</span><span class="sxs-lookup"><span data-stu-id="6742a-858">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="6742a-859">Další informace o těchto přístupůch najdete v tématu [adresy URL serveru](xref:fundamentals/host/web-host#server-urls) a [potlačení konfigurace](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="6742a-859">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="6742a-860">Vytvoří se vývojový certifikát:</span><span class="sxs-lookup"><span data-stu-id="6742a-860">A development certificate is created:</span></span>

* <span data-ttu-id="6742a-861">Po instalaci [.NET Core SDK](/dotnet/core/sdk) .</span><span class="sxs-lookup"><span data-stu-id="6742a-861">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="6742a-862">[Nástroj pro vývoj a certifikáty](xref:aspnetcore-2.1#https) slouží k vytvoření certifikátu.</span><span class="sxs-lookup"><span data-stu-id="6742a-862">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="6742a-863">Některé prohlížeče vyžadují udělení explicitního oprávnění pro důvěřování místního vývojového certifikátu.</span><span class="sxs-lookup"><span data-stu-id="6742a-863">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="6742a-864">Šablony projektů konfigurují aplikace tak, aby běžely ve výchozím nastavení HTTPS a zahrnovaly [přesměrování https a podporu HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="6742a-864">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="6742a-865">Volání <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> nebo <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> metody pro <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> konfiguraci předpon adres URL a portů pro Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6742a-865">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="6742a-866">`UseUrls`, `--urls` argument příkazového řádku, `urls` konfigurační klíč hostitele a `ASPNETCORE_URLS` Proměnná prostředí fungují také, ale mají omezení zaznamenaná později v této části (pro konfiguraci koncového bodu https musí být k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="6742a-866">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="6742a-867">`KestrelServerOptions`rozšířeného</span><span class="sxs-lookup"><span data-stu-id="6742a-867">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="6742a-868">ConfigureEndpointDefaults (akce \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="6742a-868">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="6742a-869">Určuje konfiguraci `Action` , která se spustí pro každý zadaný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="6742a-869">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="6742a-870">Volání několikrát `ConfigureEndpointDefaults` nahrazuje předchozí `Action` `Action` zadanou parametr s.</span><span class="sxs-lookup"><span data-stu-id="6742a-870">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="6742a-871">Koncové body vytvořené voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **před** voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> nebudou použity výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="6742a-871">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="6742a-872">ConfigureHttpsDefaults (akce \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="6742a-872">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="6742a-873">Určuje konfiguraci `Action` , která se má spustit pro každý koncový bod HTTPS.</span><span class="sxs-lookup"><span data-stu-id="6742a-873">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="6742a-874">Volání několikrát `ConfigureHttpsDefaults` nahrazuje předchozí `Action` `Action` zadanou parametr s.</span><span class="sxs-lookup"><span data-stu-id="6742a-874">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="6742a-875">Koncové body vytvořené voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **před** voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> nebudou použity výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="6742a-875">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="6742a-876">Konfigurace (IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="6742a-876">Configure(IConfiguration)</span></span>

<span data-ttu-id="6742a-877">Vytvoří zavaděč konfigurace pro nastavení Kestrel, který přebírá <xref:Microsoft.Extensions.Configuration.IConfiguration> jako vstup.</span><span class="sxs-lookup"><span data-stu-id="6742a-877">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="6742a-878">Konfigurace musí být vymezená na konfigurační oddíl pro Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6742a-878">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="6742a-879">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="6742a-879">ListenOptions.UseHttps</span></span>

<span data-ttu-id="6742a-880">Nakonfigurujte Kestrel tak, aby používal protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="6742a-880">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="6742a-881">`ListenOptions.UseHttps`SND</span><span class="sxs-lookup"><span data-stu-id="6742a-881">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="6742a-882">`UseHttps`: Nakonfigurujte Kestrel tak, aby používal HTTPS s výchozím certifikátem.</span><span class="sxs-lookup"><span data-stu-id="6742a-882">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="6742a-883">Vyvolá výjimku, pokud není nakonfigurován žádný výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="6742a-883">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="6742a-884">`ListenOptions.UseHttps`ukazatelů</span><span class="sxs-lookup"><span data-stu-id="6742a-884">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="6742a-885">`filename`je cesta a název souboru certifikátu vzhledem k adresáři, který obsahuje soubory obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="6742a-885">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="6742a-886">`password`je vyžadováno heslo pro přístup k datům certifikátu X. 509.</span><span class="sxs-lookup"><span data-stu-id="6742a-886">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="6742a-887">`configureOptions`je `Action` ke konfiguraci `HttpsConnectionAdapterOptions` .</span><span class="sxs-lookup"><span data-stu-id="6742a-887">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="6742a-888">Vrátí `ListenOptions` .</span><span class="sxs-lookup"><span data-stu-id="6742a-888">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="6742a-889">`storeName`je úložiště certifikátů, ze kterého se má certifikát načíst.</span><span class="sxs-lookup"><span data-stu-id="6742a-889">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="6742a-890">`subject`je název subjektu certifikátu.</span><span class="sxs-lookup"><span data-stu-id="6742a-890">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="6742a-891">`allowInvalid`Určuje, zda by měly být zváženy neplatné certifikáty, například certifikáty podepsané svým držitelem.</span><span class="sxs-lookup"><span data-stu-id="6742a-891">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="6742a-892">`location`je umístěním úložiště, ze kterého se má certifikát načíst.</span><span class="sxs-lookup"><span data-stu-id="6742a-892">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="6742a-893">`serverCertificate`je certifikát X. 509.</span><span class="sxs-lookup"><span data-stu-id="6742a-893">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="6742a-894">V produkčním prostředí musí být HTTPS explicitně nakonfigurovaný.</span><span class="sxs-lookup"><span data-stu-id="6742a-894">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="6742a-895">Musí být zadán minimálně výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="6742a-895">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="6742a-896">Podporované konfigurace popsané dál:</span><span class="sxs-lookup"><span data-stu-id="6742a-896">Supported configurations described next:</span></span>

* <span data-ttu-id="6742a-897">Žádná konfigurace</span><span class="sxs-lookup"><span data-stu-id="6742a-897">No configuration</span></span>
* <span data-ttu-id="6742a-898">Nahradit výchozí certifikát z konfigurace</span><span class="sxs-lookup"><span data-stu-id="6742a-898">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="6742a-899">Změna výchozích hodnot v kódu</span><span class="sxs-lookup"><span data-stu-id="6742a-899">Change the defaults in code</span></span>

<span data-ttu-id="6742a-900">*Žádná konfigurace*</span><span class="sxs-lookup"><span data-stu-id="6742a-900">*No configuration*</span></span>

<span data-ttu-id="6742a-901">Kestrel naslouchá na `http://localhost:5000` a `https://localhost:5001` (Pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="6742a-901">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="6742a-902">*Nahradit výchozí certifikát z konfigurace*</span><span class="sxs-lookup"><span data-stu-id="6742a-902">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="6742a-903">`CreateDefaultBuilder`Při `Configure(context.Configuration.GetSection("Kestrel"))` načítání konfigurace Kestrel ve výchozím nastavení volá.</span><span class="sxs-lookup"><span data-stu-id="6742a-903">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="6742a-904">Pro Kestrel je k dispozici výchozí schéma konfigurace nastavení aplikace HTTPS.</span><span class="sxs-lookup"><span data-stu-id="6742a-904">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="6742a-905">Nakonfigurujte několik koncových bodů, včetně adres URL a certifikátů, které se mají použít, buď ze souboru na disku, nebo z úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="6742a-905">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="6742a-906">V následujícím *appsettings.js* například:</span><span class="sxs-lookup"><span data-stu-id="6742a-906">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="6742a-907">Nastavte **AllowInvalid** na `true` , aby povolovala použití neplatných certifikátů (například certifikátů podepsaných svým držitelem).</span><span class="sxs-lookup"><span data-stu-id="6742a-907">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="6742a-908">Libovolný koncový bod HTTPS, který neurčuje certifikát (**HttpsDefaultCert** v následujícím příkladu), se vrátí k certifikátu definovanému v části **certifikáty** > **výchozí** nebo vývojový certifikát.</span><span class="sxs-lookup"><span data-stu-id="6742a-908">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="6742a-909">Alternativou k použití **cesty** a **hesla** pro libovolný uzel certifikátu je zadání certifikátu pomocí polí úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="6742a-909">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="6742a-910">Například **Certificates**  >  **výchozí** certifikát může být zadán jako:</span><span class="sxs-lookup"><span data-stu-id="6742a-910">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="6742a-911">Poznámky ke schématu:</span><span class="sxs-lookup"><span data-stu-id="6742a-911">Schema notes:</span></span>

* <span data-ttu-id="6742a-912">V názvech koncových bodů se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="6742a-912">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="6742a-913">Například `HTTPS` a `Https` jsou platné.</span><span class="sxs-lookup"><span data-stu-id="6742a-913">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="6742a-914">`Url`U každého koncového bodu je vyžadován parametr.</span><span class="sxs-lookup"><span data-stu-id="6742a-914">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="6742a-915">Formát pro tento parametr je stejný jako `Urls` konfigurační parametr nejvyšší úrovně s tím rozdílem, že je omezen na jedinou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="6742a-915">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="6742a-916">Tyto koncové body nahrazují hodnoty definované v konfiguraci nejvyšší úrovně `Urls` místo jejich přidání do těchto koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="6742a-916">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="6742a-917">Koncové body definované v kódu prostřednictvím `Listen` jsou kumulativní pomocí koncových bodů definovaných v konfiguračním oddílu.</span><span class="sxs-lookup"><span data-stu-id="6742a-917">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="6742a-918">`Certificate`Oddíl je nepovinný.</span><span class="sxs-lookup"><span data-stu-id="6742a-918">The `Certificate` section is optional.</span></span> <span data-ttu-id="6742a-919">Pokud `Certificate` není oddíl zadaný, použijí se výchozí hodnoty definované v dřívějších scénářích.</span><span class="sxs-lookup"><span data-stu-id="6742a-919">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="6742a-920">Pokud nejsou k dispozici žádné výchozí hodnoty, Server vyvolá výjimku a nemůže se spustit.</span><span class="sxs-lookup"><span data-stu-id="6742a-920">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="6742a-921">`Certificate`Oddíl podporuje jak heslo **cesty** &ndash; **Password** , tak **Subject** &ndash; certifikáty**úložiště** subjektu.</span><span class="sxs-lookup"><span data-stu-id="6742a-921">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="6742a-922">V takovém případě může být definován libovolný počet koncových bodů, pokud to nezpůsobí konflikty portů.</span><span class="sxs-lookup"><span data-stu-id="6742a-922">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="6742a-923">`options.Configure(context.Configuration.GetSection("{SECTION}"))`Vrátí `KestrelConfigurationLoader` `.Endpoint(string name, listenOptions => { })` metodu s metodou, kterou lze použít k doplnění nastavení nakonfigurovaného koncového bodu:</span><span class="sxs-lookup"><span data-stu-id="6742a-923">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="6742a-924">`KestrelServerOptions.ConfigurationLoader`k pokračování v iteraci u stávajícího zavaděče se dá získat přímý odkaz, jako je třeba ten, který poskytuje <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="6742a-924">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="6742a-925">Konfigurační oddíl pro každý koncový bod je k dispozici na možnostech v `Endpoint` metodě, aby bylo možné číst vlastní nastavení.</span><span class="sxs-lookup"><span data-stu-id="6742a-925">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="6742a-926">Více konfigurací může být načteno voláním `options.Configure(context.Configuration.GetSection("{SECTION}"))` znovu s jiným oddílem.</span><span class="sxs-lookup"><span data-stu-id="6742a-926">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="6742a-927">Je použita pouze poslední konfigurace, pokud `Load` není explicitně volána při předchozích instancích.</span><span class="sxs-lookup"><span data-stu-id="6742a-927">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="6742a-928">Metapackage nevolá `Load` , aby se jeho výchozí konfigurační oddíl mohl nahradit.</span><span class="sxs-lookup"><span data-stu-id="6742a-928">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="6742a-929">`KestrelConfigurationLoader`zrcadlí `Listen` rodinu rozhraní API z `KestrelServerOptions` as `Endpoint` přetížení, takže koncové body kódu a konfigurace lze konfigurovat na stejném místě.</span><span class="sxs-lookup"><span data-stu-id="6742a-929">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="6742a-930">Tato přetížení nepoužívají názvy a využívají pouze výchozí nastavení z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="6742a-930">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="6742a-931">*Změna výchozích hodnot v kódu*</span><span class="sxs-lookup"><span data-stu-id="6742a-931">*Change the defaults in code*</span></span>

<span data-ttu-id="6742a-932">`ConfigureEndpointDefaults`a `ConfigureHttpsDefaults` dá se použít ke změně výchozího nastavení pro `ListenOptions` a `HttpsConnectionAdapterOptions` , včetně přepsání výchozího certifikátu zadaného v předchozím scénáři.</span><span class="sxs-lookup"><span data-stu-id="6742a-932">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="6742a-933">`ConfigureEndpointDefaults`a `ConfigureHttpsDefaults` měla by být volána před konfigurací koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="6742a-933">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="6742a-934">*Podpora Kestrel pro SNI*</span><span class="sxs-lookup"><span data-stu-id="6742a-934">*Kestrel support for SNI*</span></span>

<span data-ttu-id="6742a-935">[Indikace názvu serveru (SNI)](https://tools.ietf.org/html/rfc6066#section-3) lze použít k hostování více domén na stejné IP adrese a portu.</span><span class="sxs-lookup"><span data-stu-id="6742a-935">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="6742a-936">Aby SNI fungovalo, klient pošle název hostitele pro zabezpečenou relaci serveru během metody handshake TLS, aby server mohl poskytovat správný certifikát.</span><span class="sxs-lookup"><span data-stu-id="6742a-936">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="6742a-937">Klient používá poskytnutý certifikát pro šifrovanou komunikaci se serverem během zabezpečené relace, která následuje za metodou handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="6742a-937">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="6742a-938">Kestrel podporuje SNI prostřednictvím `ServerCertificateSelector` zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="6742a-938">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="6742a-939">Zpětné volání je vyvoláno jednou za připojení, které aplikaci umožní zkontrolovat název hostitele a vybrat příslušný certifikát.</span><span class="sxs-lookup"><span data-stu-id="6742a-939">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="6742a-940">Podpora SNI vyžaduje:</span><span class="sxs-lookup"><span data-stu-id="6742a-940">SNI support requires:</span></span>

* <span data-ttu-id="6742a-941">Běží v cílovém rozhraní `netcoreapp2.1` nebo novějším.</span><span class="sxs-lookup"><span data-stu-id="6742a-941">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="6742a-942">V systému `net461` nebo novějším je zpětné volání vyvoláno, ale `name` je vždy `null` .</span><span class="sxs-lookup"><span data-stu-id="6742a-942">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="6742a-943">`name`Je také v `null` případě, že klient v TLS handshake neposkytne parametr názvu hostitele.</span><span class="sxs-lookup"><span data-stu-id="6742a-943">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="6742a-944">Všechny weby běží na stejné instanci Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6742a-944">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="6742a-945">Kestrel nepodporuje sdílení IP adresy a portu napříč několika instancemi bez reverzního proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="6742a-945">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="6742a-946">Protokolování připojení</span><span class="sxs-lookup"><span data-stu-id="6742a-946">Connection logging</span></span>

<span data-ttu-id="6742a-947">Volá <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> se, aby se vygenerovaly protokoly na úrovni ladění pro komunikaci na úrovni bajtového připojení.</span><span class="sxs-lookup"><span data-stu-id="6742a-947">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="6742a-948">Protokolování připojení je užitečné při řešení problémů v komunikaci nízké úrovně, například během šifrování TLS a za proxy servery.</span><span class="sxs-lookup"><span data-stu-id="6742a-948">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="6742a-949">Pokud `UseConnectionLogging` je umístěn před `UseHttps` , zaprotokoluje se zašifrovaný provoz.</span><span class="sxs-lookup"><span data-stu-id="6742a-949">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="6742a-950">Pokud `UseConnectionLogging` je umístěn po `UseHttps` , zaprotokoluje se dešifrovaný provoz.</span><span class="sxs-lookup"><span data-stu-id="6742a-950">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="6742a-951">Vytvoření vazby na soket TCP</span><span class="sxs-lookup"><span data-stu-id="6742a-951">Bind to a TCP socket</span></span>

<span data-ttu-id="6742a-952"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>Metoda se váže k soketu TCP a pomocí lambda možností povolíte konfiguraci certifikátu X. 509:</span><span class="sxs-lookup"><span data-stu-id="6742a-952">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

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

<span data-ttu-id="6742a-953">Příklad nakonfiguruje HTTPS pro koncový bod pomocí <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> .</span><span class="sxs-lookup"><span data-stu-id="6742a-953">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="6742a-954">Ke konfiguraci dalších nastavení Kestrel pro konkrétní koncové body použijte stejné rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="6742a-954">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="6742a-955">Vytvoření vazby na soket UNIX</span><span class="sxs-lookup"><span data-stu-id="6742a-955">Bind to a Unix socket</span></span>

<span data-ttu-id="6742a-956">Naslouchat na soketu se systémem UNIX <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> , aby se zlepšil výkon pomocí Nginx, jak je znázorněno v tomto příkladu:</span><span class="sxs-lookup"><span data-stu-id="6742a-956">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

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

* <span data-ttu-id="6742a-957">V souboru Nginx confiuguration nastavte `server`  >  `location`  >  `proxy_pass` položku na `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="6742a-957">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="6742a-958">`{KESTREL SOCKET}`je název soketu poskytnutý <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (například `kestrel-test.sock` v předchozím příkladu).</span><span class="sxs-lookup"><span data-stu-id="6742a-958">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="6742a-959">Zajistěte, aby byl soket zapisovatelný pomocí Nginx (například `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="6742a-959">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="6742a-960">Port 0</span><span class="sxs-lookup"><span data-stu-id="6742a-960">Port 0</span></span>

<span data-ttu-id="6742a-961">Po zadání čísla portu se `0` Kestrel dynamicky váže k dostupnému portu.</span><span class="sxs-lookup"><span data-stu-id="6742a-961">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="6742a-962">Následující příklad ukazuje, jak určit, který port Kestrel je skutečně vázaný za běhu:</span><span class="sxs-lookup"><span data-stu-id="6742a-962">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="6742a-963">Když se aplikace spustí, výstup okna konzoly indikuje dynamický port, na který se aplikace dá získat:</span><span class="sxs-lookup"><span data-stu-id="6742a-963">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="6742a-964">Omezení</span><span class="sxs-lookup"><span data-stu-id="6742a-964">Limitations</span></span>

<span data-ttu-id="6742a-965">Konfigurace koncových bodů pomocí následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="6742a-965">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="6742a-966">`--urls`argument příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="6742a-966">`--urls` command-line argument</span></span>
* <span data-ttu-id="6742a-967">`urls`konfigurační klíč hostitele</span><span class="sxs-lookup"><span data-stu-id="6742a-967">`urls` host configuration key</span></span>
* <span data-ttu-id="6742a-968">`ASPNETCORE_URLS`Proměnná prostředí</span><span class="sxs-lookup"><span data-stu-id="6742a-968">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="6742a-969">Tyto metody jsou užitečné, pokud chcete, aby kód pracoval na jiných serverech než Kestrel.</span><span class="sxs-lookup"><span data-stu-id="6742a-969">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="6742a-970">Mějte ale na paměti následující omezení:</span><span class="sxs-lookup"><span data-stu-id="6742a-970">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="6742a-971">Protokol HTTPS nelze s těmito přístupy použít, pokud není k dispozici výchozí certifikát v konfiguraci koncového bodu HTTPS (například pomocí `KestrelServerOptions` Konfigurace nebo konfiguračního souboru, jak je uvedeno výše v tomto tématu).</span><span class="sxs-lookup"><span data-stu-id="6742a-971">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="6742a-972">Při `Listen` `UseUrls` současném použití obou přístupů a `Listen` koncových bodů se koncové body přepisují `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="6742a-972">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="6742a-973">Konfigurace koncového bodu služby IIS</span><span class="sxs-lookup"><span data-stu-id="6742a-973">IIS endpoint configuration</span></span>

<span data-ttu-id="6742a-974">Při použití služby IIS jsou vazby adresy URL pro přepsání vazby služby IIS nastaveny buď `Listen` nebo `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="6742a-974">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="6742a-975">Další informace najdete v tématu [ASP.NET Core modulu](xref:host-and-deploy/aspnet-core-module) .</span><span class="sxs-lookup"><span data-stu-id="6742a-975">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="6742a-976">Konfigurace přenosu</span><span class="sxs-lookup"><span data-stu-id="6742a-976">Transport configuration</span></span>

<span data-ttu-id="6742a-977">S vydáním ASP.NET Core 2,1 není výchozí přenos Kestrel založen na Libuv, ale na základě spravovaných soketů.</span><span class="sxs-lookup"><span data-stu-id="6742a-977">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="6742a-978">Jedná se o zásadní změnu pro aplikace ASP.NET Core 2,0 upgradované na 2,1, které volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> a závisí na jednom z následujících balíčků:</span><span class="sxs-lookup"><span data-stu-id="6742a-978">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="6742a-979">[Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (přímý odkaz na balíček)</span><span class="sxs-lookup"><span data-stu-id="6742a-979">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="6742a-980">Microsoft. AspNetCore. app</span><span class="sxs-lookup"><span data-stu-id="6742a-980">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="6742a-981">Pro projekty, které vyžadují použití Libuv:</span><span class="sxs-lookup"><span data-stu-id="6742a-981">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="6742a-982">Přidejte do souboru projektu aplikace závislost pro balíček [Microsoft. AspNetCore. Server. Kestrel. Transport. Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) :</span><span class="sxs-lookup"><span data-stu-id="6742a-982">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="6742a-983">Volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> :</span><span class="sxs-lookup"><span data-stu-id="6742a-983">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="6742a-984">Předpony adresy URL</span><span class="sxs-lookup"><span data-stu-id="6742a-984">URL prefixes</span></span>

<span data-ttu-id="6742a-985">Při použití `UseUrls` , `--urls` argumentu příkazového řádku, `urls` konfiguračního klíče hostitele nebo `ASPNETCORE_URLS` proměnné prostředí můžou být předpony adresy URL v některém z následujících formátů.</span><span class="sxs-lookup"><span data-stu-id="6742a-985">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="6742a-986">Platné jsou pouze předpony adresy URL protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="6742a-986">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="6742a-987">Kestrel nepodporuje protokol HTTPS při konfiguraci vazeb URL pomocí `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="6742a-987">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="6742a-988">Adresa IPv4 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="6742a-988">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="6742a-989">`0.0.0.0`je zvláštní případ, který se váže ke všem adresám IPv4.</span><span class="sxs-lookup"><span data-stu-id="6742a-989">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="6742a-990">Adresa IPv6 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="6742a-990">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="6742a-991">`[::]`je ekvivalentem protokolu IPv4 pro protokol IPv6 `0.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="6742a-991">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="6742a-992">Název hostitele s číslem portu</span><span class="sxs-lookup"><span data-stu-id="6742a-992">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="6742a-993">Názvy hostitelů, `*` a `+` nejsou speciální.</span><span class="sxs-lookup"><span data-stu-id="6742a-993">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="6742a-994">Cokoli se nerozpoznalo jako platná IP adresa nebo `localhost` se váže ke všem IP adresám IPv4 a IPv6.</span><span class="sxs-lookup"><span data-stu-id="6742a-994">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="6742a-995">Pokud chcete navazovat různé názvy hostitelů na různé ASP.NET Core aplikace na stejném portu, použijte [HTTP.sys](xref:fundamentals/servers/httpsys) nebo reverzní proxy server, jako je IIS, Nginx nebo Apache.</span><span class="sxs-lookup"><span data-stu-id="6742a-995">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="6742a-996">Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="6742a-996">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="6742a-997">`localhost`Název hostitele s číslem portu nebo IP adresou zpětné smyčky s číslem portu</span><span class="sxs-lookup"><span data-stu-id="6742a-997">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="6742a-998">Když `localhost` je zadaný, Kestrel se pokusí vytvořit propojení s rozhraními zpětné smyčky IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="6742a-998">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="6742a-999">Pokud je požadovaný port používán jinou službou na rozhraní zpětné smyčky, spuštění Kestrel se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="6742a-999">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="6742a-1000">Pokud je rozhraní zpětné smyčky v žádném jiném případě nedostupné (většinou kvůli tomu, že protokol IPv6 není podporovaný), Kestrel zaznamená upozornění.</span><span class="sxs-lookup"><span data-stu-id="6742a-1000">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="6742a-1001">Filtrování hostitele</span><span class="sxs-lookup"><span data-stu-id="6742a-1001">Host filtering</span></span>

<span data-ttu-id="6742a-1002">I když Kestrel podporuje konfiguraci na základě prefixů `http://example.com:5000` , jako je, Kestrel převážně ignoruje název hostitele.</span><span class="sxs-lookup"><span data-stu-id="6742a-1002">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="6742a-1003">Hostitel `localhost` je zvláštní případ, který se používá pro vazbu na adresy zpětné smyčky.</span><span class="sxs-lookup"><span data-stu-id="6742a-1003">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="6742a-1004">Každý hostitel, který je jiný než explicitní IP adresa, se váže na všechny veřejné IP adresy.</span><span class="sxs-lookup"><span data-stu-id="6742a-1004">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="6742a-1005">`Host`hlavičky nejsou ověřené.</span><span class="sxs-lookup"><span data-stu-id="6742a-1005">`Host` headers aren't validated.</span></span>

<span data-ttu-id="6742a-1006">Jako alternativní řešení použijte middleware pro filtrování hostitele.</span><span class="sxs-lookup"><span data-stu-id="6742a-1006">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="6742a-1007">Middleware pro filtrování hostitele poskytuje balíček [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , který je součástí [Microsoft. AspNetCore. App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 nebo 2,2).</span><span class="sxs-lookup"><span data-stu-id="6742a-1007">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="6742a-1008">Middleware je přidána pomocí <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> , který volá <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> :</span><span class="sxs-lookup"><span data-stu-id="6742a-1008">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="6742a-1009">Middleware pro filtrování hostitele je ve výchozím nastavení zakázáno.</span><span class="sxs-lookup"><span data-stu-id="6742a-1009">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="6742a-1010">Chcete-li povolit middleware, definujte `AllowedHosts` klíč v *appsettings.jsna* / *appSettings. \<EnvironmentName> . JSON*.</span><span class="sxs-lookup"><span data-stu-id="6742a-1010">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="6742a-1011">Hodnota je seznam názvů hostitelů oddělených středníkem bez čísel portů:</span><span class="sxs-lookup"><span data-stu-id="6742a-1011">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="6742a-1012">*appsettings.js*:</span><span class="sxs-lookup"><span data-stu-id="6742a-1012">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="6742a-1013">[Middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer) má také <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> možnost.</span><span class="sxs-lookup"><span data-stu-id="6742a-1013">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="6742a-1014">Middleware předávaných hlaviček a middleware pro filtrování hostitele mají podobné funkce pro různé scénáře.</span><span class="sxs-lookup"><span data-stu-id="6742a-1014">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="6742a-1015">Nastavení `AllowedHosts` pomocí předávaných middlewarových hlaviček je vhodné v případě, že `Host` Hlavička není zachována při předávání požadavků pomocí reverzního proxy server nebo nástroje pro vyrovnávání zatížení.</span><span class="sxs-lookup"><span data-stu-id="6742a-1015">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="6742a-1016">Nastavení `AllowedHosts` pomocí middlewaru pro filtrování hostitele je vhodné, když se Kestrel používá jako veřejný hraniční Server nebo když se `Host` Hlavička přímo přepošle.</span><span class="sxs-lookup"><span data-stu-id="6742a-1016">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="6742a-1017">Další informace o middlewaru předávaných hlaviček najdete v tématu <xref:host-and-deploy/proxy-load-balancer> .</span><span class="sxs-lookup"><span data-stu-id="6742a-1017">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

## <a name="http11-request-draining"></a><span data-ttu-id="6742a-1018">Vyprazdňování požadavku HTTP/1.1</span><span class="sxs-lookup"><span data-stu-id="6742a-1018">HTTP/1.1 request draining</span></span>

<span data-ttu-id="6742a-1019">Otevírání připojení HTTP je časově náročné.</span><span class="sxs-lookup"><span data-stu-id="6742a-1019">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="6742a-1020">V případě protokolu HTTPS je také náročný na prostředky.</span><span class="sxs-lookup"><span data-stu-id="6742a-1020">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="6742a-1021">Proto se Kestrel pokusí znovu použít připojení na protokol HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="6742a-1021">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="6742a-1022">Aby bylo možné připojení znovu použít, musí být text žádosti plně spotřebovaný.</span><span class="sxs-lookup"><span data-stu-id="6742a-1022">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="6742a-1023">Aplikace nespotřebovává text žádosti vždycky, například `POST` žádosti, kde server vrací přesměrování nebo odpověď 404.</span><span class="sxs-lookup"><span data-stu-id="6742a-1023">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="6742a-1024">V `POST` případě případu přesměrování:</span><span class="sxs-lookup"><span data-stu-id="6742a-1024">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="6742a-1025">Klient již mohl odeslat část `POST` dat.</span><span class="sxs-lookup"><span data-stu-id="6742a-1025">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="6742a-1026">Server zapisuje odpověď 301.</span><span class="sxs-lookup"><span data-stu-id="6742a-1026">The server writes the 301 response.</span></span>
* <span data-ttu-id="6742a-1027">Připojení nelze použít pro novou žádost, dokud nebudou `POST` data z předchozího textu požadavku plně přečtena.</span><span class="sxs-lookup"><span data-stu-id="6742a-1027">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="6742a-1028">Kestrel se pokusí vyprázdnit tělo žádosti.</span><span class="sxs-lookup"><span data-stu-id="6742a-1028">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="6742a-1029">Vyprázdnit tělo žádosti znamená čtení a zahození dat bez jejich zpracování.</span><span class="sxs-lookup"><span data-stu-id="6742a-1029">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="6742a-1030">Proces vyprazdňování vytváří tradoff mezi povolením opětovného použití připojení a časem potřebným k vyprázdnění zbývajících dat:</span><span class="sxs-lookup"><span data-stu-id="6742a-1030">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="6742a-1031">Vyprázdnění má časový limit pět sekund, což není konfigurovatelné.</span><span class="sxs-lookup"><span data-stu-id="6742a-1031">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="6742a-1032">Pokud nebyla všechna data zadaná v `Content-Length` `Transfer-Encoding` hlavičce nebo přečtena před časovým limitem, připojení je zavřeno.</span><span class="sxs-lookup"><span data-stu-id="6742a-1032">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="6742a-1033">Někdy je možné, že budete chtít žádost ukončit hned nebo po zápisu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="6742a-1033">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="6742a-1034">Například klienti mohou mít omezující data, takže omezení nahraných dat může být prioritou.</span><span class="sxs-lookup"><span data-stu-id="6742a-1034">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="6742a-1035">V takových případech pro ukončení žádosti zavolejte [HttpContext. Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) z kontroleru, Razor stránky nebo middlewaru.</span><span class="sxs-lookup"><span data-stu-id="6742a-1035">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="6742a-1036">Existují upozornění pro volání `Abort` :</span><span class="sxs-lookup"><span data-stu-id="6742a-1036">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="6742a-1037">Vytváření nových připojení může být pomalé a nákladné.</span><span class="sxs-lookup"><span data-stu-id="6742a-1037">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="6742a-1038">Není zaručeno, že klient před zavřením připojení přečetl odpověď.</span><span class="sxs-lookup"><span data-stu-id="6742a-1038">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="6742a-1039">Volání `Abort` by mělo být vzácné a vyhrazené pro závažné chybové případy, ne běžné chyby.</span><span class="sxs-lookup"><span data-stu-id="6742a-1039">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="6742a-1040">Volá se jenom v případě, že je `Abort` potřeba vyřešit konkrétní problém.</span><span class="sxs-lookup"><span data-stu-id="6742a-1040">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="6742a-1041">Například volejte, `Abort` Pokud se zlomyslní klienti pokoušejí `POST` data nebo když dojde k chybě v kódu klienta, která způsobuje velké nebo velké požadavky.</span><span class="sxs-lookup"><span data-stu-id="6742a-1041">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="6742a-1042">Nevolejte `Abort` pro běžné chybové situace, například HTTP 404 (Nenalezeno).</span><span class="sxs-lookup"><span data-stu-id="6742a-1042">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="6742a-1043">Volání [HttpResponse. CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) před voláním `Abort` zajistí, že server dokončil zápis odpovědi.</span><span class="sxs-lookup"><span data-stu-id="6742a-1043">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="6742a-1044">Nicméně chování klienta není předvídatelné a nemusí před přerušením připojení číst odpověď.</span><span class="sxs-lookup"><span data-stu-id="6742a-1044">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="6742a-1045">Tento proces se liší od protokolu HTTP/2, protože protokol podporuje přerušování jednotlivých datových proudů požadavků bez ukončení připojení.</span><span class="sxs-lookup"><span data-stu-id="6742a-1045">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="6742a-1046">Nedá se použít 5 sekundový časový limit vyprázdnění.</span><span class="sxs-lookup"><span data-stu-id="6742a-1046">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="6742a-1047">Pokud po dokončení odpovědi dojde k nepřečteným datům textu žádosti, server odešle snímek HTTP/2 RST.</span><span class="sxs-lookup"><span data-stu-id="6742a-1047">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="6742a-1048">Další datové snímky požadavku jsou ignorovány.</span><span class="sxs-lookup"><span data-stu-id="6742a-1048">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="6742a-1049">Pokud je to možné, je lepší, aby klienti využili hlavičku žádosti [očekávat: 100-pokračovat](https://developer.mozilla.org/docs/Web/HTTP/Status/100) a čekali na odpověď serveru před zahájením odesílání textu žádosti.</span><span class="sxs-lookup"><span data-stu-id="6742a-1049">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="6742a-1050">Díky tomu může klient příležitost před odesláním nepotřebných dat ověřit odpověď a zrušit jejich přerušení.</span><span class="sxs-lookup"><span data-stu-id="6742a-1050">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="6742a-1051">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="6742a-1051">Additional resources</span></span>

* <span data-ttu-id="6742a-1052">Při použití soketů UNIX v systému Linux se soket při vypnutí aplikace automaticky neodstraní.</span><span class="sxs-lookup"><span data-stu-id="6742a-1052">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="6742a-1053">Další informace najdete v [tomto problému GitHubu](https://github.com/dotnet/aspnetcore/issues/14134).</span><span class="sxs-lookup"><span data-stu-id="6742a-1053">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="6742a-1054">RFC 7230: syntaxe zprávy a směrování (oddíl 5,4: hostitel)</span><span class="sxs-lookup"><span data-stu-id="6742a-1054">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
