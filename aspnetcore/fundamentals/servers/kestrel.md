---
title: Implementace webového serveru Kestrel v ASP.NET Core
author: rick-anderson
description: Seznamte se s Kestrelem, webovým serverem pro různé platformy pro ASP.NET Core.
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/10/2020
uid: fundamentals/servers/kestrel
ms.openlocfilehash: 18846d60fd5c29f17cb4e59192795fd92251e2d0
ms.sourcegitcommit: f0aeeab6ab6e09db713bb9b7862c45f4d447771b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80976765"
---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="d978a-103">Implementace webového serveru Kestrel v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d978a-103">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="d978a-104">Tom [Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), a Stephen [Halter](https://twitter.com/halter73)</span><span class="sxs-lookup"><span data-stu-id="d978a-104">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d978a-105">Kestrel je multiplatformní [webový server pro ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="d978a-105">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="d978a-106">Kestrel je webový server, který je ve výchozím nastavení zahrnut v šablonách projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d978a-106">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="d978a-107">Poštolka podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="d978a-107">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="d978a-108">HTTPS</span><span class="sxs-lookup"><span data-stu-id="d978a-108">HTTPS</span></span>
* <span data-ttu-id="d978a-109">Neprůhledný upgrade používaný k povolení [websocketů](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="d978a-109">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="d978a-110">Unixové zásuvky pro vysoký výkon za Nginx</span><span class="sxs-lookup"><span data-stu-id="d978a-110">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="d978a-111">HTTP/2 (kromě macOS)&dagger;</span><span class="sxs-lookup"><span data-stu-id="d978a-111">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="d978a-112">&dagger;HTTP/2 bude v budoucí verzi v systému macOS podporován.</span><span class="sxs-lookup"><span data-stu-id="d978a-112">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="d978a-113">Kestrel je podporován na všech platformách a verzích, které podporuje .NET Core.</span><span class="sxs-lookup"><span data-stu-id="d978a-113">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="d978a-114">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="d978a-114">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="d978a-115">Podpora HTTP/2</span><span class="sxs-lookup"><span data-stu-id="d978a-115">HTTP/2 support</span></span>

<span data-ttu-id="d978a-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) je k dispozici pro ASP.NET základní aplikace, pokud jsou splněny následující základní požadavky:</span><span class="sxs-lookup"><span data-stu-id="d978a-116">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="d978a-117">Operační systém&dagger;</span><span class="sxs-lookup"><span data-stu-id="d978a-117">Operating system&dagger;</span></span>
  * <span data-ttu-id="d978a-118">Windows Server 2016/Windows 10 nebo novější&Dagger;</span><span class="sxs-lookup"><span data-stu-id="d978a-118">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="d978a-119">Linux s OpenSSL 1.0.2 nebo novějším (například Ubuntu 16.04 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="d978a-119">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="d978a-120">Cílová architektura: .NET Core 2.2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="d978a-120">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="d978a-121">Připojení [protokolu ap (APPLICATION-Layer Protocol) (ALPN)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="d978a-121">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="d978a-122">Připojení TLS 1.2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="d978a-122">TLS 1.2 or later connection</span></span>

<span data-ttu-id="d978a-123">&dagger;HTTP/2 bude v budoucí verzi v systému macOS podporován.</span><span class="sxs-lookup"><span data-stu-id="d978a-123">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="d978a-124">&Dagger;Kestrel má omezenou podporu pro HTTP/2 v systémech Windows Server 2012 R2 a Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="d978a-124">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="d978a-125">Podpora je omezená, protože seznam podporovaných šifrovacích sad TLS dostupných v těchto operačních systémech je omezený.</span><span class="sxs-lookup"><span data-stu-id="d978a-125">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="d978a-126">K zabezpečení připojení TLS může být vyžadován certifikát generovaný pomocí algoritmu digitálního podpisu eliptické křivky (ECDSA).</span><span class="sxs-lookup"><span data-stu-id="d978a-126">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="d978a-127">Pokud je navázáno připojení HTTP/2, `HTTP/2`protokol [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) hlásí .</span><span class="sxs-lookup"><span data-stu-id="d978a-127">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="d978a-128">Protokol HTTP/2 je ve výchozím nastavení zakázán.</span><span class="sxs-lookup"><span data-stu-id="d978a-128">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="d978a-129">Další informace o konfiguraci naleznete v části [Možnosti kestrelu](#kestrel-options) a [ListenOptions.Protocols.](#listenoptionsprotocols)</span><span class="sxs-lookup"><span data-stu-id="d978a-129">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="d978a-130">Kdy použít Kestrel s reverzním proxy serverem</span><span class="sxs-lookup"><span data-stu-id="d978a-130">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="d978a-131">Poštolka může být použita sama o sobě nebo s *reverzním proxy serverem*, jako je [Internetová informační služba (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="d978a-131">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="d978a-132">Reverzní proxy server přijímá požadavky HTTP ze sítě a předá je ke strel.</span><span class="sxs-lookup"><span data-stu-id="d978a-132">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="d978a-133">Poštolka používá jako okraj (Internet-čelí) webový server:</span><span class="sxs-lookup"><span data-stu-id="d978a-133">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Poštolka komunikuje přímo s Internetem bez reverzního proxy serveru](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="d978a-135">Poštolka použitá v konfiguraci reverzního proxy serveru:</span><span class="sxs-lookup"><span data-stu-id="d978a-135">Kestrel used in a reverse proxy configuration:</span></span>

![Poštolka komunikuje nepřímo s Internetem prostřednictvím reverzního proxy serveru, jako je služba IIS, Nginx nebo Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="d978a-137">Konfigurace, s reverzním proxy serverem nebo bez něj, je podporovaná konfigurace hostování.</span><span class="sxs-lookup"><span data-stu-id="d978a-137">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="d978a-138">Kestrel používaný jako hraniční server bez reverzního proxy serveru nepodporuje sdílení stejné IP a portu mezi více procesy.</span><span class="sxs-lookup"><span data-stu-id="d978a-138">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="d978a-139">Když kestrel je nakonfigurován pro poslech na portu, Kestrel zpracovává všechny přenosy pro tento port bez ohledu na hlavičky požadavků.When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span><span class="sxs-lookup"><span data-stu-id="d978a-139">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="d978a-140">Reverzní proxy server, který může sdílet porty, má schopnost předávat požadavky Kestrelu na jedinečné IP a portu.</span><span class="sxs-lookup"><span data-stu-id="d978a-140">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="d978a-141">I v případě, že reverzní proxy server není vyžadován, použití reverzní proxy server může být dobrou volbou.</span><span class="sxs-lookup"><span data-stu-id="d978a-141">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="d978a-142">Reverzní proxy server:</span><span class="sxs-lookup"><span data-stu-id="d978a-142">A reverse proxy:</span></span>

* <span data-ttu-id="d978a-143">Může omezit exponované veřejné plochy aplikací, které hostuje.</span><span class="sxs-lookup"><span data-stu-id="d978a-143">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="d978a-144">Poskytněte další vrstvu konfigurace a obrany.</span><span class="sxs-lookup"><span data-stu-id="d978a-144">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="d978a-145">Mohlo by se lépe integrovat se stávající infrastrukturou.</span><span class="sxs-lookup"><span data-stu-id="d978a-145">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="d978a-146">Zjednodušte vyrovnávání zatížení a zabezpečenou komunikaci (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="d978a-146">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="d978a-147">Pouze reverzní proxy server vyžaduje certifikát X.509 a tento server může komunikovat se servery aplikace v interní síti pomocí prostého protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="d978a-147">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="d978a-148">Hostování v konfiguraci reverzní proxy vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="d978a-148">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="kestrel-in-aspnet-core-apps"></a><span data-ttu-id="d978a-149">Poštolka v aplikacích ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d978a-149">Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="d978a-150">ASP.NET Šablony projektu Core používají kestrel ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="d978a-150">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="d978a-151">V *Program.cs* <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>metody :</span><span class="sxs-lookup"><span data-stu-id="d978a-151">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="d978a-152">Další informace o vytváření hostitele najdete v tématu Nastavení <xref:fundamentals/host/generic-host#set-up-a-host> *hostitele* a Výchozí nastavení *tvůrce* .</span><span class="sxs-lookup"><span data-stu-id="d978a-152">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="d978a-153">Chcete-li po `ConfigureWebHostDefaults`volání `ConfigureKestrel`poskytnout další konfiguraci , použijte :</span><span class="sxs-lookup"><span data-stu-id="d978a-153">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="d978a-154">Možnosti poštolky</span><span class="sxs-lookup"><span data-stu-id="d978a-154">Kestrel options</span></span>

<span data-ttu-id="d978a-155">Webový server Kestrel má možnosti konfigurace omezení, které jsou užitečné zejména v internetových nasazeních.</span><span class="sxs-lookup"><span data-stu-id="d978a-155">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="d978a-156">Nastavte omezení vlastnosti <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> třídy. <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions></span><span class="sxs-lookup"><span data-stu-id="d978a-156">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="d978a-157">Vlastnost `Limits` obsahuje instanci <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> třídy.</span><span class="sxs-lookup"><span data-stu-id="d978a-157">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="d978a-158">V následujících příkladech se <xref:Microsoft.AspNetCore.Server.Kestrel.Core> používá obor názvů:</span><span class="sxs-lookup"><span data-stu-id="d978a-158">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="d978a-159">V příkladech uvedených dále v tomto článku kestrel možnosti jsou konfigurovány v kódu Jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="d978a-159">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="d978a-160">Možnosti kestrelu lze také nastavit pomocí [poskytovatele konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="d978a-160">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="d978a-161">Zprostředkovatel konfigurace [souborů](xref:fundamentals/configuration/index#file-configuration-provider) může například načíst konfiguraci kestrelu z souboru *appsettings.json* nebo *appsettings.{ Prostředí}.json:*</span><span class="sxs-lookup"><span data-stu-id="d978a-161">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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
> <span data-ttu-id="d978a-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>[a konfigurace koncového bodu](#endpoint-configuration) jsou konfigurovatelné od poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="d978a-162"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](#endpoint-configuration) are configurable from configuration providers.</span></span> <span data-ttu-id="d978a-163">Zbývající kestrel konfigurace musí být nakonfigurován v kódu Jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="d978a-163">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="d978a-164">Použijte **jeden** z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="d978a-164">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="d978a-165">Konfigurovat poštolek `Startup.ConfigureServices`v :</span><span class="sxs-lookup"><span data-stu-id="d978a-165">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="d978a-166">Vstříkněte `IConfiguration` `Startup` instanci do třídy.</span><span class="sxs-lookup"><span data-stu-id="d978a-166">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="d978a-167">Následující příklad předpokládá, že vstřikovaná `Configuration` konfigurace je přiřazena vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="d978a-167">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="d978a-168">V `Startup.ConfigureServices`, `Kestrel` načtěte část konfigurace do konfigurace Kestrel:</span><span class="sxs-lookup"><span data-stu-id="d978a-168">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="d978a-169">Konfigurace kestrelu při vytváření hostitele:</span><span class="sxs-lookup"><span data-stu-id="d978a-169">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="d978a-170">V *Program.cs*načtěte `Kestrel` část konfigurace do konfigurace Kestrelu:</span><span class="sxs-lookup"><span data-stu-id="d978a-170">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="d978a-171">Oba předchozí přístupy pracují s libovolným [zprostředkovatelem konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="d978a-171">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="d978a-172">Časový limit udržování života</span><span class="sxs-lookup"><span data-stu-id="d978a-172">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="d978a-173">Získá nebo nastaví [časový limit keep-alive](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="d978a-173">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="d978a-174">Výchozí hodnota je 2 minuty.</span><span class="sxs-lookup"><span data-stu-id="d978a-174">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="d978a-175">Maximální počet připojení klientů</span><span class="sxs-lookup"><span data-stu-id="d978a-175">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="d978a-176">Maximální počet souběžných otevřených připojení TCP lze nastavit pro celou aplikaci s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="d978a-176">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="d978a-177">Existuje samostatné omezení pro připojení, která byla upgradována z protokolu HTTP nebo HTTPS na jiný protokol (například na požadavek WebSockets).</span><span class="sxs-lookup"><span data-stu-id="d978a-177">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="d978a-178">Po upgradu připojení se nezapočítává `MaxConcurrentConnections` do limitu.</span><span class="sxs-lookup"><span data-stu-id="d978a-178">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="d978a-179">Maximální počet připojení je ve výchozím nastavení neomezený (null).</span><span class="sxs-lookup"><span data-stu-id="d978a-179">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="d978a-180">Maximální velikost těla požadavku</span><span class="sxs-lookup"><span data-stu-id="d978a-180">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="d978a-181">Výchozí maximální velikost těla požadavku je 30 000 000 bajtů, což je přibližně 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="d978a-181">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="d978a-182">Doporučený přístup k přepsání limitu v aplikaci ASP.NET <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> Core MVC je použití atributu na metodě akce:</span><span class="sxs-lookup"><span data-stu-id="d978a-182">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="d978a-183">Tady je příklad, který ukazuje, jak nakonfigurovat omezení pro aplikaci při každém požadavku:</span><span class="sxs-lookup"><span data-stu-id="d978a-183">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="d978a-184">Přepište nastavení na konkrétní požadavek v middlewaru:</span><span class="sxs-lookup"><span data-stu-id="d978a-184">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="d978a-185">Výjimka je vyvolána, pokud aplikace konfiguruje limit na požadavek poté, co aplikace začala číst požadavek.</span><span class="sxs-lookup"><span data-stu-id="d978a-185">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="d978a-186">Je `IsReadOnly` vlastnost, která označuje, `MaxRequestBodySize` pokud je vlastnost ve stavu jen pro čtení, což znamená, že je příliš pozdě na konfiguraci limitu.</span><span class="sxs-lookup"><span data-stu-id="d978a-186">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="d978a-187">Pokud je aplikace [spuštěna mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za ASP.NET [základnímodul](xref:host-and-deploy/aspnet-core-module), kestrel požadavek body limit je zakázán, protože iis již nastaví limit.</span><span class="sxs-lookup"><span data-stu-id="d978a-187">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="d978a-188">Minimální přenosová rychlost tělo požadavku</span><span class="sxs-lookup"><span data-stu-id="d978a-188">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="d978a-189">Poštolka kontroluje každou sekundu, pokud data přicházejí na zadanou rychlost v bajtů za sekundu.</span><span class="sxs-lookup"><span data-stu-id="d978a-189">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="d978a-190">Pokud rychlost klesne pod minimum, je časový limit připojení. Doba odkladu je doba, kterou Kestrel dává klientovi ke zvýšení míry odeslání až na minimum; během této doby není sazba kontrolována.</span><span class="sxs-lookup"><span data-stu-id="d978a-190">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="d978a-191">Období odkladu pomáhá zabránit přetažení připojení, které jsou zpočátku odesílání dat pomalým tempem z důvodu tcp pomalé spuštění.</span><span class="sxs-lookup"><span data-stu-id="d978a-191">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="d978a-192">Výchozí minimální sazba je 240 bajtů za sekundu s 5 sekundovou lhůtou odkladu.</span><span class="sxs-lookup"><span data-stu-id="d978a-192">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="d978a-193">Minimální sazba se vztahuje také na odpověď.</span><span class="sxs-lookup"><span data-stu-id="d978a-193">A minimum rate also applies to the response.</span></span> <span data-ttu-id="d978a-194">Kód pro nastavení limitu požadavku a limitu odpovědi `RequestBody` `Response` je stejný s výjimkou s nebo v názvy vlastností a rozhraní.</span><span class="sxs-lookup"><span data-stu-id="d978a-194">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="d978a-195">Zde je příklad, který ukazuje, jak nakonfigurovat minimální rychlost dat v *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="d978a-195">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="d978a-196">Přepsat minimální limity rychlosti na požadavek v middlewaru:</span><span class="sxs-lookup"><span data-stu-id="d978a-196">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="d978a-197">Odkazováno <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> v předchozím vzorku není `HttpContext.Features` k dispozici v pro požadavky HTTP/2, protože změna omezení rychlosti na základě požadavku není obecně podporována pro HTTP/2 z důvodu podpory protokolu pro multiplexování požadavků.</span><span class="sxs-lookup"><span data-stu-id="d978a-197">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="d978a-198">Je <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> však stále `HttpContext.Features` k dispozici pro požadavky HTTP/2, protože limit rychlosti čtení lze stále `IHttpMinRequestBodyDataRateFeature.MinDataRate` `null` *zcela zakázat* na základě požadavku nastavením i pro požadavek HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="d978a-198">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="d978a-199">Pokus o `IHttpMinRequestBodyDataRateFeature.MinDataRate` čtení nebo pokus u jeho nastavení `null` na jinou hodnotu bude mít za následek `NotSupportedException` vyvolání daného požadavku HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="d978a-199">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="d978a-200">Limity rychlosti na `KestrelServerOptions.Limits` celém serveru nakonfigurované prostřednictvím připojení HTTP/1.x i HTTP/2 stále platí.</span><span class="sxs-lookup"><span data-stu-id="d978a-200">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="d978a-201">Časový výtažek záhlaví požadavku</span><span class="sxs-lookup"><span data-stu-id="d978a-201">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="d978a-202">Získá nebo nastaví maximální množství času, které server stráví příjem hlavičky požadavku.</span><span class="sxs-lookup"><span data-stu-id="d978a-202">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="d978a-203">Výchozí hodnota je 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="d978a-203">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="d978a-204">Maximální počet datových proudů na připojení</span><span class="sxs-lookup"><span data-stu-id="d978a-204">Maximum streams per connection</span></span>

<span data-ttu-id="d978a-205">`Http2.MaxStreamsPerConnection`omezuje počet souběžných datových proudů požadavků na připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="d978a-205">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="d978a-206">Přebytečné proudy jsou odmítnuty.</span><span class="sxs-lookup"><span data-stu-id="d978a-206">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="d978a-207">Výchozí hodnota je 100.</span><span class="sxs-lookup"><span data-stu-id="d978a-207">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="d978a-208">Velikost tabulky záhlaví</span><span class="sxs-lookup"><span data-stu-id="d978a-208">Header table size</span></span>

<span data-ttu-id="d978a-209">Dekodér HPACK dekomprimuje hlavičky HTTP pro připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="d978a-209">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="d978a-210">`Http2.HeaderTableSize`omezuje velikost tabulky komprese záhlaví, kterou dekodér HPACK používá.</span><span class="sxs-lookup"><span data-stu-id="d978a-210">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="d978a-211">Hodnota je uvedena v oktetech a musí být větší než nula (0).</span><span class="sxs-lookup"><span data-stu-id="d978a-211">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="d978a-212">Výchozí hodnota je 4096.</span><span class="sxs-lookup"><span data-stu-id="d978a-212">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="d978a-213">Maximální velikost rámu</span><span class="sxs-lookup"><span data-stu-id="d978a-213">Maximum frame size</span></span>

<span data-ttu-id="d978a-214">`Http2.MaxFrameSize`označuje maximální povolenou velikost datové části rámce připojení HTTP/2 přijaté nebo odeslané serverem.</span><span class="sxs-lookup"><span data-stu-id="d978a-214">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="d978a-215">Hodnota je uvedena v oktetech a musí být mezi 2^14 (16 384) a 2^24-1 (16 777 215).</span><span class="sxs-lookup"><span data-stu-id="d978a-215">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="d978a-216">Výchozí hodnota je 2^14 (16 384).</span><span class="sxs-lookup"><span data-stu-id="d978a-216">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="d978a-217">Maximální velikost hlavičky požadavku</span><span class="sxs-lookup"><span data-stu-id="d978a-217">Maximum request header size</span></span>

<span data-ttu-id="d978a-218">`Http2.MaxRequestHeaderFieldSize`označuje maximální povolenou velikost v oktetech hodnot hlaviček požadavku.</span><span class="sxs-lookup"><span data-stu-id="d978a-218">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="d978a-219">Toto omezení platí pro název i hodnotu v jejich komprimovaných a nekomprimovaných reprezentacích.</span><span class="sxs-lookup"><span data-stu-id="d978a-219">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="d978a-220">Hodnota musí být větší než nula (0).</span><span class="sxs-lookup"><span data-stu-id="d978a-220">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="d978a-221">Výchozí hodnota je 8 192.</span><span class="sxs-lookup"><span data-stu-id="d978a-221">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="d978a-222">Počáteční velikost okna připojení</span><span class="sxs-lookup"><span data-stu-id="d978a-222">Initial connection window size</span></span>

<span data-ttu-id="d978a-223">`Http2.InitialConnectionWindowSize`označuje maximální data těla požadavků v bajtech vyrovnávacích pamětí serveru najednou agregované ve všech požadavcích (datových proudech) na připojení.</span><span class="sxs-lookup"><span data-stu-id="d978a-223">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="d978a-224">Žádosti jsou také `Http2.InitialStreamWindowSize`omezeny .</span><span class="sxs-lookup"><span data-stu-id="d978a-224">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="d978a-225">Hodnota musí být větší nebo rovna 65 535 a menší než 2^31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="d978a-225">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="d978a-226">Výchozí hodnota je 128 kB (131 072).</span><span class="sxs-lookup"><span data-stu-id="d978a-226">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="d978a-227">Počáteční velikost okna datového proudu</span><span class="sxs-lookup"><span data-stu-id="d978a-227">Initial stream window size</span></span>

<span data-ttu-id="d978a-228">`Http2.InitialStreamWindowSize`označuje maximální data těla požadavku v bajtů vyrovnávacích pamětí serveru najednou na jeden požadavek (datový proud).</span><span class="sxs-lookup"><span data-stu-id="d978a-228">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="d978a-229">Žádosti jsou také `Http2.InitialConnectionWindowSize`omezeny .</span><span class="sxs-lookup"><span data-stu-id="d978a-229">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="d978a-230">Hodnota musí být větší nebo rovna 65 535 a menší než 2^31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="d978a-230">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="d978a-231">Výchozí hodnota je 96 KB (98 304).</span><span class="sxs-lookup"><span data-stu-id="d978a-231">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="d978a-232">Synchronní vstupně-výstupní operace</span><span class="sxs-lookup"><span data-stu-id="d978a-232">Synchronous I/O</span></span>

<span data-ttu-id="d978a-233"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>určuje, zda je pro požadavek a odpověď povolena synchronní vstupně-va.</span><span class="sxs-lookup"><span data-stu-id="d978a-233"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="d978a-234">Výchozí hodnota je `false`.</span><span class="sxs-lookup"><span data-stu-id="d978a-234">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="d978a-235">Velký počet blokování synchronní vstupně-va operace může vést k nedostatku fondu vláken, což způsobí, že aplikace nereaguje.</span><span class="sxs-lookup"><span data-stu-id="d978a-235">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="d978a-236">Povolit `AllowSynchronousIO` pouze při použití knihovny, která nepodporuje asynchronní vstupně-v.A/O.</span><span class="sxs-lookup"><span data-stu-id="d978a-236">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="d978a-237">Následující příklad umožňuje synchronní vstupně-va:</span><span class="sxs-lookup"><span data-stu-id="d978a-237">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="d978a-238">Informace o dalších možnostech a limitech kestrelu naleznete v těchto tématech:</span><span class="sxs-lookup"><span data-stu-id="d978a-238">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="d978a-239">Konfigurace koncového bodu</span><span class="sxs-lookup"><span data-stu-id="d978a-239">Endpoint configuration</span></span>

<span data-ttu-id="d978a-240">Ve výchozím nastavení se ASP.NET jádro váže na:</span><span class="sxs-lookup"><span data-stu-id="d978a-240">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="d978a-241">`https://localhost:5001`(pokud je k dispozici certifikát místního rozvoje)</span><span class="sxs-lookup"><span data-stu-id="d978a-241">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="d978a-242">Zadejte adresy URL pomocí:</span><span class="sxs-lookup"><span data-stu-id="d978a-242">Specify URLs using the:</span></span>

* <span data-ttu-id="d978a-243">`ASPNETCORE_URLS`proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="d978a-243">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="d978a-244">`--urls`argument příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="d978a-244">`--urls` command-line argument.</span></span>
* <span data-ttu-id="d978a-245">`urls`konfiguračního klíče hostitele.</span><span class="sxs-lookup"><span data-stu-id="d978a-245">`urls` host configuration key.</span></span>
* <span data-ttu-id="d978a-246">`UseUrls`rozšíření.</span><span class="sxs-lookup"><span data-stu-id="d978a-246">`UseUrls` extension method.</span></span>

<span data-ttu-id="d978a-247">Hodnota poskytovaná pomocí těchto přístupů může být jeden nebo více koncových bodů HTTP a HTTPS (HTTPS, pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="d978a-247">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="d978a-248">Nakonfigurujte hodnotu jako seznam oddělený `"Urls": "http://localhost:8000;http://localhost:8001"`středníkem (například).</span><span class="sxs-lookup"><span data-stu-id="d978a-248">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="d978a-249">Další informace o těchto přístupech naleznete v [tématu Adresy URL serveru](xref:fundamentals/host/web-host#server-urls) a [konfigurace override](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="d978a-249">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="d978a-250">Je vytvořen vývojový certifikát:</span><span class="sxs-lookup"><span data-stu-id="d978a-250">A development certificate is created:</span></span>

* <span data-ttu-id="d978a-251">Při instalaci sady [.NET Core SDK.](/dotnet/core/sdk)</span><span class="sxs-lookup"><span data-stu-id="d978a-251">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="d978a-252">Nástroj [dev-certs](xref:aspnetcore-2.1#https) se používá k vytvoření certifikátu.</span><span class="sxs-lookup"><span data-stu-id="d978a-252">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="d978a-253">Některé prohlížeče vyžadují udělení výslovného oprávnění důvěřovat certifikátu místního vývoje.</span><span class="sxs-lookup"><span data-stu-id="d978a-253">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="d978a-254">Šablony projektů konfigurují aplikace tak, aby se ve výchozím nastavení spouštěly na protokolu HTTPS a zahrnovaly [přesměrování HTTPS a podporu HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="d978a-254">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="d978a-255">Volání <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> nebo <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> metody na konfiguraci URL předpony a porty pro Kestrel.</span><span class="sxs-lookup"><span data-stu-id="d978a-255">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="d978a-256">`UseUrls`, `--urls` argument příkazového `urls` řádku, konfigurační klíč hostitele a proměnná `ASPNETCORE_URLS` prostředí také fungují, ale omezení jsou zaznamenána dále v této části (výchozí certifikát musí být k dispozici pro konfiguraci koncového bodu HTTPS).</span><span class="sxs-lookup"><span data-stu-id="d978a-256">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="d978a-257">`KestrelServerOptions`Konfigurace:</span><span class="sxs-lookup"><span data-stu-id="d978a-257">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="d978a-258">ConfigureEndpointDefaults (akce\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="d978a-258">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="d978a-259">Určuje konfiguraci, `Action` která má být spuštěna pro každý zadaný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="d978a-259">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="d978a-260">Volání `ConfigureEndpointDefaults` vícekrát nahradí `Action`předchozí s `Action` posledním zadaným.</span><span class="sxs-lookup"><span data-stu-id="d978a-260">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="d978a-261">Koncové body vytvořené <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> voláním **před** voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> nebudou mít výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="d978a-261">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="d978a-262">KonfigurovatHttpsDefaults (akce\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="d978a-262">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="d978a-263">Určuje konfiguraci, `Action` která má být spuštěna pro každý koncový bod HTTPS.</span><span class="sxs-lookup"><span data-stu-id="d978a-263">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="d978a-264">Volání `ConfigureHttpsDefaults` vícekrát nahradí `Action`předchozí s `Action` posledním zadaným.</span><span class="sxs-lookup"><span data-stu-id="d978a-264">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="d978a-265">Koncové body vytvořené <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> voláním **před** voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> nebudou mít výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="d978a-265">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="d978a-266">Configure(IKonfigurace)</span><span class="sxs-lookup"><span data-stu-id="d978a-266">Configure(IConfiguration)</span></span>

<span data-ttu-id="d978a-267">Vytvoří konfigurační zavaděč pro <xref:Microsoft.Extensions.Configuration.IConfiguration> nastavení Kestrel, který bere jako vstup.</span><span class="sxs-lookup"><span data-stu-id="d978a-267">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="d978a-268">Konfigurace musí být vymezena na konfigurační sekci pro Kestrel.</span><span class="sxs-lookup"><span data-stu-id="d978a-268">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="d978a-269">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="d978a-269">ListenOptions.UseHttps</span></span>

<span data-ttu-id="d978a-270">Nakonfigurujte Kestrel pro použití protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="d978a-270">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="d978a-271">`ListenOptions.UseHttps`Rozšíření:</span><span class="sxs-lookup"><span data-stu-id="d978a-271">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="d978a-272">`UseHttps`&ndash; Nakonfigurujte Kestrel tak, aby používal protokol HTTPS s výchozím certifikátem.</span><span class="sxs-lookup"><span data-stu-id="d978a-272">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="d978a-273">Vyvolá výjimku, pokud není nakonfigurován žádný výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="d978a-273">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="d978a-274">`ListenOptions.UseHttps`Parametry:</span><span class="sxs-lookup"><span data-stu-id="d978a-274">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="d978a-275">`filename`je cesta a název souboru certifikátu vzhledem k adresáři, který obsahuje soubory obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="d978a-275">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="d978a-276">`password`je heslo potřebné pro přístup k datům certifikátu X.509.</span><span class="sxs-lookup"><span data-stu-id="d978a-276">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="d978a-277">`configureOptions`je `Action` konfigurace rozhraní `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="d978a-277">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="d978a-278">Vrátí `ListenOptions`hodnotu .</span><span class="sxs-lookup"><span data-stu-id="d978a-278">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="d978a-279">`storeName`je úložiště certifikátů, ze kterého se má certifikát načíst.</span><span class="sxs-lookup"><span data-stu-id="d978a-279">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="d978a-280">`subject`je název subjektu certifikátu.</span><span class="sxs-lookup"><span data-stu-id="d978a-280">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="d978a-281">`allowInvalid`označuje, zda by měly být považovány za neplatné certifikáty, například certifikáty podepsané svým držitelem.</span><span class="sxs-lookup"><span data-stu-id="d978a-281">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="d978a-282">`location`je umístění úložiště, ze kterých se má certifikát načíst.</span><span class="sxs-lookup"><span data-stu-id="d978a-282">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="d978a-283">`serverCertificate`je certifikát X.509.</span><span class="sxs-lookup"><span data-stu-id="d978a-283">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="d978a-284">V produkčním prostředí musí být protokol HTTPS explicitně nakonfigurován.</span><span class="sxs-lookup"><span data-stu-id="d978a-284">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="d978a-285">Musí být poskytnut minimálně výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="d978a-285">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="d978a-286">Podporované konfigurace popsané dále:</span><span class="sxs-lookup"><span data-stu-id="d978a-286">Supported configurations described next:</span></span>

* <span data-ttu-id="d978a-287">Žádná konfigurace</span><span class="sxs-lookup"><span data-stu-id="d978a-287">No configuration</span></span>
* <span data-ttu-id="d978a-288">Nahrazení výchozího certifikátu z konfigurace</span><span class="sxs-lookup"><span data-stu-id="d978a-288">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="d978a-289">Změna výchozích hodnot v kódu</span><span class="sxs-lookup"><span data-stu-id="d978a-289">Change the defaults in code</span></span>

<span data-ttu-id="d978a-290">*Žádná konfigurace*</span><span class="sxs-lookup"><span data-stu-id="d978a-290">*No configuration*</span></span>

<span data-ttu-id="d978a-291">Poštolka naslouchá `http://localhost:5000` dál `https://localhost:5001` a (pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="d978a-291">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="d978a-292">*Nahrazení výchozího certifikátu z konfigurace*</span><span class="sxs-lookup"><span data-stu-id="d978a-292">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="d978a-293">`CreateDefaultBuilder`ve `Configure(context.Configuration.GetSection("Kestrel"))` výchozím nastavení načítá konfiguraci Kestrel.</span><span class="sxs-lookup"><span data-stu-id="d978a-293">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="d978a-294">Pro Kestrel je k dispozici výchozí schéma konfigurace nastavení aplikace HTTPS.</span><span class="sxs-lookup"><span data-stu-id="d978a-294">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="d978a-295">Nakonfigurujte více koncových bodů, včetně adres URL a certifikátů, které mají být používány, ze souboru na disku nebo z úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="d978a-295">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="d978a-296">V následujícím příkladu *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="d978a-296">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="d978a-297">Nastavte **AllowInvalid** na `true` povolení použití neplatných certifikátů (například certifikátů podepsaných svým držitelem).</span><span class="sxs-lookup"><span data-stu-id="d978a-297">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="d978a-298">Libovolný koncový bod HTTPS, který neurčuje certifikát (**HttpsDefaultCert** v následujícím příkladu) přejde zpět na certifikát definovaný v části **Výchozí** **certifikáty** > nebo vývojový certifikát.</span><span class="sxs-lookup"><span data-stu-id="d978a-298">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="d978a-299">Alternativou k použití **cesty** a **hesla** pro libovolný uzel certifikátu je určení certifikátu pomocí polí úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="d978a-299">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="d978a-300">**Například certifikát výchozí** certifikát **y** > lze zadat jako:</span><span class="sxs-lookup"><span data-stu-id="d978a-300">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="d978a-301">Poznámky schématu:</span><span class="sxs-lookup"><span data-stu-id="d978a-301">Schema notes:</span></span>

* <span data-ttu-id="d978a-302">Názvy koncových bodů nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="d978a-302">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="d978a-303">Například `HTTPS` a `Https` jsou platné.</span><span class="sxs-lookup"><span data-stu-id="d978a-303">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="d978a-304">Parametr `Url` je vyžadován pro každý koncový bod.</span><span class="sxs-lookup"><span data-stu-id="d978a-304">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="d978a-305">Formát tohoto parametru je stejný jako `Urls` parametr konfigurace nejvyšší úrovně s tím rozdílem, že je omezen na jednu hodnotu.</span><span class="sxs-lookup"><span data-stu-id="d978a-305">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="d978a-306">Tyto koncové body nahradit ty definované `Urls` v konfiguraci nejvyšší úrovně, nikoli jejich přidání.</span><span class="sxs-lookup"><span data-stu-id="d978a-306">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="d978a-307">Koncové body definované `Listen` v kódu prostřednictvím jsou kumulativní s koncovými body definovanými v konfigurační části.</span><span class="sxs-lookup"><span data-stu-id="d978a-307">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="d978a-308">Sekce `Certificate` je nepovinná.</span><span class="sxs-lookup"><span data-stu-id="d978a-308">The `Certificate` section is optional.</span></span> <span data-ttu-id="d978a-309">Pokud `Certificate` oddíl není zadán, použijí se výchozí hodnoty definované v předchozích scénářích.</span><span class="sxs-lookup"><span data-stu-id="d978a-309">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="d978a-310">Pokud nejsou k dispozici žádné výchozí hodnoty, server vyvolá výjimku a nepodaří se spustit.</span><span class="sxs-lookup"><span data-stu-id="d978a-310">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="d978a-311">V `Certificate` této části jsou certifikáty**hesel** **cesty**&ndash;i**úložiště** **předmětů.**&ndash;</span><span class="sxs-lookup"><span data-stu-id="d978a-311">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="d978a-312">Tímto způsobem může být definován libovolný počet koncových bodů, pokud nezpůsobí konflikty portů.</span><span class="sxs-lookup"><span data-stu-id="d978a-312">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="d978a-313">`options.Configure(context.Configuration.GetSection("{SECTION}"))`vrátí `KestrelConfigurationLoader` metodu `.Endpoint(string name, listenOptions => { })` a s metodou, kterou lze použít k doplnění nastavení nakonfigurovaného koncového bodu:</span><span class="sxs-lookup"><span data-stu-id="d978a-313">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="d978a-314">`KestrelServerOptions.ConfigurationLoader`lze přímo přistupovat k pokračování iterace na stávajícím nakladači, jako je například ten, který <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>poskytuje .</span><span class="sxs-lookup"><span data-stu-id="d978a-314">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="d978a-315">Konfigurační oddíl pro každý koncový bod `Endpoint` je k dispozici na možnosti v metodě tak, aby vlastní nastavení může být číst.</span><span class="sxs-lookup"><span data-stu-id="d978a-315">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="d978a-316">Více konfigurací může být `options.Configure(context.Configuration.GetSection("{SECTION}"))` načteno voláním znovu s jiným oddílem.</span><span class="sxs-lookup"><span data-stu-id="d978a-316">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="d978a-317">Používá se pouze poslední `Load` konfigurace, pokud není explicitně volána na předchozí instance.</span><span class="sxs-lookup"><span data-stu-id="d978a-317">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="d978a-318">Metabalíček nevolá, `Load` takže jeho výchozí konfigurační část může být nahrazena.</span><span class="sxs-lookup"><span data-stu-id="d978a-318">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="d978a-319">`KestrelConfigurationLoader`zrcadlí `Listen` rodinu API z `KestrelServerOptions` `Endpoint` jako přetížení, takže kód a koncové body konfigurace mohou být nakonfigurovány na stejném místě.</span><span class="sxs-lookup"><span data-stu-id="d978a-319">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="d978a-320">Tato přetížení nepoužívají názvy a spotřebovávají pouze výchozí nastavení z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="d978a-320">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="d978a-321">*Změna výchozích hodnot v kódu*</span><span class="sxs-lookup"><span data-stu-id="d978a-321">*Change the defaults in code*</span></span>

<span data-ttu-id="d978a-322">`ConfigureEndpointDefaults`a `ConfigureHttpsDefaults` lze použít ke změně `ListenOptions` `HttpsConnectionAdapterOptions`výchozího nastavení pro a , včetně přepsání výchozího certifikátu zadaného v předchozím scénáři.</span><span class="sxs-lookup"><span data-stu-id="d978a-322">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="d978a-323">`ConfigureEndpointDefaults`a `ConfigureHttpsDefaults` měla by být volána před konfigurací všech koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="d978a-323">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="d978a-324">*Podpora pro Kestrel pro SNI*</span><span class="sxs-lookup"><span data-stu-id="d978a-324">*Kestrel support for SNI*</span></span>

<span data-ttu-id="d978a-325">[Označení názvu serveru (SNI)](https://tools.ietf.org/html/rfc6066#section-3) lze použít k hostování více domén na stejné adrese IP a portu.</span><span class="sxs-lookup"><span data-stu-id="d978a-325">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="d978a-326">Aby sni fungovalo, klient odešle název hostitele pro zabezpečenou relaci na server během tls handshake tak, aby server mohl poskytnout správný certifikát.</span><span class="sxs-lookup"><span data-stu-id="d978a-326">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="d978a-327">Klient používá zařízený certifikát pro šifrovanou komunikaci se serverem během zabezpečené relace, která následuje po handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="d978a-327">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="d978a-328">Kestrel podporuje SNI `ServerCertificateSelector` prostřednictvím zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="d978a-328">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="d978a-329">Zpětné volání je vyvoláno jednou za připojení, aby aplikace mohla zkontrolovat název hostitele a vybrat příslušný certifikát.</span><span class="sxs-lookup"><span data-stu-id="d978a-329">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="d978a-330">Podpora SNI vyžaduje:</span><span class="sxs-lookup"><span data-stu-id="d978a-330">SNI support requires:</span></span>

* <span data-ttu-id="d978a-331">Spuštění na `netcoreapp2.1` cílové rozhraní nebo později.</span><span class="sxs-lookup"><span data-stu-id="d978a-331">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="d978a-332">Na `net461` nebo novější zpětné volání je `name` vyvolána, ale je vždy `null`.</span><span class="sxs-lookup"><span data-stu-id="d978a-332">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="d978a-333">Je `name` také `null` v případě, že klient neposkytuje parametr název hostitele v tls handshake.</span><span class="sxs-lookup"><span data-stu-id="d978a-333">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="d978a-334">Všechny webové stránky běží na stejné instanci Kestrel.</span><span class="sxs-lookup"><span data-stu-id="d978a-334">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="d978a-335">Kestrel nepodporuje sdílení IP adresy a portu ve více instancích bez reverzního proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="d978a-335">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="d978a-336">Protokolování připojení</span><span class="sxs-lookup"><span data-stu-id="d978a-336">Connection logging</span></span>

<span data-ttu-id="d978a-337">Volání <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> vyzařovat protokoly úrovně ladění pro komunikaci na úrovni bajtů na připojení.</span><span class="sxs-lookup"><span data-stu-id="d978a-337">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="d978a-338">Protokolování připojení je užitečné při řešení problémů v nízkoúrovňové komunikaci, například během šifrování TLS a za proxy servery.</span><span class="sxs-lookup"><span data-stu-id="d978a-338">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="d978a-339">Pokud `UseConnectionLogging` je `UseHttps`umístěn před , šifrovaný provoz je zaznamenána.</span><span class="sxs-lookup"><span data-stu-id="d978a-339">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="d978a-340">Pokud `UseConnectionLogging` je `UseHttps`umístěn po , dešifrovaný provoz je zaznamenána.</span><span class="sxs-lookup"><span data-stu-id="d978a-340">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="d978a-341">Vazba na soket TCP</span><span class="sxs-lookup"><span data-stu-id="d978a-341">Bind to a TCP socket</span></span>

<span data-ttu-id="d978a-342">Metoda <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> se váže na soket TCP a možnosti lambda umožňuje konfiguraci certifikátu X.509:</span><span class="sxs-lookup"><span data-stu-id="d978a-342">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="d978a-343">Příklad konfiguruje protokol <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>HTTPS pro koncový bod s aplikací .</span><span class="sxs-lookup"><span data-stu-id="d978a-343">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="d978a-344">Stejné rozhraní API použijte ke konfiguraci dalších nastavení kestrelu pro konkrétní koncové body.</span><span class="sxs-lookup"><span data-stu-id="d978a-344">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="d978a-345">Vazba na unixovou zásuvku</span><span class="sxs-lookup"><span data-stu-id="d978a-345">Bind to a Unix socket</span></span>

<span data-ttu-id="d978a-346">Poslouchejte na unixovém soketu s <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> pro lepší výkon s Nginx, jak je znázorněno v tomto příkladu:</span><span class="sxs-lookup"><span data-stu-id="d978a-346">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="d978a-347">V konfiguračním souboru `server`  >  `location`  >  `proxy_pass` Nginx nastavte položku na `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span><span class="sxs-lookup"><span data-stu-id="d978a-347">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="d978a-348">`{KESTREL SOCKET}`je název soketu k <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> dispozici (například `kestrel-test.sock` v předchozím příkladu).</span><span class="sxs-lookup"><span data-stu-id="d978a-348">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="d978a-349">Ujistěte se, že soket je zapisovatelný Nginx `chmod go+w /tmp/kestrel-test.sock`(například).</span><span class="sxs-lookup"><span data-stu-id="d978a-349">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

### <a name="port-0"></a><span data-ttu-id="d978a-350">Port 0</span><span class="sxs-lookup"><span data-stu-id="d978a-350">Port 0</span></span>

<span data-ttu-id="d978a-351">Je-li `0` zadáno číslo portu, kestrel dynamicky se váže na dostupný port.</span><span class="sxs-lookup"><span data-stu-id="d978a-351">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="d978a-352">Následující příklad ukazuje, jak určit, který port Kestrel skutečně vázán za běhu:</span><span class="sxs-lookup"><span data-stu-id="d978a-352">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="d978a-353">Při spuštění aplikace výstup okna konzoly označuje dynamický port, kde lze aplikaci dosáhnout:</span><span class="sxs-lookup"><span data-stu-id="d978a-353">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="d978a-354">Omezení</span><span class="sxs-lookup"><span data-stu-id="d978a-354">Limitations</span></span>

<span data-ttu-id="d978a-355">Nakonfigurujte koncové body pomocí následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="d978a-355">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="d978a-356">`--urls`argument příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="d978a-356">`--urls` command-line argument</span></span>
* <span data-ttu-id="d978a-357">`urls`konfigurační klíč hostitele</span><span class="sxs-lookup"><span data-stu-id="d978a-357">`urls` host configuration key</span></span>
* <span data-ttu-id="d978a-358">`ASPNETCORE_URLS`proměnná prostředí</span><span class="sxs-lookup"><span data-stu-id="d978a-358">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="d978a-359">Tyto metody jsou užitečné pro práci s kódem s jinými servery než Kestrel.</span><span class="sxs-lookup"><span data-stu-id="d978a-359">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="d978a-360">Mějte však na paměti následující omezení:</span><span class="sxs-lookup"><span data-stu-id="d978a-360">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="d978a-361">Protokol HTTPS nelze použít s těmito přístupy, pokud výchozí certifikát je k `KestrelServerOptions` dispozici v konfiguraci koncového bodu HTTPS (například pomocí konfigurace nebo konfiguračního souboru, jak je uvedeno výše v tomto tématu).</span><span class="sxs-lookup"><span data-stu-id="d978a-361">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="d978a-362">Při obou `Listen` `UseUrls` a přístupy jsou `Listen` používány současně, `UseUrls` koncové body přepsat koncové body.</span><span class="sxs-lookup"><span data-stu-id="d978a-362">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="d978a-363">Konfigurace koncového bodu služby IIS</span><span class="sxs-lookup"><span data-stu-id="d978a-363">IIS endpoint configuration</span></span>

<span data-ttu-id="d978a-364">Při použití služby IIS jsou vazby adres URL pro `Listen` `UseUrls`vazby přepsání služby IIS nastaveny buď nebo .</span><span class="sxs-lookup"><span data-stu-id="d978a-364">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="d978a-365">Další informace naleznete v [tématu ASP.NET základní modul.](xref:host-and-deploy/aspnet-core-module)</span><span class="sxs-lookup"><span data-stu-id="d978a-365">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="d978a-366">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="d978a-366">ListenOptions.Protocols</span></span>

<span data-ttu-id="d978a-367">Vlastnost `Protocols` vytvoří protokoly HTTP`HttpProtocols`( ) povolené na koncovém bodu připojení nebo pro server.</span><span class="sxs-lookup"><span data-stu-id="d978a-367">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="d978a-368">Přiřaďte `Protocols` hodnotu `HttpProtocols` vlastnosti z výčtu.</span><span class="sxs-lookup"><span data-stu-id="d978a-368">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="d978a-369">`HttpProtocols`Hodnota výčtu</span><span class="sxs-lookup"><span data-stu-id="d978a-369">`HttpProtocols` enum value</span></span> | <span data-ttu-id="d978a-370">Protokol připojení povolen</span><span class="sxs-lookup"><span data-stu-id="d978a-370">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="d978a-371">pouze http/1.1.</span><span class="sxs-lookup"><span data-stu-id="d978a-371">HTTP/1.1 only.</span></span> <span data-ttu-id="d978a-372">Lze použít s TLS nebo bez něj.</span><span class="sxs-lookup"><span data-stu-id="d978a-372">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="d978a-373">pouze http/2.</span><span class="sxs-lookup"><span data-stu-id="d978a-373">HTTP/2 only.</span></span> <span data-ttu-id="d978a-374">Bez TLS lze použít pouze v případě, že klient podporuje [režim předchozí znalosti](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="d978a-374">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="d978a-375">HTTP/1.1 a HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="d978a-375">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="d978a-376">Protokol HTTP/2 vyžaduje, aby klient v příkazu handshake (TLS [Application-Layer Protocol)](https://tools.ietf.org/html/rfc7301#section-3) vybral protokol HTTP/2. v opačném případě je výchozí připojení http/1.1.</span><span class="sxs-lookup"><span data-stu-id="d978a-376">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="d978a-377">Výchozí `ListenOptions.Protocols` hodnota pro libovolný `HttpProtocols.Http1AndHttp2`koncový bod je .</span><span class="sxs-lookup"><span data-stu-id="d978a-377">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="d978a-378">Omezení TLS pro HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="d978a-378">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="d978a-379">TLS verze 1.2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="d978a-379">TLS version 1.2 or later</span></span>
* <span data-ttu-id="d978a-380">Opětovné vyjednávání zakázáno.</span><span class="sxs-lookup"><span data-stu-id="d978a-380">Renegotiation disabled</span></span>
* <span data-ttu-id="d978a-381">Komprese zakázána</span><span class="sxs-lookup"><span data-stu-id="d978a-381">Compression disabled</span></span>
* <span data-ttu-id="d978a-382">Minimální velikosti dočasných klíčů:</span><span class="sxs-lookup"><span data-stu-id="d978a-382">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="d978a-383">Eliptická křivka Diffie-Hellman &lbrack;(ECDHE) [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; &ndash; 224 bitů minimum</span><span class="sxs-lookup"><span data-stu-id="d978a-383">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum</span></span>
  * <span data-ttu-id="d978a-384">Konečné pole Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; &ndash; minimálně 2048 bitů</span><span class="sxs-lookup"><span data-stu-id="d978a-384">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bits minimum</span></span>
* <span data-ttu-id="d978a-385">Šifrovací sada není na černé listině</span><span class="sxs-lookup"><span data-stu-id="d978a-385">Cipher suite not blacklisted</span></span>

<span data-ttu-id="d978a-386">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack; &lbrack; `FIPS186` &rbrack; s eliptickou křivkou P-256 je ve výchozím nastavení podporována. `TLS-ECDHE` &rbrack;</span><span class="sxs-lookup"><span data-stu-id="d978a-386">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="d978a-387">Následující příklad umožňuje připojení HTTP/1.1 a HTTP/2 na portu 8000.</span><span class="sxs-lookup"><span data-stu-id="d978a-387">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="d978a-388">Připojení jsou zabezpečena TLS s dodaným certifikátem:</span><span class="sxs-lookup"><span data-stu-id="d978a-388">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="d978a-389">Pomocí middlewaru připojení můžete v případě potřeby filtrovat tls handshakes na základě jednotlivých připojení pro konkrétní šifry.</span><span class="sxs-lookup"><span data-stu-id="d978a-389">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="d978a-390">Následující příklad vyvolá <xref:System.NotSupportedException> jakýkoli algoritmus šifry, který aplikace nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="d978a-390">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="d978a-391">Alternativně definujte a porovnejte [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) se seznamem přijatelných šifrovacích sad.</span><span class="sxs-lookup"><span data-stu-id="d978a-391">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="d978a-392">S šifrovacím algoritmem [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) se nepoužívá žádné šifrování.</span><span class="sxs-lookup"><span data-stu-id="d978a-392">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

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

<span data-ttu-id="d978a-393">Filtrování připojení lze také nakonfigurovat pomocí <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span><span class="sxs-lookup"><span data-stu-id="d978a-393">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

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

<span data-ttu-id="d978a-394">Na Linuxu, <xref:System.Net.Security.CipherSuitesPolicy> lze filtrovat TLS handshakes na základě připojení:</span><span class="sxs-lookup"><span data-stu-id="d978a-394">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

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

<span data-ttu-id="d978a-395">*Nastavení protokolu z konfigurace*</span><span class="sxs-lookup"><span data-stu-id="d978a-395">*Set the protocol from configuration*</span></span>

<span data-ttu-id="d978a-396">`CreateDefaultBuilder`ve `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` výchozím nastavení načítá konfiguraci Kestrel.</span><span class="sxs-lookup"><span data-stu-id="d978a-396">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="d978a-397">Následující příklad *appsettings.json* vytvoří HTTP/1.1 jako výchozí protokol připojení pro všechny koncové body:</span><span class="sxs-lookup"><span data-stu-id="d978a-397">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="d978a-398">Následující příklad *appsettings.json* vytvoří protokol připojení HTTP/1.1 pro konkrétní koncový bod:</span><span class="sxs-lookup"><span data-stu-id="d978a-398">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="d978a-399">Protokoly zadané v hodnotách přepsání kódu nastavených konfigurací.</span><span class="sxs-lookup"><span data-stu-id="d978a-399">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="d978a-400">Konfigurace přenosu</span><span class="sxs-lookup"><span data-stu-id="d978a-400">Transport configuration</span></span>

<span data-ttu-id="d978a-401">Pro projekty, které vyžadují použití<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>Libuv ( ):</span><span class="sxs-lookup"><span data-stu-id="d978a-401">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span></span>

* <span data-ttu-id="d978a-402">Přidejte závislost pro balíček [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) do souboru projektu aplikace:</span><span class="sxs-lookup"><span data-stu-id="d978a-402">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* <span data-ttu-id="d978a-403">Volejte <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> na `IWebHostBuilder`:</span><span class="sxs-lookup"><span data-stu-id="d978a-403">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> on the `IWebHostBuilder`:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="d978a-404">Předpony adres URL</span><span class="sxs-lookup"><span data-stu-id="d978a-404">URL prefixes</span></span>

<span data-ttu-id="d978a-405">Při `UseUrls`použití `--urls` , argument `urls` příkazového řádku, `ASPNETCORE_URLS` konfigurační klíč hostitele nebo proměnné prostředí, předpony URL může být v některém z následujících formátů.</span><span class="sxs-lookup"><span data-stu-id="d978a-405">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="d978a-406">Platné jsou pouze předpony adresy URL HTTP.</span><span class="sxs-lookup"><span data-stu-id="d978a-406">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="d978a-407">Kestrel nepodporuje protokol HTTPS při konfiguraci `UseUrls`vazeb URL pomocí .</span><span class="sxs-lookup"><span data-stu-id="d978a-407">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="d978a-408">Adresa IPv4 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="d978a-408">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="d978a-409">`0.0.0.0`je zvláštní případ, který se váže na všechny adresy IPv4.</span><span class="sxs-lookup"><span data-stu-id="d978a-409">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="d978a-410">Adresa IPv6 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="d978a-410">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="d978a-411">`[::]`je ekvivalent IPv6 iPv4 `0.0.0.0`.</span><span class="sxs-lookup"><span data-stu-id="d978a-411">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="d978a-412">Název hostitele s číslem portu</span><span class="sxs-lookup"><span data-stu-id="d978a-412">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="d978a-413">Názvy `*`hostitelů `+`, a , nejsou zvláštní.</span><span class="sxs-lookup"><span data-stu-id="d978a-413">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="d978a-414">Cokoli, co není rozpoznáno `localhost` jako platná adresa IP nebo se váže na všechny IP adresy IPv4 a IPv6.</span><span class="sxs-lookup"><span data-stu-id="d978a-414">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="d978a-415">Chcete-li svázat různé názvy hostitelů s různými aplikacemi ASP.NET Core na stejném portu, použijte [http.sys](xref:fundamentals/servers/httpsys) nebo reverzní proxy server, jako je Služba IIS, Nginx nebo Apache.</span><span class="sxs-lookup"><span data-stu-id="d978a-415">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="d978a-416">Hostování v konfiguraci reverzní proxy vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="d978a-416">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="d978a-417">Název `localhost` hostitele s číslem portu nebo ip adresou zpětné smyčky s číslem portu</span><span class="sxs-lookup"><span data-stu-id="d978a-417">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="d978a-418">Je-li `localhost` zadáno, kestrel se pokusí vytvořit vazbu na rozhraní zpětné smyčky IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="d978a-418">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="d978a-419">Pokud je požadovaný port používán jinou službou na obou rozhraních zpětné smyčky, kestrel se nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="d978a-419">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="d978a-420">Pokud buď rozhraní zpětné smyčky není k dispozici z jakéhokoli jiného důvodu (nejčastěji proto, že Není podporován protokol Empv6), Kestrel protokoly upozornění.</span><span class="sxs-lookup"><span data-stu-id="d978a-420">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="d978a-421">Filtrování hostitelů</span><span class="sxs-lookup"><span data-stu-id="d978a-421">Host filtering</span></span>

<span data-ttu-id="d978a-422">Zatímco Kestrel podporuje konfiguraci založenou na předponami, jako je například `http://example.com:5000`, Kestrel do značné míry ignoruje název hostitele.</span><span class="sxs-lookup"><span data-stu-id="d978a-422">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="d978a-423">Hostitel `localhost` je zvláštní případ používaný pro vazbu na adresy zpětné smyčky.</span><span class="sxs-lookup"><span data-stu-id="d978a-423">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="d978a-424">Jakýkoli hostitel jiný než explicitní adresa IP se váže na všechny veřejné IP adresy.</span><span class="sxs-lookup"><span data-stu-id="d978a-424">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="d978a-425">`Host`záhlaví nejsou ověřena.</span><span class="sxs-lookup"><span data-stu-id="d978a-425">`Host` headers aren't validated.</span></span>

<span data-ttu-id="d978a-426">Jako řešení použijte middleware filtrování hostitelů.</span><span class="sxs-lookup"><span data-stu-id="d978a-426">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="d978a-427">Middleware filtrování hostitelů poskytuje balíček [Microsoft.AspNetCore.HostFiltering,](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) který je implicitně k dispozici pro ASP.NET aplikace Core.</span><span class="sxs-lookup"><span data-stu-id="d978a-427">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="d978a-428">Middleware je přidán <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>který volá :</span><span class="sxs-lookup"><span data-stu-id="d978a-428">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="d978a-429">Middleware filtrování hostitelů je ve výchozím nastavení zakázáno.</span><span class="sxs-lookup"><span data-stu-id="d978a-429">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="d978a-430">Chcete-li povolit middleware, definujte `AllowedHosts` klíč v *appsettings.json*/*appsettings.\< EnvironmentName>.json*.</span><span class="sxs-lookup"><span data-stu-id="d978a-430">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="d978a-431">Hodnota je seznam názvů hostitelů oddělených středníkem bez čísel portů:</span><span class="sxs-lookup"><span data-stu-id="d978a-431">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="d978a-432">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="d978a-432">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="d978a-433">[Předávaných záhlaví Middleware](xref:host-and-deploy/proxy-load-balancer) má také možnost. <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts></span><span class="sxs-lookup"><span data-stu-id="d978a-433">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="d978a-434">Předané hlavičky Middleware a Host Filtrování Middleware mají podobné funkce pro různé scénáře.</span><span class="sxs-lookup"><span data-stu-id="d978a-434">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="d978a-435">Nastavení `AllowedHosts` pomocí middlewaru s předanými hlavičkami je vhodné, pokud `Host` záhlaví není zachováno při předávání požadavků pomocí reverzního proxy serveru nebo vyrovnávání zatížení.</span><span class="sxs-lookup"><span data-stu-id="d978a-435">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="d978a-436">Nastavení `AllowedHosts` s Middleware filtrování hostitelů je vhodné, pokud je Kestrel `Host` používán jako veřejný hraniční server nebo když je záhlaví přímo předáno dál.</span><span class="sxs-lookup"><span data-stu-id="d978a-436">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="d978a-437">Další informace o middlewaru s <xref:host-and-deploy/proxy-load-balancer>předaných záhlaví naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="d978a-437">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="d978a-438">Kestrel je multiplatformní [webový server pro ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="d978a-438">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="d978a-439">Kestrel je webový server, který je ve výchozím nastavení zahrnut v šablonách projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d978a-439">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="d978a-440">Poštolka podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="d978a-440">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="d978a-441">HTTPS</span><span class="sxs-lookup"><span data-stu-id="d978a-441">HTTPS</span></span>
* <span data-ttu-id="d978a-442">Neprůhledný upgrade používaný k povolení [websocketů](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="d978a-442">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="d978a-443">Unixové zásuvky pro vysoký výkon za Nginx</span><span class="sxs-lookup"><span data-stu-id="d978a-443">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="d978a-444">HTTP/2 (kromě macOS)&dagger;</span><span class="sxs-lookup"><span data-stu-id="d978a-444">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="d978a-445">&dagger;HTTP/2 bude v budoucí verzi v systému macOS podporován.</span><span class="sxs-lookup"><span data-stu-id="d978a-445">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="d978a-446">Kestrel je podporován na všech platformách a verzích, které podporuje .NET Core.</span><span class="sxs-lookup"><span data-stu-id="d978a-446">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="d978a-447">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="d978a-447">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="d978a-448">Podpora HTTP/2</span><span class="sxs-lookup"><span data-stu-id="d978a-448">HTTP/2 support</span></span>

<span data-ttu-id="d978a-449">[HTTP/2](https://httpwg.org/specs/rfc7540.html) je k dispozici pro ASP.NET základní aplikace, pokud jsou splněny následující základní požadavky:</span><span class="sxs-lookup"><span data-stu-id="d978a-449">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="d978a-450">Operační systém&dagger;</span><span class="sxs-lookup"><span data-stu-id="d978a-450">Operating system&dagger;</span></span>
  * <span data-ttu-id="d978a-451">Windows Server 2016/Windows 10 nebo novější&Dagger;</span><span class="sxs-lookup"><span data-stu-id="d978a-451">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="d978a-452">Linux s OpenSSL 1.0.2 nebo novějším (například Ubuntu 16.04 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="d978a-452">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="d978a-453">Cílová architektura: .NET Core 2.2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="d978a-453">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="d978a-454">Připojení [protokolu ap (APPLICATION-Layer Protocol) (ALPN)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="d978a-454">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="d978a-455">Připojení TLS 1.2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="d978a-455">TLS 1.2 or later connection</span></span>

<span data-ttu-id="d978a-456">&dagger;HTTP/2 bude v budoucí verzi v systému macOS podporován.</span><span class="sxs-lookup"><span data-stu-id="d978a-456">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="d978a-457">&Dagger;Kestrel má omezenou podporu pro HTTP/2 v systémech Windows Server 2012 R2 a Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="d978a-457">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="d978a-458">Podpora je omezená, protože seznam podporovaných šifrovacích sad TLS dostupných v těchto operačních systémech je omezený.</span><span class="sxs-lookup"><span data-stu-id="d978a-458">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="d978a-459">K zabezpečení připojení TLS může být vyžadován certifikát generovaný pomocí algoritmu digitálního podpisu eliptické křivky (ECDSA).</span><span class="sxs-lookup"><span data-stu-id="d978a-459">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="d978a-460">Pokud je navázáno připojení HTTP/2, `HTTP/2`protokol [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) hlásí .</span><span class="sxs-lookup"><span data-stu-id="d978a-460">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="d978a-461">Protokol HTTP/2 je ve výchozím nastavení zakázán.</span><span class="sxs-lookup"><span data-stu-id="d978a-461">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="d978a-462">Další informace o konfiguraci naleznete v části [Možnosti kestrelu](#kestrel-options) a [ListenOptions.Protocols.](#listenoptionsprotocols)</span><span class="sxs-lookup"><span data-stu-id="d978a-462">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="d978a-463">Kdy použít Kestrel s reverzním proxy serverem</span><span class="sxs-lookup"><span data-stu-id="d978a-463">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="d978a-464">Poštolka může být použita sama o sobě nebo s *reverzním proxy serverem*, jako je [Internetová informační služba (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="d978a-464">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="d978a-465">Reverzní proxy server přijímá požadavky HTTP ze sítě a předá je ke strel.</span><span class="sxs-lookup"><span data-stu-id="d978a-465">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="d978a-466">Poštolka používá jako okraj (Internet-čelí) webový server:</span><span class="sxs-lookup"><span data-stu-id="d978a-466">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Poštolka komunikuje přímo s Internetem bez reverzního proxy serveru](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="d978a-468">Poštolka použitá v konfiguraci reverzního proxy serveru:</span><span class="sxs-lookup"><span data-stu-id="d978a-468">Kestrel used in a reverse proxy configuration:</span></span>

![Poštolka komunikuje nepřímo s Internetem prostřednictvím reverzního proxy serveru, jako je služba IIS, Nginx nebo Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="d978a-470">Konfigurace, s reverzním proxy serverem nebo bez něj, je podporovaná konfigurace hostování.</span><span class="sxs-lookup"><span data-stu-id="d978a-470">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="d978a-471">Kestrel používaný jako hraniční server bez reverzního proxy serveru nepodporuje sdílení stejné IP a portu mezi více procesy.</span><span class="sxs-lookup"><span data-stu-id="d978a-471">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="d978a-472">Když kestrel je nakonfigurován pro poslech na portu, Kestrel zpracovává všechny přenosy pro tento port bez ohledu na hlavičky požadavků.When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span><span class="sxs-lookup"><span data-stu-id="d978a-472">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="d978a-473">Reverzní proxy server, který může sdílet porty, má schopnost předávat požadavky Kestrelu na jedinečné IP a portu.</span><span class="sxs-lookup"><span data-stu-id="d978a-473">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="d978a-474">I v případě, že reverzní proxy server není vyžadován, použití reverzní proxy server může být dobrou volbou.</span><span class="sxs-lookup"><span data-stu-id="d978a-474">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="d978a-475">Reverzní proxy server:</span><span class="sxs-lookup"><span data-stu-id="d978a-475">A reverse proxy:</span></span>

* <span data-ttu-id="d978a-476">Může omezit exponované veřejné plochy aplikací, které hostuje.</span><span class="sxs-lookup"><span data-stu-id="d978a-476">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="d978a-477">Poskytněte další vrstvu konfigurace a obrany.</span><span class="sxs-lookup"><span data-stu-id="d978a-477">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="d978a-478">Mohlo by se lépe integrovat se stávající infrastrukturou.</span><span class="sxs-lookup"><span data-stu-id="d978a-478">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="d978a-479">Zjednodušte vyrovnávání zatížení a zabezpečenou komunikaci (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="d978a-479">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="d978a-480">Pouze reverzní proxy server vyžaduje certifikát X.509 a tento server může komunikovat se servery aplikace v interní síti pomocí prostého protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="d978a-480">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="d978a-481">Hostování v konfiguraci reverzní proxy vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="d978a-481">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="d978a-482">Jak používat Kestrel v aplikacích ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d978a-482">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="d978a-483">Balíček [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) je součástí [metabalíčku Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="d978a-483">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="d978a-484">ASP.NET Šablony projektu Core používají kestrel ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="d978a-484">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="d978a-485">V *Program.cs*volání <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>kódu šablony <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> , který volá na pozadí.</span><span class="sxs-lookup"><span data-stu-id="d978a-485">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="d978a-486">Další informace `CreateDefaultBuilder` o hostiteli a jeho vytváření naleznete <xref:fundamentals/host/web-host#set-up-a-host>v části Nastavení *hostitele* v .</span><span class="sxs-lookup"><span data-stu-id="d978a-486">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="d978a-487">Chcete-li po `CreateDefaultBuilder`volání `ConfigureKestrel`poskytnout další konfiguraci , použijte :</span><span class="sxs-lookup"><span data-stu-id="d978a-487">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="d978a-488">Pokud aplikace `CreateDefaultBuilder` nevolá pro nastavení hostitele, <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> zavolejte `ConfigureKestrel` **před** voláním :</span><span class="sxs-lookup"><span data-stu-id="d978a-488">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="d978a-489">Možnosti poštolky</span><span class="sxs-lookup"><span data-stu-id="d978a-489">Kestrel options</span></span>

<span data-ttu-id="d978a-490">Webový server Kestrel má možnosti konfigurace omezení, které jsou užitečné zejména v internetových nasazeních.</span><span class="sxs-lookup"><span data-stu-id="d978a-490">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="d978a-491">Nastavte omezení vlastnosti <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> třídy. <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions></span><span class="sxs-lookup"><span data-stu-id="d978a-491">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="d978a-492">Vlastnost `Limits` obsahuje instanci <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> třídy.</span><span class="sxs-lookup"><span data-stu-id="d978a-492">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="d978a-493">V následujících příkladech se <xref:Microsoft.AspNetCore.Server.Kestrel.Core> používá obor názvů:</span><span class="sxs-lookup"><span data-stu-id="d978a-493">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="d978a-494">Možnosti kestrelu, které jsou konfigurovány v kódu Jazyka C# v následujících příkladech, lze také nastavit pomocí [zprostředkovatele konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="d978a-494">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="d978a-495">Zprostředkovatel konfigurace souborů může například načíst konfiguraci kestrelu z souboru *appsettings.json* nebo *appsettings.{ Prostředí}.json:*</span><span class="sxs-lookup"><span data-stu-id="d978a-495">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="d978a-496">Použijte **jeden** z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="d978a-496">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="d978a-497">Konfigurovat poštolek `Startup.ConfigureServices`v :</span><span class="sxs-lookup"><span data-stu-id="d978a-497">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="d978a-498">Vstříkněte `IConfiguration` `Startup` instanci do třídy.</span><span class="sxs-lookup"><span data-stu-id="d978a-498">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="d978a-499">Následující příklad předpokládá, že vstřikovaná `Configuration` konfigurace je přiřazena vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="d978a-499">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="d978a-500">V `Startup.ConfigureServices`, `Kestrel` načtěte část konfigurace do konfigurace Kestrel:</span><span class="sxs-lookup"><span data-stu-id="d978a-500">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="d978a-501">Konfigurace kestrelu při vytváření hostitele:</span><span class="sxs-lookup"><span data-stu-id="d978a-501">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="d978a-502">V *Program.cs*načtěte `Kestrel` část konfigurace do konfigurace Kestrelu:</span><span class="sxs-lookup"><span data-stu-id="d978a-502">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="d978a-503">Oba předchozí přístupy pracují s libovolným [zprostředkovatelem konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="d978a-503">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="d978a-504">Časový limit udržování života</span><span class="sxs-lookup"><span data-stu-id="d978a-504">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="d978a-505">Získá nebo nastaví [časový limit keep-alive](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="d978a-505">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="d978a-506">Výchozí hodnota je 2 minuty.</span><span class="sxs-lookup"><span data-stu-id="d978a-506">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="d978a-507">Maximální počet připojení klientů</span><span class="sxs-lookup"><span data-stu-id="d978a-507">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="d978a-508">Maximální počet souběžných otevřených připojení TCP lze nastavit pro celou aplikaci s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="d978a-508">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="d978a-509">Existuje samostatné omezení pro připojení, která byla upgradována z protokolu HTTP nebo HTTPS na jiný protokol (například na požadavek WebSockets).</span><span class="sxs-lookup"><span data-stu-id="d978a-509">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="d978a-510">Po upgradu připojení se nezapočítává `MaxConcurrentConnections` do limitu.</span><span class="sxs-lookup"><span data-stu-id="d978a-510">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="d978a-511">Maximální počet připojení je ve výchozím nastavení neomezený (null).</span><span class="sxs-lookup"><span data-stu-id="d978a-511">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="d978a-512">Maximální velikost těla požadavku</span><span class="sxs-lookup"><span data-stu-id="d978a-512">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="d978a-513">Výchozí maximální velikost těla požadavku je 30 000 000 bajtů, což je přibližně 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="d978a-513">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="d978a-514">Doporučený přístup k přepsání limitu v aplikaci ASP.NET <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> Core MVC je použití atributu na metodě akce:</span><span class="sxs-lookup"><span data-stu-id="d978a-514">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="d978a-515">Tady je příklad, který ukazuje, jak nakonfigurovat omezení pro aplikaci při každém požadavku:</span><span class="sxs-lookup"><span data-stu-id="d978a-515">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="d978a-516">Přepište nastavení na konkrétní požadavek v middlewaru:</span><span class="sxs-lookup"><span data-stu-id="d978a-516">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="d978a-517">Výjimka je vyvolána, pokud aplikace konfiguruje limit na požadavek poté, co aplikace začala číst požadavek.</span><span class="sxs-lookup"><span data-stu-id="d978a-517">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="d978a-518">Je `IsReadOnly` vlastnost, která označuje, `MaxRequestBodySize` pokud je vlastnost ve stavu jen pro čtení, což znamená, že je příliš pozdě na konfiguraci limitu.</span><span class="sxs-lookup"><span data-stu-id="d978a-518">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="d978a-519">Pokud je aplikace [spuštěna mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za ASP.NET [základnímodul](xref:host-and-deploy/aspnet-core-module), kestrel požadavek body limit je zakázán, protože iis již nastaví limit.</span><span class="sxs-lookup"><span data-stu-id="d978a-519">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="d978a-520">Minimální přenosová rychlost tělo požadavku</span><span class="sxs-lookup"><span data-stu-id="d978a-520">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="d978a-521">Poštolka kontroluje každou sekundu, pokud data přicházejí na zadanou rychlost v bajtů za sekundu.</span><span class="sxs-lookup"><span data-stu-id="d978a-521">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="d978a-522">Pokud rychlost klesne pod minimum, je časový limit připojení. Doba odkladu je doba, kterou Kestrel dává klientovi ke zvýšení míry odeslání až na minimum; během této doby není sazba kontrolována.</span><span class="sxs-lookup"><span data-stu-id="d978a-522">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="d978a-523">Období odkladu pomáhá zabránit přetažení připojení, které jsou zpočátku odesílání dat pomalým tempem z důvodu tcp pomalé spuštění.</span><span class="sxs-lookup"><span data-stu-id="d978a-523">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="d978a-524">Výchozí minimální sazba je 240 bajtů za sekundu s 5 sekundovou lhůtou odkladu.</span><span class="sxs-lookup"><span data-stu-id="d978a-524">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="d978a-525">Minimální sazba se vztahuje také na odpověď.</span><span class="sxs-lookup"><span data-stu-id="d978a-525">A minimum rate also applies to the response.</span></span> <span data-ttu-id="d978a-526">Kód pro nastavení limitu požadavku a limitu odpovědi `RequestBody` `Response` je stejný s výjimkou s nebo v názvy vlastností a rozhraní.</span><span class="sxs-lookup"><span data-stu-id="d978a-526">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="d978a-527">Zde je příklad, který ukazuje, jak nakonfigurovat minimální rychlost dat v *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="d978a-527">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="d978a-528">Přepsat minimální limity rychlosti na požadavek v middlewaru:</span><span class="sxs-lookup"><span data-stu-id="d978a-528">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="d978a-529">Pro požadavky HTTP/2 nejsou k dispozici `HttpContext.Features` ani funkce rychlosti, na kterou odkazuje předchozí ukázka, protože úprava omezení rychlosti na základě požadavku není pro protokol HTTP/2 podporována z důvodu podpory protokolu pro multiplexování požadavků.</span><span class="sxs-lookup"><span data-stu-id="d978a-529">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="d978a-530">Limity rychlosti na `KestrelServerOptions.Limits` celém serveru nakonfigurované prostřednictvím připojení HTTP/1.x i HTTP/2 stále platí.</span><span class="sxs-lookup"><span data-stu-id="d978a-530">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="d978a-531">Časový výtažek záhlaví požadavku</span><span class="sxs-lookup"><span data-stu-id="d978a-531">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="d978a-532">Získá nebo nastaví maximální množství času, které server stráví příjem hlavičky požadavku.</span><span class="sxs-lookup"><span data-stu-id="d978a-532">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="d978a-533">Výchozí hodnota je 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="d978a-533">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="d978a-534">Maximální počet datových proudů na připojení</span><span class="sxs-lookup"><span data-stu-id="d978a-534">Maximum streams per connection</span></span>

<span data-ttu-id="d978a-535">`Http2.MaxStreamsPerConnection`omezuje počet souběžných datových proudů požadavků na připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="d978a-535">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="d978a-536">Přebytečné proudy jsou odmítnuty.</span><span class="sxs-lookup"><span data-stu-id="d978a-536">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="d978a-537">Výchozí hodnota je 100.</span><span class="sxs-lookup"><span data-stu-id="d978a-537">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="d978a-538">Velikost tabulky záhlaví</span><span class="sxs-lookup"><span data-stu-id="d978a-538">Header table size</span></span>

<span data-ttu-id="d978a-539">Dekodér HPACK dekomprimuje hlavičky HTTP pro připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="d978a-539">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="d978a-540">`Http2.HeaderTableSize`omezuje velikost tabulky komprese záhlaví, kterou dekodér HPACK používá.</span><span class="sxs-lookup"><span data-stu-id="d978a-540">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="d978a-541">Hodnota je uvedena v oktetech a musí být větší než nula (0).</span><span class="sxs-lookup"><span data-stu-id="d978a-541">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="d978a-542">Výchozí hodnota je 4096.</span><span class="sxs-lookup"><span data-stu-id="d978a-542">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="d978a-543">Maximální velikost rámu</span><span class="sxs-lookup"><span data-stu-id="d978a-543">Maximum frame size</span></span>

<span data-ttu-id="d978a-544">`Http2.MaxFrameSize`označuje maximální velikost datové části rámce připojení HTTP/2, kterou má být přijímána.</span><span class="sxs-lookup"><span data-stu-id="d978a-544">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="d978a-545">Hodnota je uvedena v oktetech a musí být mezi 2^14 (16 384) a 2^24-1 (16 777 215).</span><span class="sxs-lookup"><span data-stu-id="d978a-545">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="d978a-546">Výchozí hodnota je 2^14 (16 384).</span><span class="sxs-lookup"><span data-stu-id="d978a-546">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="d978a-547">Maximální velikost hlavičky požadavku</span><span class="sxs-lookup"><span data-stu-id="d978a-547">Maximum request header size</span></span>

<span data-ttu-id="d978a-548">`Http2.MaxRequestHeaderFieldSize`označuje maximální povolenou velikost v oktetech hodnot hlaviček požadavku.</span><span class="sxs-lookup"><span data-stu-id="d978a-548">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="d978a-549">Toto omezení platí pro název i hodnotu společně v jejich komprimované a nekomprimované reprezentace.</span><span class="sxs-lookup"><span data-stu-id="d978a-549">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="d978a-550">Hodnota musí být větší než nula (0).</span><span class="sxs-lookup"><span data-stu-id="d978a-550">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="d978a-551">Výchozí hodnota je 8 192.</span><span class="sxs-lookup"><span data-stu-id="d978a-551">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="d978a-552">Počáteční velikost okna připojení</span><span class="sxs-lookup"><span data-stu-id="d978a-552">Initial connection window size</span></span>

<span data-ttu-id="d978a-553">`Http2.InitialConnectionWindowSize`označuje maximální data těla požadavků v bajtech vyrovnávacích pamětí serveru najednou agregované ve všech požadavcích (datových proudech) na připojení.</span><span class="sxs-lookup"><span data-stu-id="d978a-553">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="d978a-554">Žádosti jsou také `Http2.InitialStreamWindowSize`omezeny .</span><span class="sxs-lookup"><span data-stu-id="d978a-554">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="d978a-555">Hodnota musí být větší nebo rovna 65 535 a menší než 2^31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="d978a-555">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="d978a-556">Výchozí hodnota je 128 kB (131 072).</span><span class="sxs-lookup"><span data-stu-id="d978a-556">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="d978a-557">Počáteční velikost okna datového proudu</span><span class="sxs-lookup"><span data-stu-id="d978a-557">Initial stream window size</span></span>

<span data-ttu-id="d978a-558">`Http2.InitialStreamWindowSize`označuje maximální data těla požadavku v bajtů vyrovnávacích pamětí serveru najednou na jeden požadavek (datový proud).</span><span class="sxs-lookup"><span data-stu-id="d978a-558">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="d978a-559">Žádosti jsou také `Http2.InitialStreamWindowSize`omezeny .</span><span class="sxs-lookup"><span data-stu-id="d978a-559">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="d978a-560">Hodnota musí být větší nebo rovna 65 535 a menší než 2^31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="d978a-560">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="d978a-561">Výchozí hodnota je 96 KB (98 304).</span><span class="sxs-lookup"><span data-stu-id="d978a-561">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="d978a-562">Synchronní vstupně-výstupní operace</span><span class="sxs-lookup"><span data-stu-id="d978a-562">Synchronous I/O</span></span>

<span data-ttu-id="d978a-563"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>určuje, zda je pro požadavek a odpověď povolena synchronní vstupně-va.</span><span class="sxs-lookup"><span data-stu-id="d978a-563"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="d978a-564">Výchozí hodnota `true`je .</span><span class="sxs-lookup"><span data-stu-id="d978a-564">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="d978a-565">Velký počet blokování synchronní vstupně-va operace může vést k nedostatku fondu vláken, což způsobí, že aplikace nereaguje.</span><span class="sxs-lookup"><span data-stu-id="d978a-565">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="d978a-566">Povolit `AllowSynchronousIO` pouze při použití knihovny, která nepodporuje asynchronní vstupně-v.A/O.</span><span class="sxs-lookup"><span data-stu-id="d978a-566">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="d978a-567">Následující příklad umožňuje synchronní vstupně-va:</span><span class="sxs-lookup"><span data-stu-id="d978a-567">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="d978a-568">Informace o dalších možnostech a limitech kestrelu naleznete v těchto tématech:</span><span class="sxs-lookup"><span data-stu-id="d978a-568">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="d978a-569">Konfigurace koncového bodu</span><span class="sxs-lookup"><span data-stu-id="d978a-569">Endpoint configuration</span></span>

<span data-ttu-id="d978a-570">Ve výchozím nastavení se ASP.NET jádro váže na:</span><span class="sxs-lookup"><span data-stu-id="d978a-570">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="d978a-571">`https://localhost:5001`(pokud je k dispozici certifikát místního rozvoje)</span><span class="sxs-lookup"><span data-stu-id="d978a-571">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="d978a-572">Zadejte adresy URL pomocí:</span><span class="sxs-lookup"><span data-stu-id="d978a-572">Specify URLs using the:</span></span>

* <span data-ttu-id="d978a-573">`ASPNETCORE_URLS`proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="d978a-573">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="d978a-574">`--urls`argument příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="d978a-574">`--urls` command-line argument.</span></span>
* <span data-ttu-id="d978a-575">`urls`konfiguračního klíče hostitele.</span><span class="sxs-lookup"><span data-stu-id="d978a-575">`urls` host configuration key.</span></span>
* <span data-ttu-id="d978a-576">`UseUrls`rozšíření.</span><span class="sxs-lookup"><span data-stu-id="d978a-576">`UseUrls` extension method.</span></span>

<span data-ttu-id="d978a-577">Hodnota poskytovaná pomocí těchto přístupů může být jeden nebo více koncových bodů HTTP a HTTPS (HTTPS, pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="d978a-577">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="d978a-578">Nakonfigurujte hodnotu jako seznam oddělený `"Urls": "http://localhost:8000;http://localhost:8001"`středníkem (například).</span><span class="sxs-lookup"><span data-stu-id="d978a-578">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="d978a-579">Další informace o těchto přístupech naleznete v [tématu Adresy URL serveru](xref:fundamentals/host/web-host#server-urls) a [konfigurace override](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="d978a-579">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="d978a-580">Je vytvořen vývojový certifikát:</span><span class="sxs-lookup"><span data-stu-id="d978a-580">A development certificate is created:</span></span>

* <span data-ttu-id="d978a-581">Při instalaci sady [.NET Core SDK.](/dotnet/core/sdk)</span><span class="sxs-lookup"><span data-stu-id="d978a-581">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="d978a-582">Nástroj [dev-certs](xref:aspnetcore-2.1#https) se používá k vytvoření certifikátu.</span><span class="sxs-lookup"><span data-stu-id="d978a-582">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="d978a-583">Některé prohlížeče vyžadují udělení výslovného oprávnění důvěřovat certifikátu místního vývoje.</span><span class="sxs-lookup"><span data-stu-id="d978a-583">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="d978a-584">Šablony projektů konfigurují aplikace tak, aby se ve výchozím nastavení spouštěly na protokolu HTTPS a zahrnovaly [přesměrování HTTPS a podporu HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="d978a-584">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="d978a-585">Volání <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> nebo <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> metody na konfiguraci URL předpony a porty pro Kestrel.</span><span class="sxs-lookup"><span data-stu-id="d978a-585">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="d978a-586">`UseUrls`, `--urls` argument příkazového `urls` řádku, konfigurační klíč hostitele a proměnná `ASPNETCORE_URLS` prostředí také fungují, ale omezení jsou zaznamenána dále v této části (výchozí certifikát musí být k dispozici pro konfiguraci koncového bodu HTTPS).</span><span class="sxs-lookup"><span data-stu-id="d978a-586">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="d978a-587">`KestrelServerOptions`Konfigurace:</span><span class="sxs-lookup"><span data-stu-id="d978a-587">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="d978a-588">ConfigureEndpointDefaults (akce\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="d978a-588">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="d978a-589">Určuje konfiguraci, `Action` která má být spuštěna pro každý zadaný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="d978a-589">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="d978a-590">Volání `ConfigureEndpointDefaults` vícekrát nahradí `Action`předchozí s `Action` posledním zadaným.</span><span class="sxs-lookup"><span data-stu-id="d978a-590">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="d978a-591">Koncové body vytvořené <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> voláním **před** voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> nebudou mít výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="d978a-591">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="d978a-592">KonfigurovatHttpsDefaults (akce\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="d978a-592">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="d978a-593">Určuje konfiguraci, `Action` která má být spuštěna pro každý koncový bod HTTPS.</span><span class="sxs-lookup"><span data-stu-id="d978a-593">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="d978a-594">Volání `ConfigureHttpsDefaults` vícekrát nahradí `Action`předchozí s `Action` posledním zadaným.</span><span class="sxs-lookup"><span data-stu-id="d978a-594">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="d978a-595">Koncové body vytvořené <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> voláním **před** voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> nebudou mít výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="d978a-595">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>


### <a name="configureiconfiguration"></a><span data-ttu-id="d978a-596">Configure(IKonfigurace)</span><span class="sxs-lookup"><span data-stu-id="d978a-596">Configure(IConfiguration)</span></span>

<span data-ttu-id="d978a-597">Vytvoří konfigurační zavaděč pro <xref:Microsoft.Extensions.Configuration.IConfiguration> nastavení Kestrel, který bere jako vstup.</span><span class="sxs-lookup"><span data-stu-id="d978a-597">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="d978a-598">Konfigurace musí být vymezena na konfigurační sekci pro Kestrel.</span><span class="sxs-lookup"><span data-stu-id="d978a-598">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="d978a-599">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="d978a-599">ListenOptions.UseHttps</span></span>

<span data-ttu-id="d978a-600">Nakonfigurujte Kestrel pro použití protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="d978a-600">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="d978a-601">`ListenOptions.UseHttps`Rozšíření:</span><span class="sxs-lookup"><span data-stu-id="d978a-601">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="d978a-602">`UseHttps`&ndash; Nakonfigurujte Kestrel tak, aby používal protokol HTTPS s výchozím certifikátem.</span><span class="sxs-lookup"><span data-stu-id="d978a-602">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="d978a-603">Vyvolá výjimku, pokud není nakonfigurován žádný výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="d978a-603">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="d978a-604">`ListenOptions.UseHttps`Parametry:</span><span class="sxs-lookup"><span data-stu-id="d978a-604">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="d978a-605">`filename`je cesta a název souboru certifikátu vzhledem k adresáři, který obsahuje soubory obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="d978a-605">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="d978a-606">`password`je heslo potřebné pro přístup k datům certifikátu X.509.</span><span class="sxs-lookup"><span data-stu-id="d978a-606">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="d978a-607">`configureOptions`je `Action` konfigurace rozhraní `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="d978a-607">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="d978a-608">Vrátí `ListenOptions`hodnotu .</span><span class="sxs-lookup"><span data-stu-id="d978a-608">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="d978a-609">`storeName`je úložiště certifikátů, ze kterého se má certifikát načíst.</span><span class="sxs-lookup"><span data-stu-id="d978a-609">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="d978a-610">`subject`je název subjektu certifikátu.</span><span class="sxs-lookup"><span data-stu-id="d978a-610">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="d978a-611">`allowInvalid`označuje, zda by měly být považovány za neplatné certifikáty, například certifikáty podepsané svým držitelem.</span><span class="sxs-lookup"><span data-stu-id="d978a-611">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="d978a-612">`location`je umístění úložiště, ze kterých se má certifikát načíst.</span><span class="sxs-lookup"><span data-stu-id="d978a-612">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="d978a-613">`serverCertificate`je certifikát X.509.</span><span class="sxs-lookup"><span data-stu-id="d978a-613">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="d978a-614">V produkčním prostředí musí být protokol HTTPS explicitně nakonfigurován.</span><span class="sxs-lookup"><span data-stu-id="d978a-614">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="d978a-615">Musí být poskytnut minimálně výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="d978a-615">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="d978a-616">Podporované konfigurace popsané dále:</span><span class="sxs-lookup"><span data-stu-id="d978a-616">Supported configurations described next:</span></span>

* <span data-ttu-id="d978a-617">Žádná konfigurace</span><span class="sxs-lookup"><span data-stu-id="d978a-617">No configuration</span></span>
* <span data-ttu-id="d978a-618">Nahrazení výchozího certifikátu z konfigurace</span><span class="sxs-lookup"><span data-stu-id="d978a-618">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="d978a-619">Změna výchozích hodnot v kódu</span><span class="sxs-lookup"><span data-stu-id="d978a-619">Change the defaults in code</span></span>

<span data-ttu-id="d978a-620">*Žádná konfigurace*</span><span class="sxs-lookup"><span data-stu-id="d978a-620">*No configuration*</span></span>

<span data-ttu-id="d978a-621">Poštolka naslouchá `http://localhost:5000` dál `https://localhost:5001` a (pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="d978a-621">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="d978a-622">*Nahrazení výchozího certifikátu z konfigurace*</span><span class="sxs-lookup"><span data-stu-id="d978a-622">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="d978a-623">`CreateDefaultBuilder`ve `Configure(context.Configuration.GetSection("Kestrel"))` výchozím nastavení načítá konfiguraci Kestrel.</span><span class="sxs-lookup"><span data-stu-id="d978a-623">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="d978a-624">Pro Kestrel je k dispozici výchozí schéma konfigurace nastavení aplikace HTTPS.</span><span class="sxs-lookup"><span data-stu-id="d978a-624">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="d978a-625">Nakonfigurujte více koncových bodů, včetně adres URL a certifikátů, které mají být používány, ze souboru na disku nebo z úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="d978a-625">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="d978a-626">V následujícím příkladu *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="d978a-626">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="d978a-627">Nastavte **AllowInvalid** na `true` povolení použití neplatných certifikátů (například certifikátů podepsaných svým držitelem).</span><span class="sxs-lookup"><span data-stu-id="d978a-627">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="d978a-628">Libovolný koncový bod HTTPS, který neurčuje certifikát (**HttpsDefaultCert** v následujícím příkladu) přejde zpět na certifikát definovaný v části **Výchozí** **certifikáty** > nebo vývojový certifikát.</span><span class="sxs-lookup"><span data-stu-id="d978a-628">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="d978a-629">Alternativou k použití **cesty** a **hesla** pro libovolný uzel certifikátu je určení certifikátu pomocí polí úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="d978a-629">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="d978a-630">**Například certifikát výchozí** certifikát **y** > lze zadat jako:</span><span class="sxs-lookup"><span data-stu-id="d978a-630">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="d978a-631">Poznámky schématu:</span><span class="sxs-lookup"><span data-stu-id="d978a-631">Schema notes:</span></span>

* <span data-ttu-id="d978a-632">Názvy koncových bodů nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="d978a-632">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="d978a-633">Například `HTTPS` a `Https` jsou platné.</span><span class="sxs-lookup"><span data-stu-id="d978a-633">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="d978a-634">Parametr `Url` je vyžadován pro každý koncový bod.</span><span class="sxs-lookup"><span data-stu-id="d978a-634">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="d978a-635">Formát tohoto parametru je stejný jako `Urls` parametr konfigurace nejvyšší úrovně s tím rozdílem, že je omezen na jednu hodnotu.</span><span class="sxs-lookup"><span data-stu-id="d978a-635">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="d978a-636">Tyto koncové body nahradit ty definované `Urls` v konfiguraci nejvyšší úrovně, nikoli jejich přidání.</span><span class="sxs-lookup"><span data-stu-id="d978a-636">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="d978a-637">Koncové body definované `Listen` v kódu prostřednictvím jsou kumulativní s koncovými body definovanými v konfigurační části.</span><span class="sxs-lookup"><span data-stu-id="d978a-637">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="d978a-638">Sekce `Certificate` je nepovinná.</span><span class="sxs-lookup"><span data-stu-id="d978a-638">The `Certificate` section is optional.</span></span> <span data-ttu-id="d978a-639">Pokud `Certificate` oddíl není zadán, použijí se výchozí hodnoty definované v předchozích scénářích.</span><span class="sxs-lookup"><span data-stu-id="d978a-639">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="d978a-640">Pokud nejsou k dispozici žádné výchozí hodnoty, server vyvolá výjimku a nepodaří se spustit.</span><span class="sxs-lookup"><span data-stu-id="d978a-640">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="d978a-641">V `Certificate` této části jsou certifikáty**hesel** **cesty**&ndash;i**úložiště** **předmětů.**&ndash;</span><span class="sxs-lookup"><span data-stu-id="d978a-641">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="d978a-642">Tímto způsobem může být definován libovolný počet koncových bodů, pokud nezpůsobí konflikty portů.</span><span class="sxs-lookup"><span data-stu-id="d978a-642">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="d978a-643">`options.Configure(context.Configuration.GetSection("{SECTION}"))`vrátí `KestrelConfigurationLoader` metodu `.Endpoint(string name, listenOptions => { })` a s metodou, kterou lze použít k doplnění nastavení nakonfigurovaného koncového bodu:</span><span class="sxs-lookup"><span data-stu-id="d978a-643">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="d978a-644">`KestrelServerOptions.ConfigurationLoader`lze přímo přistupovat k pokračování iterace na stávajícím nakladači, jako je například ten, který <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>poskytuje .</span><span class="sxs-lookup"><span data-stu-id="d978a-644">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="d978a-645">Konfigurační oddíl pro každý koncový bod `Endpoint` je k dispozici na možnosti v metodě tak, aby vlastní nastavení může být číst.</span><span class="sxs-lookup"><span data-stu-id="d978a-645">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="d978a-646">Více konfigurací může být `options.Configure(context.Configuration.GetSection("{SECTION}"))` načteno voláním znovu s jiným oddílem.</span><span class="sxs-lookup"><span data-stu-id="d978a-646">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="d978a-647">Používá se pouze poslední `Load` konfigurace, pokud není explicitně volána na předchozí instance.</span><span class="sxs-lookup"><span data-stu-id="d978a-647">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="d978a-648">Metabalíček nevolá, `Load` takže jeho výchozí konfigurační část může být nahrazena.</span><span class="sxs-lookup"><span data-stu-id="d978a-648">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="d978a-649">`KestrelConfigurationLoader`zrcadlí `Listen` rodinu API z `KestrelServerOptions` `Endpoint` jako přetížení, takže kód a koncové body konfigurace mohou být nakonfigurovány na stejném místě.</span><span class="sxs-lookup"><span data-stu-id="d978a-649">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="d978a-650">Tato přetížení nepoužívají názvy a spotřebovávají pouze výchozí nastavení z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="d978a-650">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="d978a-651">*Změna výchozích hodnot v kódu*</span><span class="sxs-lookup"><span data-stu-id="d978a-651">*Change the defaults in code*</span></span>

<span data-ttu-id="d978a-652">`ConfigureEndpointDefaults`a `ConfigureHttpsDefaults` lze použít ke změně `ListenOptions` `HttpsConnectionAdapterOptions`výchozího nastavení pro a , včetně přepsání výchozího certifikátu zadaného v předchozím scénáři.</span><span class="sxs-lookup"><span data-stu-id="d978a-652">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="d978a-653">`ConfigureEndpointDefaults`a `ConfigureHttpsDefaults` měla by být volána před konfigurací všech koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="d978a-653">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="d978a-654">*Podpora pro Kestrel pro SNI*</span><span class="sxs-lookup"><span data-stu-id="d978a-654">*Kestrel support for SNI*</span></span>

<span data-ttu-id="d978a-655">[Označení názvu serveru (SNI)](https://tools.ietf.org/html/rfc6066#section-3) lze použít k hostování více domén na stejné adrese IP a portu.</span><span class="sxs-lookup"><span data-stu-id="d978a-655">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="d978a-656">Aby sni fungovalo, klient odešle název hostitele pro zabezpečenou relaci na server během tls handshake tak, aby server mohl poskytnout správný certifikát.</span><span class="sxs-lookup"><span data-stu-id="d978a-656">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="d978a-657">Klient používá zařízený certifikát pro šifrovanou komunikaci se serverem během zabezpečené relace, která následuje po handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="d978a-657">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="d978a-658">Kestrel podporuje SNI `ServerCertificateSelector` prostřednictvím zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="d978a-658">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="d978a-659">Zpětné volání je vyvoláno jednou za připojení, aby aplikace mohla zkontrolovat název hostitele a vybrat příslušný certifikát.</span><span class="sxs-lookup"><span data-stu-id="d978a-659">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="d978a-660">Podpora SNI vyžaduje:</span><span class="sxs-lookup"><span data-stu-id="d978a-660">SNI support requires:</span></span>

* <span data-ttu-id="d978a-661">Spuštění na `netcoreapp2.1` cílové rozhraní nebo později.</span><span class="sxs-lookup"><span data-stu-id="d978a-661">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="d978a-662">Na `net461` nebo novější zpětné volání je `name` vyvolána, ale je vždy `null`.</span><span class="sxs-lookup"><span data-stu-id="d978a-662">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="d978a-663">Je `name` také `null` v případě, že klient neposkytuje parametr název hostitele v tls handshake.</span><span class="sxs-lookup"><span data-stu-id="d978a-663">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="d978a-664">Všechny webové stránky běží na stejné instanci Kestrel.</span><span class="sxs-lookup"><span data-stu-id="d978a-664">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="d978a-665">Kestrel nepodporuje sdílení IP adresy a portu ve více instancích bez reverzního proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="d978a-665">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="d978a-666">Protokolování připojení</span><span class="sxs-lookup"><span data-stu-id="d978a-666">Connection logging</span></span>

<span data-ttu-id="d978a-667">Volání <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> vyzařovat protokoly úrovně ladění pro komunikaci na úrovni bajtů na připojení.</span><span class="sxs-lookup"><span data-stu-id="d978a-667">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="d978a-668">Protokolování připojení je užitečné při řešení problémů v nízkoúrovňové komunikaci, například během šifrování TLS a za proxy servery.</span><span class="sxs-lookup"><span data-stu-id="d978a-668">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="d978a-669">Pokud `UseConnectionLogging` je `UseHttps`umístěn před , šifrovaný provoz je zaznamenána.</span><span class="sxs-lookup"><span data-stu-id="d978a-669">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="d978a-670">Pokud `UseConnectionLogging` je `UseHttps`umístěn po , dešifrovaný provoz je zaznamenána.</span><span class="sxs-lookup"><span data-stu-id="d978a-670">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="d978a-671">Vazba na soket TCP</span><span class="sxs-lookup"><span data-stu-id="d978a-671">Bind to a TCP socket</span></span>

<span data-ttu-id="d978a-672">Metoda <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> se váže na soket TCP a možnosti lambda umožňuje konfiguraci certifikátu X.509:</span><span class="sxs-lookup"><span data-stu-id="d978a-672">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="d978a-673">Příklad konfiguruje protokol <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>HTTPS pro koncový bod s aplikací .</span><span class="sxs-lookup"><span data-stu-id="d978a-673">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="d978a-674">Stejné rozhraní API použijte ke konfiguraci dalších nastavení kestrelu pro konkrétní koncové body.</span><span class="sxs-lookup"><span data-stu-id="d978a-674">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="d978a-675">Vazba na unixovou zásuvku</span><span class="sxs-lookup"><span data-stu-id="d978a-675">Bind to a Unix socket</span></span>

<span data-ttu-id="d978a-676">Poslouchejte na unixovém soketu s <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> pro lepší výkon s Nginx, jak je znázorněno v tomto příkladu:</span><span class="sxs-lookup"><span data-stu-id="d978a-676">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="d978a-677">V souboru Konfiguration Nginx `server`  >  `location`  >  `proxy_pass` nastavte `http://unix:/tmp/{KESTREL SOCKET}:/;`položku na .</span><span class="sxs-lookup"><span data-stu-id="d978a-677">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="d978a-678">`{KESTREL SOCKET}`je název soketu k <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> dispozici (například `kestrel-test.sock` v předchozím příkladu).</span><span class="sxs-lookup"><span data-stu-id="d978a-678">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="d978a-679">Ujistěte se, že soket je zapisovatelný Nginx `chmod go+w /tmp/kestrel-test.sock`(například).</span><span class="sxs-lookup"><span data-stu-id="d978a-679">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="d978a-680">Port 0</span><span class="sxs-lookup"><span data-stu-id="d978a-680">Port 0</span></span>

<span data-ttu-id="d978a-681">Je-li `0` zadáno číslo portu, kestrel dynamicky se váže na dostupný port.</span><span class="sxs-lookup"><span data-stu-id="d978a-681">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="d978a-682">Následující příklad ukazuje, jak určit, který port Kestrel skutečně vázán za běhu:</span><span class="sxs-lookup"><span data-stu-id="d978a-682">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="d978a-683">Při spuštění aplikace výstup okna konzoly označuje dynamický port, kde lze aplikaci dosáhnout:</span><span class="sxs-lookup"><span data-stu-id="d978a-683">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="d978a-684">Omezení</span><span class="sxs-lookup"><span data-stu-id="d978a-684">Limitations</span></span>

<span data-ttu-id="d978a-685">Nakonfigurujte koncové body pomocí následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="d978a-685">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="d978a-686">`--urls`argument příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="d978a-686">`--urls` command-line argument</span></span>
* <span data-ttu-id="d978a-687">`urls`konfigurační klíč hostitele</span><span class="sxs-lookup"><span data-stu-id="d978a-687">`urls` host configuration key</span></span>
* <span data-ttu-id="d978a-688">`ASPNETCORE_URLS`proměnná prostředí</span><span class="sxs-lookup"><span data-stu-id="d978a-688">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="d978a-689">Tyto metody jsou užitečné pro práci s kódem s jinými servery než Kestrel.</span><span class="sxs-lookup"><span data-stu-id="d978a-689">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="d978a-690">Mějte však na paměti následující omezení:</span><span class="sxs-lookup"><span data-stu-id="d978a-690">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="d978a-691">Protokol HTTPS nelze použít s těmito přístupy, pokud výchozí certifikát je k `KestrelServerOptions` dispozici v konfiguraci koncového bodu HTTPS (například pomocí konfigurace nebo konfiguračního souboru, jak je uvedeno výše v tomto tématu).</span><span class="sxs-lookup"><span data-stu-id="d978a-691">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="d978a-692">Při obou `Listen` `UseUrls` a přístupy jsou `Listen` používány současně, `UseUrls` koncové body přepsat koncové body.</span><span class="sxs-lookup"><span data-stu-id="d978a-692">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="d978a-693">Konfigurace koncového bodu služby IIS</span><span class="sxs-lookup"><span data-stu-id="d978a-693">IIS endpoint configuration</span></span>

<span data-ttu-id="d978a-694">Při použití služby IIS jsou vazby adres URL pro `Listen` `UseUrls`vazby přepsání služby IIS nastaveny buď nebo .</span><span class="sxs-lookup"><span data-stu-id="d978a-694">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="d978a-695">Další informace naleznete v [tématu ASP.NET základní modul.](xref:host-and-deploy/aspnet-core-module)</span><span class="sxs-lookup"><span data-stu-id="d978a-695">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="d978a-696">ListenOptions.Protocols</span><span class="sxs-lookup"><span data-stu-id="d978a-696">ListenOptions.Protocols</span></span>

<span data-ttu-id="d978a-697">Vlastnost `Protocols` vytvoří protokoly HTTP`HttpProtocols`( ) povolené na koncovém bodu připojení nebo pro server.</span><span class="sxs-lookup"><span data-stu-id="d978a-697">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="d978a-698">Přiřaďte `Protocols` hodnotu `HttpProtocols` vlastnosti z výčtu.</span><span class="sxs-lookup"><span data-stu-id="d978a-698">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="d978a-699">`HttpProtocols`Hodnota výčtu</span><span class="sxs-lookup"><span data-stu-id="d978a-699">`HttpProtocols` enum value</span></span> | <span data-ttu-id="d978a-700">Protokol připojení povolen</span><span class="sxs-lookup"><span data-stu-id="d978a-700">Connection protocol permitted</span></span> |
| -------------------------- | ----------------------------- |
| `Http1`                    | <span data-ttu-id="d978a-701">pouze http/1.1.</span><span class="sxs-lookup"><span data-stu-id="d978a-701">HTTP/1.1 only.</span></span> <span data-ttu-id="d978a-702">Lze použít s TLS nebo bez něj.</span><span class="sxs-lookup"><span data-stu-id="d978a-702">Can be used with or without TLS.</span></span> |
| `Http2`                    | <span data-ttu-id="d978a-703">pouze http/2.</span><span class="sxs-lookup"><span data-stu-id="d978a-703">HTTP/2 only.</span></span> <span data-ttu-id="d978a-704">Bez TLS lze použít pouze v případě, že klient podporuje [režim předchozí znalosti](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="d978a-704">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> |
| `Http1AndHttp2`            | <span data-ttu-id="d978a-705">HTTP/1.1 a HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="d978a-705">HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="d978a-706">Protokol HTTP/2 vyžaduje připojení pro vyjednávání protokolu TLS a [aplikační vrstvy (ALPN).](https://tools.ietf.org/html/rfc7301#section-3) v opačném případě je výchozí připojení http/1.1.</span><span class="sxs-lookup"><span data-stu-id="d978a-706">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="d978a-707">Výchozí protokol je HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="d978a-707">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="d978a-708">Omezení TLS pro HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="d978a-708">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="d978a-709">TLS verze 1.2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="d978a-709">TLS version 1.2 or later</span></span>
* <span data-ttu-id="d978a-710">Opětovné vyjednávání zakázáno.</span><span class="sxs-lookup"><span data-stu-id="d978a-710">Renegotiation disabled</span></span>
* <span data-ttu-id="d978a-711">Komprese zakázána</span><span class="sxs-lookup"><span data-stu-id="d978a-711">Compression disabled</span></span>
* <span data-ttu-id="d978a-712">Minimální velikosti dočasných klíčů:</span><span class="sxs-lookup"><span data-stu-id="d978a-712">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="d978a-713">Eliptická křivka Diffie-Hellman &lbrack;(ECDHE) [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; &ndash; 224 bitů minimum</span><span class="sxs-lookup"><span data-stu-id="d978a-713">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack; &ndash; 224 bits minimum</span></span>
  * <span data-ttu-id="d978a-714">Konečné pole Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; &ndash; minimálně 2048 bitů</span><span class="sxs-lookup"><span data-stu-id="d978a-714">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack; &ndash; 2048 bits minimum</span></span>
* <span data-ttu-id="d978a-715">Šifrovací sada není na černé listině</span><span class="sxs-lookup"><span data-stu-id="d978a-715">Cipher suite not blacklisted</span></span>

<span data-ttu-id="d978a-716">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack; &lbrack; `FIPS186` &rbrack; s eliptickou křivkou P-256 je ve výchozím nastavení podporována. `TLS-ECDHE` &rbrack;</span><span class="sxs-lookup"><span data-stu-id="d978a-716">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="d978a-717">Následující příklad umožňuje připojení HTTP/1.1 a HTTP/2 na portu 8000.</span><span class="sxs-lookup"><span data-stu-id="d978a-717">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="d978a-718">Připojení jsou zabezpečena TLS s dodaným certifikátem:</span><span class="sxs-lookup"><span data-stu-id="d978a-718">Connections are secured by TLS with a supplied certificate:</span></span>

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

<span data-ttu-id="d978a-719">Volitelně vytvořte implementaci `IConnectionAdapter` pro filtrování handshake s TLS pro jednotlivé připojení pro konkrétní šifry:</span><span class="sxs-lookup"><span data-stu-id="d978a-719">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

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

<span data-ttu-id="d978a-720">*Nastavení protokolu z konfigurace*</span><span class="sxs-lookup"><span data-stu-id="d978a-720">*Set the protocol from configuration*</span></span>

<span data-ttu-id="d978a-721"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>ve `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` výchozím nastavení načítá konfiguraci Kestrel.</span><span class="sxs-lookup"><span data-stu-id="d978a-721"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="d978a-722">V následujícím příkladu *appsettings.json* je pro všechny koncové body Kestrelu vytvořen výchozí protokol připojení (HTTP/1.1 a HTTP/2):</span><span class="sxs-lookup"><span data-stu-id="d978a-722">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="d978a-723">Následující příklad konfiguračního souboru vytvoří protokol připojení pro konkrétní koncový bod:</span><span class="sxs-lookup"><span data-stu-id="d978a-723">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="d978a-724">Protokoly zadané v hodnotách přepsání kódu nastavených konfigurací.</span><span class="sxs-lookup"><span data-stu-id="d978a-724">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="d978a-725">Konfigurace přenosu</span><span class="sxs-lookup"><span data-stu-id="d978a-725">Transport configuration</span></span>

<span data-ttu-id="d978a-726">S vydáním ASP.NET Core 2.1, Kestrel výchozí přenos již není založen na Libuv, ale místo toho na spravované sokety.</span><span class="sxs-lookup"><span data-stu-id="d978a-726">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="d978a-727">Toto je zásadní změna pro ASP.NET aplikace Core 2.0 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> upgradující na 2.1, které volají a závisí na jednom z následujících balíčků:</span><span class="sxs-lookup"><span data-stu-id="d978a-727">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="d978a-728">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (přímý odkaz na balíček)</span><span class="sxs-lookup"><span data-stu-id="d978a-728">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="d978a-729">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="d978a-729">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="d978a-730">Pro projekty, které vyžadují použití Libuv:</span><span class="sxs-lookup"><span data-stu-id="d978a-730">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="d978a-731">Přidejte závislost pro balíček [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) do souboru projektu aplikace:</span><span class="sxs-lookup"><span data-stu-id="d978a-731">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="d978a-732">Volejte <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span><span class="sxs-lookup"><span data-stu-id="d978a-732">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="d978a-733">Předpony adres URL</span><span class="sxs-lookup"><span data-stu-id="d978a-733">URL prefixes</span></span>

<span data-ttu-id="d978a-734">Při `UseUrls`použití `--urls` , argument `urls` příkazového řádku, `ASPNETCORE_URLS` konfigurační klíč hostitele nebo proměnné prostředí, předpony URL může být v některém z následujících formátů.</span><span class="sxs-lookup"><span data-stu-id="d978a-734">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="d978a-735">Platné jsou pouze předpony adresy URL HTTP.</span><span class="sxs-lookup"><span data-stu-id="d978a-735">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="d978a-736">Kestrel nepodporuje protokol HTTPS při konfiguraci `UseUrls`vazeb URL pomocí .</span><span class="sxs-lookup"><span data-stu-id="d978a-736">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="d978a-737">Adresa IPv4 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="d978a-737">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="d978a-738">`0.0.0.0`je zvláštní případ, který se váže na všechny adresy IPv4.</span><span class="sxs-lookup"><span data-stu-id="d978a-738">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="d978a-739">Adresa IPv6 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="d978a-739">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="d978a-740">`[::]`je ekvivalent IPv6 iPv4 `0.0.0.0`.</span><span class="sxs-lookup"><span data-stu-id="d978a-740">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="d978a-741">Název hostitele s číslem portu</span><span class="sxs-lookup"><span data-stu-id="d978a-741">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="d978a-742">Názvy `*`hostitelů `+`, a , nejsou zvláštní.</span><span class="sxs-lookup"><span data-stu-id="d978a-742">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="d978a-743">Cokoli, co není rozpoznáno `localhost` jako platná adresa IP nebo se váže na všechny IP adresy IPv4 a IPv6.</span><span class="sxs-lookup"><span data-stu-id="d978a-743">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="d978a-744">Chcete-li svázat různé názvy hostitelů s různými aplikacemi ASP.NET Core na stejném portu, použijte [http.sys](xref:fundamentals/servers/httpsys) nebo reverzní proxy server, jako je Služba IIS, Nginx nebo Apache.</span><span class="sxs-lookup"><span data-stu-id="d978a-744">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="d978a-745">Hostování v konfiguraci reverzní proxy vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="d978a-745">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="d978a-746">Název `localhost` hostitele s číslem portu nebo ip adresou zpětné smyčky s číslem portu</span><span class="sxs-lookup"><span data-stu-id="d978a-746">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="d978a-747">Je-li `localhost` zadáno, kestrel se pokusí vytvořit vazbu na rozhraní zpětné smyčky IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="d978a-747">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="d978a-748">Pokud je požadovaný port používán jinou službou na obou rozhraních zpětné smyčky, kestrel se nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="d978a-748">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="d978a-749">Pokud buď rozhraní zpětné smyčky není k dispozici z jakéhokoli jiného důvodu (nejčastěji proto, že Není podporován protokol Empv6), Kestrel protokoly upozornění.</span><span class="sxs-lookup"><span data-stu-id="d978a-749">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="d978a-750">Filtrování hostitelů</span><span class="sxs-lookup"><span data-stu-id="d978a-750">Host filtering</span></span>

<span data-ttu-id="d978a-751">Zatímco Kestrel podporuje konfiguraci založenou na předponami, jako je například `http://example.com:5000`, Kestrel do značné míry ignoruje název hostitele.</span><span class="sxs-lookup"><span data-stu-id="d978a-751">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="d978a-752">Hostitel `localhost` je zvláštní případ používaný pro vazbu na adresy zpětné smyčky.</span><span class="sxs-lookup"><span data-stu-id="d978a-752">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="d978a-753">Jakýkoli hostitel jiný než explicitní adresa IP se váže na všechny veřejné IP adresy.</span><span class="sxs-lookup"><span data-stu-id="d978a-753">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="d978a-754">`Host`záhlaví nejsou ověřena.</span><span class="sxs-lookup"><span data-stu-id="d978a-754">`Host` headers aren't validated.</span></span>

<span data-ttu-id="d978a-755">Jako řešení použijte middleware filtrování hostitelů.</span><span class="sxs-lookup"><span data-stu-id="d978a-755">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="d978a-756">Middleware filtrování hostitelů poskytuje balíček [Microsoft.AspNetCore.HostFiltering,](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) který je součástí [metabalíčku Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 nebo 2.2).</span><span class="sxs-lookup"><span data-stu-id="d978a-756">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="d978a-757">Middleware je přidán <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>který volá :</span><span class="sxs-lookup"><span data-stu-id="d978a-757">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="d978a-758">Middleware filtrování hostitelů je ve výchozím nastavení zakázáno.</span><span class="sxs-lookup"><span data-stu-id="d978a-758">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="d978a-759">Chcete-li povolit middleware, definujte `AllowedHosts` klíč v *appsettings.json*/*appsettings.\< EnvironmentName>.json*.</span><span class="sxs-lookup"><span data-stu-id="d978a-759">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="d978a-760">Hodnota je seznam názvů hostitelů oddělených středníkem bez čísel portů:</span><span class="sxs-lookup"><span data-stu-id="d978a-760">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="d978a-761">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="d978a-761">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="d978a-762">[Předávaných záhlaví Middleware](xref:host-and-deploy/proxy-load-balancer) má také možnost. <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts></span><span class="sxs-lookup"><span data-stu-id="d978a-762">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="d978a-763">Předané hlavičky Middleware a Host Filtrování Middleware mají podobné funkce pro různé scénáře.</span><span class="sxs-lookup"><span data-stu-id="d978a-763">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="d978a-764">Nastavení `AllowedHosts` pomocí middlewaru s předanými hlavičkami je vhodné, pokud `Host` záhlaví není zachováno při předávání požadavků pomocí reverzního proxy serveru nebo vyrovnávání zatížení.</span><span class="sxs-lookup"><span data-stu-id="d978a-764">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="d978a-765">Nastavení `AllowedHosts` s Middleware filtrování hostitelů je vhodné, pokud je Kestrel `Host` používán jako veřejný hraniční server nebo když je záhlaví přímo předáno dál.</span><span class="sxs-lookup"><span data-stu-id="d978a-765">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="d978a-766">Další informace o middlewaru s <xref:host-and-deploy/proxy-load-balancer>předaných záhlaví naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="d978a-766">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="d978a-767">Kestrel je multiplatformní [webový server pro ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="d978a-767">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="d978a-768">Kestrel je webový server, který je ve výchozím nastavení zahrnut v šablonách projektu ASP.NET Core.</span><span class="sxs-lookup"><span data-stu-id="d978a-768">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="d978a-769">Poštolka podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="d978a-769">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="d978a-770">HTTPS</span><span class="sxs-lookup"><span data-stu-id="d978a-770">HTTPS</span></span>
* <span data-ttu-id="d978a-771">Neprůhledný upgrade používaný k povolení [websocketů](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="d978a-771">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="d978a-772">Unixové zásuvky pro vysoký výkon za Nginx</span><span class="sxs-lookup"><span data-stu-id="d978a-772">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="d978a-773">Kestrel je podporován na všech platformách a verzích, které podporuje .NET Core.</span><span class="sxs-lookup"><span data-stu-id="d978a-773">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="d978a-774">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) [(jak stáhnout)](xref:index#how-to-download-a-sample)</span><span class="sxs-lookup"><span data-stu-id="d978a-774">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="d978a-775">Kdy použít Kestrel s reverzním proxy serverem</span><span class="sxs-lookup"><span data-stu-id="d978a-775">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="d978a-776">Poštolka může být použita sama o sobě nebo s *reverzním proxy serverem*, jako je [Internetová informační služba (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="d978a-776">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="d978a-777">Reverzní proxy server přijímá požadavky HTTP ze sítě a předá je ke strel.</span><span class="sxs-lookup"><span data-stu-id="d978a-777">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="d978a-778">Poštolka používá jako okraj (Internet-čelí) webový server:</span><span class="sxs-lookup"><span data-stu-id="d978a-778">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Poštolka komunikuje přímo s Internetem bez reverzního proxy serveru](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="d978a-780">Poštolka použitá v konfiguraci reverzního proxy serveru:</span><span class="sxs-lookup"><span data-stu-id="d978a-780">Kestrel used in a reverse proxy configuration:</span></span>

![Poštolka komunikuje nepřímo s Internetem prostřednictvím reverzního proxy serveru, jako je služba IIS, Nginx nebo Apache](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="d978a-782">Konfigurace, s reverzním proxy serverem nebo bez něj, je podporovaná konfigurace hostování.</span><span class="sxs-lookup"><span data-stu-id="d978a-782">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="d978a-783">Kestrel používaný jako hraniční server bez reverzního proxy serveru nepodporuje sdílení stejné IP a portu mezi více procesy.</span><span class="sxs-lookup"><span data-stu-id="d978a-783">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="d978a-784">Když kestrel je nakonfigurován pro poslech na portu, Kestrel zpracovává všechny přenosy pro tento port bez ohledu na hlavičky požadavků.When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span><span class="sxs-lookup"><span data-stu-id="d978a-784">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="d978a-785">Reverzní proxy server, který může sdílet porty, má schopnost předávat požadavky Kestrelu na jedinečné IP a portu.</span><span class="sxs-lookup"><span data-stu-id="d978a-785">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="d978a-786">I v případě, že reverzní proxy server není vyžadován, použití reverzní proxy server může být dobrou volbou.</span><span class="sxs-lookup"><span data-stu-id="d978a-786">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="d978a-787">Reverzní proxy server:</span><span class="sxs-lookup"><span data-stu-id="d978a-787">A reverse proxy:</span></span>

* <span data-ttu-id="d978a-788">Může omezit exponované veřejné plochy aplikací, které hostuje.</span><span class="sxs-lookup"><span data-stu-id="d978a-788">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="d978a-789">Poskytněte další vrstvu konfigurace a obrany.</span><span class="sxs-lookup"><span data-stu-id="d978a-789">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="d978a-790">Mohlo by se lépe integrovat se stávající infrastrukturou.</span><span class="sxs-lookup"><span data-stu-id="d978a-790">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="d978a-791">Zjednodušte vyrovnávání zatížení a zabezpečenou komunikaci (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="d978a-791">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="d978a-792">Pouze reverzní proxy server vyžaduje certifikát X.509 a tento server může komunikovat se servery aplikace v interní síti pomocí prostého protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="d978a-792">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="d978a-793">Hostování v konfiguraci reverzní proxy vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="d978a-793">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="d978a-794">Jak používat Kestrel v aplikacích ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d978a-794">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="d978a-795">Balíček [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) je součástí [metabalíčku Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="d978a-795">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="d978a-796">ASP.NET Šablony projektu Core používají kestrel ve výchozím nastavení.</span><span class="sxs-lookup"><span data-stu-id="d978a-796">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="d978a-797">V *Program.cs*volání <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>kódu šablony <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> , který volá na pozadí.</span><span class="sxs-lookup"><span data-stu-id="d978a-797">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="d978a-798">Chcete-li po `CreateDefaultBuilder`zavolání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>poskytnout další konfiguraci , volejte :</span><span class="sxs-lookup"><span data-stu-id="d978a-798">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="d978a-799">Další informace `CreateDefaultBuilder` o hostiteli a jeho vytváření naleznete <xref:fundamentals/host/web-host#set-up-a-host>v části Nastavení *hostitele* v .</span><span class="sxs-lookup"><span data-stu-id="d978a-799">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="d978a-800">Možnosti poštolky</span><span class="sxs-lookup"><span data-stu-id="d978a-800">Kestrel options</span></span>

<span data-ttu-id="d978a-801">Webový server Kestrel má možnosti konfigurace omezení, které jsou užitečné zejména v internetových nasazeních.</span><span class="sxs-lookup"><span data-stu-id="d978a-801">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="d978a-802">Nastavte omezení vlastnosti <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> třídy. <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions></span><span class="sxs-lookup"><span data-stu-id="d978a-802">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="d978a-803">Vlastnost `Limits` obsahuje instanci <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> třídy.</span><span class="sxs-lookup"><span data-stu-id="d978a-803">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="d978a-804">V následujících příkladech se <xref:Microsoft.AspNetCore.Server.Kestrel.Core> používá obor názvů:</span><span class="sxs-lookup"><span data-stu-id="d978a-804">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="d978a-805">Možnosti kestrelu, které jsou konfigurovány v kódu Jazyka C# v následujících příkladech, lze také nastavit pomocí [zprostředkovatele konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="d978a-805">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="d978a-806">Zprostředkovatel konfigurace souborů může například načíst konfiguraci kestrelu z souboru *appsettings.json* nebo *appsettings.{ Prostředí}.json:*</span><span class="sxs-lookup"><span data-stu-id="d978a-806">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="d978a-807">Použijte **jeden** z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="d978a-807">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="d978a-808">Konfigurovat poštolek `Startup.ConfigureServices`v :</span><span class="sxs-lookup"><span data-stu-id="d978a-808">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="d978a-809">Vstříkněte `IConfiguration` `Startup` instanci do třídy.</span><span class="sxs-lookup"><span data-stu-id="d978a-809">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="d978a-810">Následující příklad předpokládá, že vstřikovaná `Configuration` konfigurace je přiřazena vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="d978a-810">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="d978a-811">V `Startup.ConfigureServices`, `Kestrel` načtěte část konfigurace do konfigurace Kestrel:</span><span class="sxs-lookup"><span data-stu-id="d978a-811">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="d978a-812">Konfigurace kestrelu při vytváření hostitele:</span><span class="sxs-lookup"><span data-stu-id="d978a-812">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="d978a-813">V *Program.cs*načtěte `Kestrel` část konfigurace do konfigurace Kestrelu:</span><span class="sxs-lookup"><span data-stu-id="d978a-813">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="d978a-814">Oba předchozí přístupy pracují s libovolným [zprostředkovatelem konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="d978a-814">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="d978a-815">Časový limit udržování života</span><span class="sxs-lookup"><span data-stu-id="d978a-815">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="d978a-816">Získá nebo nastaví [časový limit keep-alive](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="d978a-816">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="d978a-817">Výchozí hodnota je 2 minuty.</span><span class="sxs-lookup"><span data-stu-id="d978a-817">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="d978a-818">Maximální počet připojení klientů</span><span class="sxs-lookup"><span data-stu-id="d978a-818">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="d978a-819">Maximální počet souběžných otevřených připojení TCP lze nastavit pro celou aplikaci s následujícím kódem:</span><span class="sxs-lookup"><span data-stu-id="d978a-819">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="d978a-820">Existuje samostatné omezení pro připojení, která byla upgradována z protokolu HTTP nebo HTTPS na jiný protokol (například na požadavek WebSockets).</span><span class="sxs-lookup"><span data-stu-id="d978a-820">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="d978a-821">Po upgradu připojení se nezapočítává `MaxConcurrentConnections` do limitu.</span><span class="sxs-lookup"><span data-stu-id="d978a-821">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="d978a-822">Maximální počet připojení je ve výchozím nastavení neomezený (null).</span><span class="sxs-lookup"><span data-stu-id="d978a-822">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="d978a-823">Maximální velikost těla požadavku</span><span class="sxs-lookup"><span data-stu-id="d978a-823">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="d978a-824">Výchozí maximální velikost těla požadavku je 30 000 000 bajtů, což je přibližně 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="d978a-824">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="d978a-825">Doporučený přístup k přepsání limitu v aplikaci ASP.NET <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> Core MVC je použití atributu na metodě akce:</span><span class="sxs-lookup"><span data-stu-id="d978a-825">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="d978a-826">Tady je příklad, který ukazuje, jak nakonfigurovat omezení pro aplikaci při každém požadavku:</span><span class="sxs-lookup"><span data-stu-id="d978a-826">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="d978a-827">Přepište nastavení na konkrétní požadavek v middlewaru:</span><span class="sxs-lookup"><span data-stu-id="d978a-827">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="d978a-828">Výjimka je vyvolána, pokud aplikace konfiguruje limit na požadavek poté, co aplikace začala číst požadavek.</span><span class="sxs-lookup"><span data-stu-id="d978a-828">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="d978a-829">Je `IsReadOnly` vlastnost, která označuje, `MaxRequestBodySize` pokud je vlastnost ve stavu jen pro čtení, což znamená, že je příliš pozdě na konfiguraci limitu.</span><span class="sxs-lookup"><span data-stu-id="d978a-829">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="d978a-830">Pokud je aplikace [spuštěna mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za ASP.NET [základnímodul](xref:host-and-deploy/aspnet-core-module), kestrel požadavek body limit je zakázán, protože iis již nastaví limit.</span><span class="sxs-lookup"><span data-stu-id="d978a-830">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="d978a-831">Minimální přenosová rychlost tělo požadavku</span><span class="sxs-lookup"><span data-stu-id="d978a-831">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="d978a-832">Poštolka kontroluje každou sekundu, pokud data přicházejí na zadanou rychlost v bajtů za sekundu.</span><span class="sxs-lookup"><span data-stu-id="d978a-832">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="d978a-833">Pokud rychlost klesne pod minimum, je časový limit připojení. Doba odkladu je doba, kterou Kestrel dává klientovi ke zvýšení míry odeslání až na minimum; během této doby není sazba kontrolována.</span><span class="sxs-lookup"><span data-stu-id="d978a-833">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="d978a-834">Období odkladu pomáhá zabránit přetažení připojení, které jsou zpočátku odesílání dat pomalým tempem z důvodu tcp pomalé spuštění.</span><span class="sxs-lookup"><span data-stu-id="d978a-834">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="d978a-835">Výchozí minimální sazba je 240 bajtů za sekundu s 5 sekundovou lhůtou odkladu.</span><span class="sxs-lookup"><span data-stu-id="d978a-835">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="d978a-836">Minimální sazba se vztahuje také na odpověď.</span><span class="sxs-lookup"><span data-stu-id="d978a-836">A minimum rate also applies to the response.</span></span> <span data-ttu-id="d978a-837">Kód pro nastavení limitu požadavku a limitu odpovědi `RequestBody` `Response` je stejný s výjimkou s nebo v názvy vlastností a rozhraní.</span><span class="sxs-lookup"><span data-stu-id="d978a-837">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="d978a-838">Zde je příklad, který ukazuje, jak nakonfigurovat minimální rychlost dat v *Program.cs*:</span><span class="sxs-lookup"><span data-stu-id="d978a-838">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

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

### <a name="request-headers-timeout"></a><span data-ttu-id="d978a-839">Časový výtažek záhlaví požadavku</span><span class="sxs-lookup"><span data-stu-id="d978a-839">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="d978a-840">Získá nebo nastaví maximální množství času, které server stráví příjem hlavičky požadavku.</span><span class="sxs-lookup"><span data-stu-id="d978a-840">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="d978a-841">Výchozí hodnota je 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="d978a-841">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="d978a-842">Synchronní vstupně-výstupní operace</span><span class="sxs-lookup"><span data-stu-id="d978a-842">Synchronous I/O</span></span>

<span data-ttu-id="d978a-843"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>určuje, zda je pro požadavek a odpověď povolena synchronní vstupně-va.</span><span class="sxs-lookup"><span data-stu-id="d978a-843"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="d978a-844">Výchozí hodnota `true`je .</span><span class="sxs-lookup"><span data-stu-id="d978a-844">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="d978a-845">Velký počet blokování synchronní vstupně-va operace může vést k nedostatku fondu vláken, což způsobí, že aplikace nereaguje.</span><span class="sxs-lookup"><span data-stu-id="d978a-845">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="d978a-846">Povolit `AllowSynchronousIO` pouze při použití knihovny, která nepodporuje asynchronní vstupně-v.A/O.</span><span class="sxs-lookup"><span data-stu-id="d978a-846">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="d978a-847">Následující příklad zakáže synchronní vstupně-va:</span><span class="sxs-lookup"><span data-stu-id="d978a-847">The following example disables synchronous I/O:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="d978a-848">Informace o dalších možnostech a limitech kestrelu naleznete v těchto tématech:</span><span class="sxs-lookup"><span data-stu-id="d978a-848">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="d978a-849">Konfigurace koncového bodu</span><span class="sxs-lookup"><span data-stu-id="d978a-849">Endpoint configuration</span></span>

<span data-ttu-id="d978a-850">Ve výchozím nastavení se ASP.NET jádro váže na:</span><span class="sxs-lookup"><span data-stu-id="d978a-850">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="d978a-851">`https://localhost:5001`(pokud je k dispozici certifikát místního rozvoje)</span><span class="sxs-lookup"><span data-stu-id="d978a-851">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="d978a-852">Zadejte adresy URL pomocí:</span><span class="sxs-lookup"><span data-stu-id="d978a-852">Specify URLs using the:</span></span>

* <span data-ttu-id="d978a-853">`ASPNETCORE_URLS`proměnné prostředí.</span><span class="sxs-lookup"><span data-stu-id="d978a-853">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="d978a-854">`--urls`argument příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="d978a-854">`--urls` command-line argument.</span></span>
* <span data-ttu-id="d978a-855">`urls`konfiguračního klíče hostitele.</span><span class="sxs-lookup"><span data-stu-id="d978a-855">`urls` host configuration key.</span></span>
* <span data-ttu-id="d978a-856">`UseUrls`rozšíření.</span><span class="sxs-lookup"><span data-stu-id="d978a-856">`UseUrls` extension method.</span></span>

<span data-ttu-id="d978a-857">Hodnota poskytovaná pomocí těchto přístupů může být jeden nebo více koncových bodů HTTP a HTTPS (HTTPS, pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="d978a-857">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="d978a-858">Nakonfigurujte hodnotu jako seznam oddělený `"Urls": "http://localhost:8000;http://localhost:8001"`středníkem (například).</span><span class="sxs-lookup"><span data-stu-id="d978a-858">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="d978a-859">Další informace o těchto přístupech naleznete v [tématu Adresy URL serveru](xref:fundamentals/host/web-host#server-urls) a [konfigurace override](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="d978a-859">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="d978a-860">Je vytvořen vývojový certifikát:</span><span class="sxs-lookup"><span data-stu-id="d978a-860">A development certificate is created:</span></span>

* <span data-ttu-id="d978a-861">Při instalaci sady [.NET Core SDK.](/dotnet/core/sdk)</span><span class="sxs-lookup"><span data-stu-id="d978a-861">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="d978a-862">Nástroj [dev-certs](xref:aspnetcore-2.1#https) se používá k vytvoření certifikátu.</span><span class="sxs-lookup"><span data-stu-id="d978a-862">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="d978a-863">Některé prohlížeče vyžadují udělení výslovného oprávnění důvěřovat certifikátu místního vývoje.</span><span class="sxs-lookup"><span data-stu-id="d978a-863">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="d978a-864">Šablony projektů konfigurují aplikace tak, aby se ve výchozím nastavení spouštěly na protokolu HTTPS a zahrnovaly [přesměrování HTTPS a podporu HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="d978a-864">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="d978a-865">Volání <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> nebo <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> metody na konfiguraci URL předpony a porty pro Kestrel.</span><span class="sxs-lookup"><span data-stu-id="d978a-865">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="d978a-866">`UseUrls`, `--urls` argument příkazového `urls` řádku, konfigurační klíč hostitele a proměnná `ASPNETCORE_URLS` prostředí také fungují, ale omezení jsou zaznamenána dále v této části (výchozí certifikát musí být k dispozici pro konfiguraci koncového bodu HTTPS).</span><span class="sxs-lookup"><span data-stu-id="d978a-866">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="d978a-867">`KestrelServerOptions`Konfigurace:</span><span class="sxs-lookup"><span data-stu-id="d978a-867">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="d978a-868">ConfigureEndpointDefaults (akce\<ListenOptions>)</span><span class="sxs-lookup"><span data-stu-id="d978a-868">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="d978a-869">Určuje konfiguraci, `Action` která má být spuštěna pro každý zadaný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="d978a-869">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="d978a-870">Volání `ConfigureEndpointDefaults` vícekrát nahradí `Action`předchozí s `Action` posledním zadaným.</span><span class="sxs-lookup"><span data-stu-id="d978a-870">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="d978a-871">Koncové body vytvořené <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> voláním **před** voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> nebudou mít výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="d978a-871">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="d978a-872">KonfigurovatHttpsDefaults (akce\<HttpsConnectionAdapterOptions>)</span><span class="sxs-lookup"><span data-stu-id="d978a-872">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="d978a-873">Určuje konfiguraci, `Action` která má být spuštěna pro každý koncový bod HTTPS.</span><span class="sxs-lookup"><span data-stu-id="d978a-873">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="d978a-874">Volání `ConfigureHttpsDefaults` vícekrát nahradí `Action`předchozí s `Action` posledním zadaným.</span><span class="sxs-lookup"><span data-stu-id="d978a-874">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="d978a-875">Koncové body vytvořené <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> voláním **před** voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> nebudou mít výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="d978a-875">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="d978a-876">Configure(IKonfigurace)</span><span class="sxs-lookup"><span data-stu-id="d978a-876">Configure(IConfiguration)</span></span>

<span data-ttu-id="d978a-877">Vytvoří konfigurační zavaděč pro <xref:Microsoft.Extensions.Configuration.IConfiguration> nastavení Kestrel, který bere jako vstup.</span><span class="sxs-lookup"><span data-stu-id="d978a-877">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="d978a-878">Konfigurace musí být vymezena na konfigurační sekci pro Kestrel.</span><span class="sxs-lookup"><span data-stu-id="d978a-878">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="d978a-879">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="d978a-879">ListenOptions.UseHttps</span></span>

<span data-ttu-id="d978a-880">Nakonfigurujte Kestrel pro použití protokolu HTTPS.</span><span class="sxs-lookup"><span data-stu-id="d978a-880">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="d978a-881">`ListenOptions.UseHttps`Rozšíření:</span><span class="sxs-lookup"><span data-stu-id="d978a-881">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="d978a-882">`UseHttps`&ndash; Nakonfigurujte Kestrel tak, aby používal protokol HTTPS s výchozím certifikátem.</span><span class="sxs-lookup"><span data-stu-id="d978a-882">`UseHttps` &ndash; Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="d978a-883">Vyvolá výjimku, pokud není nakonfigurován žádný výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="d978a-883">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="d978a-884">`ListenOptions.UseHttps`Parametry:</span><span class="sxs-lookup"><span data-stu-id="d978a-884">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="d978a-885">`filename`je cesta a název souboru certifikátu vzhledem k adresáři, který obsahuje soubory obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="d978a-885">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="d978a-886">`password`je heslo potřebné pro přístup k datům certifikátu X.509.</span><span class="sxs-lookup"><span data-stu-id="d978a-886">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="d978a-887">`configureOptions`je `Action` konfigurace rozhraní `HttpsConnectionAdapterOptions`.</span><span class="sxs-lookup"><span data-stu-id="d978a-887">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="d978a-888">Vrátí `ListenOptions`hodnotu .</span><span class="sxs-lookup"><span data-stu-id="d978a-888">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="d978a-889">`storeName`je úložiště certifikátů, ze kterého se má certifikát načíst.</span><span class="sxs-lookup"><span data-stu-id="d978a-889">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="d978a-890">`subject`je název subjektu certifikátu.</span><span class="sxs-lookup"><span data-stu-id="d978a-890">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="d978a-891">`allowInvalid`označuje, zda by měly být považovány za neplatné certifikáty, například certifikáty podepsané svým držitelem.</span><span class="sxs-lookup"><span data-stu-id="d978a-891">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="d978a-892">`location`je umístění úložiště, ze kterých se má certifikát načíst.</span><span class="sxs-lookup"><span data-stu-id="d978a-892">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="d978a-893">`serverCertificate`je certifikát X.509.</span><span class="sxs-lookup"><span data-stu-id="d978a-893">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="d978a-894">V produkčním prostředí musí být protokol HTTPS explicitně nakonfigurován.</span><span class="sxs-lookup"><span data-stu-id="d978a-894">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="d978a-895">Musí být poskytnut minimálně výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="d978a-895">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="d978a-896">Podporované konfigurace popsané dále:</span><span class="sxs-lookup"><span data-stu-id="d978a-896">Supported configurations described next:</span></span>

* <span data-ttu-id="d978a-897">Žádná konfigurace</span><span class="sxs-lookup"><span data-stu-id="d978a-897">No configuration</span></span>
* <span data-ttu-id="d978a-898">Nahrazení výchozího certifikátu z konfigurace</span><span class="sxs-lookup"><span data-stu-id="d978a-898">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="d978a-899">Změna výchozích hodnot v kódu</span><span class="sxs-lookup"><span data-stu-id="d978a-899">Change the defaults in code</span></span>

<span data-ttu-id="d978a-900">*Žádná konfigurace*</span><span class="sxs-lookup"><span data-stu-id="d978a-900">*No configuration*</span></span>

<span data-ttu-id="d978a-901">Poštolka naslouchá `http://localhost:5000` dál `https://localhost:5001` a (pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="d978a-901">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="d978a-902">*Nahrazení výchozího certifikátu z konfigurace*</span><span class="sxs-lookup"><span data-stu-id="d978a-902">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="d978a-903">`CreateDefaultBuilder`ve `Configure(context.Configuration.GetSection("Kestrel"))` výchozím nastavení načítá konfiguraci Kestrel.</span><span class="sxs-lookup"><span data-stu-id="d978a-903">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="d978a-904">Pro Kestrel je k dispozici výchozí schéma konfigurace nastavení aplikace HTTPS.</span><span class="sxs-lookup"><span data-stu-id="d978a-904">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="d978a-905">Nakonfigurujte více koncových bodů, včetně adres URL a certifikátů, které mají být používány, ze souboru na disku nebo z úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="d978a-905">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="d978a-906">V následujícím příkladu *appsettings.json:*</span><span class="sxs-lookup"><span data-stu-id="d978a-906">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="d978a-907">Nastavte **AllowInvalid** na `true` povolení použití neplatných certifikátů (například certifikátů podepsaných svým držitelem).</span><span class="sxs-lookup"><span data-stu-id="d978a-907">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="d978a-908">Libovolný koncový bod HTTPS, který neurčuje certifikát (**HttpsDefaultCert** v následujícím příkladu) přejde zpět na certifikát definovaný v části **Výchozí** **certifikáty** > nebo vývojový certifikát.</span><span class="sxs-lookup"><span data-stu-id="d978a-908">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="d978a-909">Alternativou k použití **cesty** a **hesla** pro libovolný uzel certifikátu je určení certifikátu pomocí polí úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="d978a-909">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="d978a-910">**Například certifikát výchozí** certifikát **y** > lze zadat jako:</span><span class="sxs-lookup"><span data-stu-id="d978a-910">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="d978a-911">Poznámky schématu:</span><span class="sxs-lookup"><span data-stu-id="d978a-911">Schema notes:</span></span>

* <span data-ttu-id="d978a-912">Názvy koncových bodů nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="d978a-912">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="d978a-913">Například `HTTPS` a `Https` jsou platné.</span><span class="sxs-lookup"><span data-stu-id="d978a-913">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="d978a-914">Parametr `Url` je vyžadován pro každý koncový bod.</span><span class="sxs-lookup"><span data-stu-id="d978a-914">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="d978a-915">Formát tohoto parametru je stejný jako `Urls` parametr konfigurace nejvyšší úrovně s tím rozdílem, že je omezen na jednu hodnotu.</span><span class="sxs-lookup"><span data-stu-id="d978a-915">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="d978a-916">Tyto koncové body nahradit ty definované `Urls` v konfiguraci nejvyšší úrovně, nikoli jejich přidání.</span><span class="sxs-lookup"><span data-stu-id="d978a-916">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="d978a-917">Koncové body definované `Listen` v kódu prostřednictvím jsou kumulativní s koncovými body definovanými v konfigurační části.</span><span class="sxs-lookup"><span data-stu-id="d978a-917">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="d978a-918">Sekce `Certificate` je nepovinná.</span><span class="sxs-lookup"><span data-stu-id="d978a-918">The `Certificate` section is optional.</span></span> <span data-ttu-id="d978a-919">Pokud `Certificate` oddíl není zadán, použijí se výchozí hodnoty definované v předchozích scénářích.</span><span class="sxs-lookup"><span data-stu-id="d978a-919">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="d978a-920">Pokud nejsou k dispozici žádné výchozí hodnoty, server vyvolá výjimku a nepodaří se spustit.</span><span class="sxs-lookup"><span data-stu-id="d978a-920">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="d978a-921">V `Certificate` této části jsou certifikáty**hesel** **cesty**&ndash;i**úložiště** **předmětů.**&ndash;</span><span class="sxs-lookup"><span data-stu-id="d978a-921">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="d978a-922">Tímto způsobem může být definován libovolný počet koncových bodů, pokud nezpůsobí konflikty portů.</span><span class="sxs-lookup"><span data-stu-id="d978a-922">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="d978a-923">`options.Configure(context.Configuration.GetSection("{SECTION}"))`vrátí `KestrelConfigurationLoader` metodu `.Endpoint(string name, listenOptions => { })` a s metodou, kterou lze použít k doplnění nastavení nakonfigurovaného koncového bodu:</span><span class="sxs-lookup"><span data-stu-id="d978a-923">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="d978a-924">`KestrelServerOptions.ConfigurationLoader`lze přímo přistupovat k pokračování iterace na stávajícím nakladači, jako je například ten, který <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>poskytuje .</span><span class="sxs-lookup"><span data-stu-id="d978a-924">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="d978a-925">Konfigurační oddíl pro každý koncový bod `Endpoint` je k dispozici na možnosti v metodě tak, aby vlastní nastavení může být číst.</span><span class="sxs-lookup"><span data-stu-id="d978a-925">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="d978a-926">Více konfigurací může být `options.Configure(context.Configuration.GetSection("{SECTION}"))` načteno voláním znovu s jiným oddílem.</span><span class="sxs-lookup"><span data-stu-id="d978a-926">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="d978a-927">Používá se pouze poslední `Load` konfigurace, pokud není explicitně volána na předchozí instance.</span><span class="sxs-lookup"><span data-stu-id="d978a-927">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="d978a-928">Metabalíček nevolá, `Load` takže jeho výchozí konfigurační část může být nahrazena.</span><span class="sxs-lookup"><span data-stu-id="d978a-928">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="d978a-929">`KestrelConfigurationLoader`zrcadlí `Listen` rodinu API z `KestrelServerOptions` `Endpoint` jako přetížení, takže kód a koncové body konfigurace mohou být nakonfigurovány na stejném místě.</span><span class="sxs-lookup"><span data-stu-id="d978a-929">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="d978a-930">Tato přetížení nepoužívají názvy a spotřebovávají pouze výchozí nastavení z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="d978a-930">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="d978a-931">*Změna výchozích hodnot v kódu*</span><span class="sxs-lookup"><span data-stu-id="d978a-931">*Change the defaults in code*</span></span>

<span data-ttu-id="d978a-932">`ConfigureEndpointDefaults`a `ConfigureHttpsDefaults` lze použít ke změně `ListenOptions` `HttpsConnectionAdapterOptions`výchozího nastavení pro a , včetně přepsání výchozího certifikátu zadaného v předchozím scénáři.</span><span class="sxs-lookup"><span data-stu-id="d978a-932">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="d978a-933">`ConfigureEndpointDefaults`a `ConfigureHttpsDefaults` měla by být volána před konfigurací všech koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="d978a-933">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="d978a-934">*Podpora pro Kestrel pro SNI*</span><span class="sxs-lookup"><span data-stu-id="d978a-934">*Kestrel support for SNI*</span></span>

<span data-ttu-id="d978a-935">[Označení názvu serveru (SNI)](https://tools.ietf.org/html/rfc6066#section-3) lze použít k hostování více domén na stejné adrese IP a portu.</span><span class="sxs-lookup"><span data-stu-id="d978a-935">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="d978a-936">Aby sni fungovalo, klient odešle název hostitele pro zabezpečenou relaci na server během tls handshake tak, aby server mohl poskytnout správný certifikát.</span><span class="sxs-lookup"><span data-stu-id="d978a-936">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="d978a-937">Klient používá zařízený certifikát pro šifrovanou komunikaci se serverem během zabezpečené relace, která následuje po handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="d978a-937">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="d978a-938">Kestrel podporuje SNI `ServerCertificateSelector` prostřednictvím zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="d978a-938">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="d978a-939">Zpětné volání je vyvoláno jednou za připojení, aby aplikace mohla zkontrolovat název hostitele a vybrat příslušný certifikát.</span><span class="sxs-lookup"><span data-stu-id="d978a-939">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="d978a-940">Podpora SNI vyžaduje:</span><span class="sxs-lookup"><span data-stu-id="d978a-940">SNI support requires:</span></span>

* <span data-ttu-id="d978a-941">Spuštění na `netcoreapp2.1` cílové rozhraní nebo později.</span><span class="sxs-lookup"><span data-stu-id="d978a-941">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="d978a-942">Na `net461` nebo novější zpětné volání je `name` vyvolána, ale je vždy `null`.</span><span class="sxs-lookup"><span data-stu-id="d978a-942">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="d978a-943">Je `name` také `null` v případě, že klient neposkytuje parametr název hostitele v tls handshake.</span><span class="sxs-lookup"><span data-stu-id="d978a-943">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="d978a-944">Všechny webové stránky běží na stejné instanci Kestrel.</span><span class="sxs-lookup"><span data-stu-id="d978a-944">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="d978a-945">Kestrel nepodporuje sdílení IP adresy a portu ve více instancích bez reverzního proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="d978a-945">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="d978a-946">Protokolování připojení</span><span class="sxs-lookup"><span data-stu-id="d978a-946">Connection logging</span></span>

<span data-ttu-id="d978a-947">Volání <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> vyzařovat protokoly úrovně ladění pro komunikaci na úrovni bajtů na připojení.</span><span class="sxs-lookup"><span data-stu-id="d978a-947">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="d978a-948">Protokolování připojení je užitečné při řešení problémů v nízkoúrovňové komunikaci, například během šifrování TLS a za proxy servery.</span><span class="sxs-lookup"><span data-stu-id="d978a-948">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="d978a-949">Pokud `UseConnectionLogging` je `UseHttps`umístěn před , šifrovaný provoz je zaznamenána.</span><span class="sxs-lookup"><span data-stu-id="d978a-949">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="d978a-950">Pokud `UseConnectionLogging` je `UseHttps`umístěn po , dešifrovaný provoz je zaznamenána.</span><span class="sxs-lookup"><span data-stu-id="d978a-950">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="d978a-951">Vazba na soket TCP</span><span class="sxs-lookup"><span data-stu-id="d978a-951">Bind to a TCP socket</span></span>

<span data-ttu-id="d978a-952">Metoda <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> se váže na soket TCP a možnosti lambda umožňuje konfiguraci certifikátu X.509:</span><span class="sxs-lookup"><span data-stu-id="d978a-952">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

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

<span data-ttu-id="d978a-953">Příklad konfiguruje protokol <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>HTTPS pro koncový bod s aplikací .</span><span class="sxs-lookup"><span data-stu-id="d978a-953">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="d978a-954">Stejné rozhraní API použijte ke konfiguraci dalších nastavení kestrelu pro konkrétní koncové body.</span><span class="sxs-lookup"><span data-stu-id="d978a-954">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="d978a-955">Vazba na unixovou zásuvku</span><span class="sxs-lookup"><span data-stu-id="d978a-955">Bind to a Unix socket</span></span>

<span data-ttu-id="d978a-956">Poslouchejte na unixovém soketu s <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> pro lepší výkon s Nginx, jak je znázorněno v tomto příkladu:</span><span class="sxs-lookup"><span data-stu-id="d978a-956">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

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

* <span data-ttu-id="d978a-957">V souboru Konfiguration Nginx `server`  >  `location`  >  `proxy_pass` nastavte `http://unix:/tmp/{KESTREL SOCKET}:/;`položku na .</span><span class="sxs-lookup"><span data-stu-id="d978a-957">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="d978a-958">`{KESTREL SOCKET}`je název soketu k <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> dispozici (například `kestrel-test.sock` v předchozím příkladu).</span><span class="sxs-lookup"><span data-stu-id="d978a-958">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="d978a-959">Ujistěte se, že soket je zapisovatelný Nginx `chmod go+w /tmp/kestrel-test.sock`(například).</span><span class="sxs-lookup"><span data-stu-id="d978a-959">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="d978a-960">Port 0</span><span class="sxs-lookup"><span data-stu-id="d978a-960">Port 0</span></span>

<span data-ttu-id="d978a-961">Je-li `0` zadáno číslo portu, kestrel dynamicky se váže na dostupný port.</span><span class="sxs-lookup"><span data-stu-id="d978a-961">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="d978a-962">Následující příklad ukazuje, jak určit, který port Kestrel skutečně vázán za běhu:</span><span class="sxs-lookup"><span data-stu-id="d978a-962">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="d978a-963">Při spuštění aplikace výstup okna konzoly označuje dynamický port, kde lze aplikaci dosáhnout:</span><span class="sxs-lookup"><span data-stu-id="d978a-963">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="d978a-964">Omezení</span><span class="sxs-lookup"><span data-stu-id="d978a-964">Limitations</span></span>

<span data-ttu-id="d978a-965">Nakonfigurujte koncové body pomocí následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="d978a-965">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="d978a-966">`--urls`argument příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="d978a-966">`--urls` command-line argument</span></span>
* <span data-ttu-id="d978a-967">`urls`konfigurační klíč hostitele</span><span class="sxs-lookup"><span data-stu-id="d978a-967">`urls` host configuration key</span></span>
* <span data-ttu-id="d978a-968">`ASPNETCORE_URLS`proměnná prostředí</span><span class="sxs-lookup"><span data-stu-id="d978a-968">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="d978a-969">Tyto metody jsou užitečné pro práci s kódem s jinými servery než Kestrel.</span><span class="sxs-lookup"><span data-stu-id="d978a-969">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="d978a-970">Mějte však na paměti následující omezení:</span><span class="sxs-lookup"><span data-stu-id="d978a-970">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="d978a-971">Protokol HTTPS nelze použít s těmito přístupy, pokud výchozí certifikát je k `KestrelServerOptions` dispozici v konfiguraci koncového bodu HTTPS (například pomocí konfigurace nebo konfiguračního souboru, jak je uvedeno výše v tomto tématu).</span><span class="sxs-lookup"><span data-stu-id="d978a-971">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="d978a-972">Při obou `Listen` `UseUrls` a přístupy jsou `Listen` používány současně, `UseUrls` koncové body přepsat koncové body.</span><span class="sxs-lookup"><span data-stu-id="d978a-972">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="d978a-973">Konfigurace koncového bodu služby IIS</span><span class="sxs-lookup"><span data-stu-id="d978a-973">IIS endpoint configuration</span></span>

<span data-ttu-id="d978a-974">Při použití služby IIS jsou vazby adres URL pro `Listen` `UseUrls`vazby přepsání služby IIS nastaveny buď nebo .</span><span class="sxs-lookup"><span data-stu-id="d978a-974">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="d978a-975">Další informace naleznete v [tématu ASP.NET základní modul.](xref:host-and-deploy/aspnet-core-module)</span><span class="sxs-lookup"><span data-stu-id="d978a-975">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="d978a-976">Konfigurace přenosu</span><span class="sxs-lookup"><span data-stu-id="d978a-976">Transport configuration</span></span>

<span data-ttu-id="d978a-977">S vydáním ASP.NET Core 2.1, Kestrel výchozí přenos již není založen na Libuv, ale místo toho na spravované sokety.</span><span class="sxs-lookup"><span data-stu-id="d978a-977">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="d978a-978">Toto je zásadní změna pro ASP.NET aplikace Core 2.0 <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> upgradující na 2.1, které volají a závisí na jednom z následujících balíčků:</span><span class="sxs-lookup"><span data-stu-id="d978a-978">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="d978a-979">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (přímý odkaz na balíček)</span><span class="sxs-lookup"><span data-stu-id="d978a-979">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="d978a-980">Microsoft.AspNetCore.App</span><span class="sxs-lookup"><span data-stu-id="d978a-980">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="d978a-981">Pro projekty, které vyžadují použití Libuv:</span><span class="sxs-lookup"><span data-stu-id="d978a-981">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="d978a-982">Přidejte závislost pro balíček [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) do souboru projektu aplikace:</span><span class="sxs-lookup"><span data-stu-id="d978a-982">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="d978a-983">Volejte <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span><span class="sxs-lookup"><span data-stu-id="d978a-983">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="d978a-984">Předpony adres URL</span><span class="sxs-lookup"><span data-stu-id="d978a-984">URL prefixes</span></span>

<span data-ttu-id="d978a-985">Při `UseUrls`použití `--urls` , argument `urls` příkazového řádku, `ASPNETCORE_URLS` konfigurační klíč hostitele nebo proměnné prostředí, předpony URL může být v některém z následujících formátů.</span><span class="sxs-lookup"><span data-stu-id="d978a-985">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="d978a-986">Platné jsou pouze předpony adresy URL HTTP.</span><span class="sxs-lookup"><span data-stu-id="d978a-986">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="d978a-987">Kestrel nepodporuje protokol HTTPS při konfiguraci `UseUrls`vazeb URL pomocí .</span><span class="sxs-lookup"><span data-stu-id="d978a-987">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="d978a-988">Adresa IPv4 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="d978a-988">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="d978a-989">`0.0.0.0`je zvláštní případ, který se váže na všechny adresy IPv4.</span><span class="sxs-lookup"><span data-stu-id="d978a-989">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="d978a-990">Adresa IPv6 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="d978a-990">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="d978a-991">`[::]`je ekvivalent IPv6 iPv4 `0.0.0.0`.</span><span class="sxs-lookup"><span data-stu-id="d978a-991">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="d978a-992">Název hostitele s číslem portu</span><span class="sxs-lookup"><span data-stu-id="d978a-992">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="d978a-993">Názvy `*`hostitelů `+`, a , nejsou zvláštní.</span><span class="sxs-lookup"><span data-stu-id="d978a-993">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="d978a-994">Cokoli, co není rozpoznáno `localhost` jako platná adresa IP nebo se váže na všechny IP adresy IPv4 a IPv6.</span><span class="sxs-lookup"><span data-stu-id="d978a-994">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="d978a-995">Chcete-li svázat různé názvy hostitelů s různými aplikacemi ASP.NET Core na stejném portu, použijte [http.sys](xref:fundamentals/servers/httpsys) nebo reverzní proxy server, jako je Služba IIS, Nginx nebo Apache.</span><span class="sxs-lookup"><span data-stu-id="d978a-995">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="d978a-996">Hostování v konfiguraci reverzní proxy vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="d978a-996">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="d978a-997">Název `localhost` hostitele s číslem portu nebo ip adresou zpětné smyčky s číslem portu</span><span class="sxs-lookup"><span data-stu-id="d978a-997">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="d978a-998">Je-li `localhost` zadáno, kestrel se pokusí vytvořit vazbu na rozhraní zpětné smyčky IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="d978a-998">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="d978a-999">Pokud je požadovaný port používán jinou službou na obou rozhraních zpětné smyčky, kestrel se nepodaří spustit.</span><span class="sxs-lookup"><span data-stu-id="d978a-999">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="d978a-1000">Pokud buď rozhraní zpětné smyčky není k dispozici z jakéhokoli jiného důvodu (nejčastěji proto, že Není podporován protokol Empv6), Kestrel protokoly upozornění.</span><span class="sxs-lookup"><span data-stu-id="d978a-1000">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="d978a-1001">Filtrování hostitelů</span><span class="sxs-lookup"><span data-stu-id="d978a-1001">Host filtering</span></span>

<span data-ttu-id="d978a-1002">Zatímco Kestrel podporuje konfiguraci založenou na předponami, jako je například `http://example.com:5000`, Kestrel do značné míry ignoruje název hostitele.</span><span class="sxs-lookup"><span data-stu-id="d978a-1002">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="d978a-1003">Hostitel `localhost` je zvláštní případ používaný pro vazbu na adresy zpětné smyčky.</span><span class="sxs-lookup"><span data-stu-id="d978a-1003">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="d978a-1004">Jakýkoli hostitel jiný než explicitní adresa IP se váže na všechny veřejné IP adresy.</span><span class="sxs-lookup"><span data-stu-id="d978a-1004">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="d978a-1005">`Host`záhlaví nejsou ověřena.</span><span class="sxs-lookup"><span data-stu-id="d978a-1005">`Host` headers aren't validated.</span></span>

<span data-ttu-id="d978a-1006">Jako řešení použijte middleware filtrování hostitelů.</span><span class="sxs-lookup"><span data-stu-id="d978a-1006">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="d978a-1007">Middleware filtrování hostitelů poskytuje balíček [Microsoft.AspNetCore.HostFiltering,](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) který je součástí [metabalíčku Microsoft.AspNetCore.App](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 nebo 2.2).</span><span class="sxs-lookup"><span data-stu-id="d978a-1007">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="d978a-1008">Middleware je přidán <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>který volá :</span><span class="sxs-lookup"><span data-stu-id="d978a-1008">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="d978a-1009">Middleware filtrování hostitelů je ve výchozím nastavení zakázáno.</span><span class="sxs-lookup"><span data-stu-id="d978a-1009">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="d978a-1010">Chcete-li povolit middleware, definujte `AllowedHosts` klíč v *appsettings.json*/*appsettings.\< EnvironmentName>.json*.</span><span class="sxs-lookup"><span data-stu-id="d978a-1010">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="d978a-1011">Hodnota je seznam názvů hostitelů oddělených středníkem bez čísel portů:</span><span class="sxs-lookup"><span data-stu-id="d978a-1011">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="d978a-1012">*appsettings.json*:</span><span class="sxs-lookup"><span data-stu-id="d978a-1012">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="d978a-1013">[Předávaných záhlaví Middleware](xref:host-and-deploy/proxy-load-balancer) má také možnost. <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts></span><span class="sxs-lookup"><span data-stu-id="d978a-1013">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="d978a-1014">Předané hlavičky Middleware a Host Filtrování Middleware mají podobné funkce pro různé scénáře.</span><span class="sxs-lookup"><span data-stu-id="d978a-1014">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="d978a-1015">Nastavení `AllowedHosts` pomocí middlewaru s předanými hlavičkami je vhodné, pokud `Host` záhlaví není zachováno při předávání požadavků pomocí reverzního proxy serveru nebo vyrovnávání zatížení.</span><span class="sxs-lookup"><span data-stu-id="d978a-1015">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="d978a-1016">Nastavení `AllowedHosts` s Middleware filtrování hostitelů je vhodné, pokud je Kestrel `Host` používán jako veřejný hraniční server nebo když je záhlaví přímo předáno dál.</span><span class="sxs-lookup"><span data-stu-id="d978a-1016">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="d978a-1017">Další informace o middlewaru s <xref:host-and-deploy/proxy-load-balancer>předaných záhlaví naleznete v tématu .</span><span class="sxs-lookup"><span data-stu-id="d978a-1017">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="d978a-1018">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="d978a-1018">Additional resources</span></span>

* <span data-ttu-id="d978a-1019">Při použití unixových soketů v Systému Linux se soket při vypnutí aplikace automaticky neodstraní.</span><span class="sxs-lookup"><span data-stu-id="d978a-1019">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="d978a-1020">Další informace naleznete v [tomto problému GitHub](https://github.com/dotnet/aspnetcore/issues/14134).</span><span class="sxs-lookup"><span data-stu-id="d978a-1020">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="d978a-1021">RFC 7230: Syntaxe a směrování zpráv (oddíl 5.4: Host)</span><span class="sxs-lookup"><span data-stu-id="d978a-1021">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
