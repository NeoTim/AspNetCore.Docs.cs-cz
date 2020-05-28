---
<span data-ttu-id="2fe10-101">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-102">'Blazor'</span></span>
- <span data-ttu-id="2fe10-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-103">'Identity'</span></span>
- <span data-ttu-id="2fe10-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-105">'Razor'</span></span>
- <span data-ttu-id="2fe10-106">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-106">'SignalR' uid:</span></span> 

---
# <a name="kestrel-web-server-implementation-in-aspnet-core"></a><span data-ttu-id="2fe10-107">Implementace webového serveru Kestrel v ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2fe10-107">Kestrel web server implementation in ASP.NET Core</span></span>

<span data-ttu-id="2fe10-108">Díky [Dykstra](https://github.com/tdykstra), [Novák Rossův](https://github.com/Tratcher)a zablokování [Stephen](https://twitter.com/halter73)</span><span class="sxs-lookup"><span data-stu-id="2fe10-108">By [Tom Dykstra](https://github.com/tdykstra), [Chris Ross](https://github.com/Tratcher), and [Stephen Halter](https://twitter.com/halter73)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2fe10-109">Kestrel je [webový server pro různé platformy pro ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="2fe10-109">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="2fe10-110">Kestrel je webový server, který je ve výchozím nastavení součástí ASP.NET Core šablon projektů.</span><span class="sxs-lookup"><span data-stu-id="2fe10-110">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="2fe10-111">Kestrel podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="2fe10-111">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="2fe10-112">HTTPS</span><span class="sxs-lookup"><span data-stu-id="2fe10-112">HTTPS</span></span>
* <span data-ttu-id="2fe10-113">Neprůhledný upgrade používaný k povolení [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="2fe10-113">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="2fe10-114">Technologie UNIX Sockets s vysokým výkonem za Nginx</span><span class="sxs-lookup"><span data-stu-id="2fe10-114">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="2fe10-115">HTTP/2 (kromě macOS &dagger; )</span><span class="sxs-lookup"><span data-stu-id="2fe10-115">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="2fe10-116">&dagger;HTTP/2 se v budoucí verzi podporuje v macOS.</span><span class="sxs-lookup"><span data-stu-id="2fe10-116">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="2fe10-117">Kestrel se podporuje na všech platformách a verzích, které podporuje .NET Core.</span><span class="sxs-lookup"><span data-stu-id="2fe10-117">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="2fe10-118">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2fe10-118">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="2fe10-119">Podpora HTTP/2</span><span class="sxs-lookup"><span data-stu-id="2fe10-119">HTTP/2 support</span></span>

<span data-ttu-id="2fe10-120">[Http/2](https://httpwg.org/specs/rfc7540.html) je k dispozici pro ASP.NET Core aplikace, pokud jsou splněné následující základní požadavky:</span><span class="sxs-lookup"><span data-stu-id="2fe10-120">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="2fe10-121">Operační systém&dagger;</span><span class="sxs-lookup"><span data-stu-id="2fe10-121">Operating system&dagger;</span></span>
  * <span data-ttu-id="2fe10-122">Windows Server 2016/Windows 10 nebo novější&Dagger;</span><span class="sxs-lookup"><span data-stu-id="2fe10-122">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="2fe10-123">Linux s OpenSSL 1.0.2 nebo novějším (například Ubuntu 16,04 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="2fe10-123">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="2fe10-124">Cílová architektura: .NET Core 2,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="2fe10-124">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="2fe10-125">Připojení [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="2fe10-125">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="2fe10-126">Připojení TLS 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="2fe10-126">TLS 1.2 or later connection</span></span>

<span data-ttu-id="2fe10-127">&dagger;HTTP/2 se v budoucí verzi podporuje v macOS.</span><span class="sxs-lookup"><span data-stu-id="2fe10-127">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="2fe10-128">&Dagger;Kestrel má omezená podpora HTTP/2 na Windows Serveru 2012 R2 a Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="2fe10-128">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="2fe10-129">Podpora je omezená, protože seznam podporovaných šifrovacích sad TLS, které jsou k dispozici v těchto operačních systémech, je omezený.</span><span class="sxs-lookup"><span data-stu-id="2fe10-129">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="2fe10-130">Pro zabezpečení připojení TLS může být vyžadován certifikát vygenerovaný pomocí algoritmu ECDSA (s připojením typu eliptická křivka).</span><span class="sxs-lookup"><span data-stu-id="2fe10-130">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="2fe10-131">Pokud je vytvořeno připojení HTTP/2, sestavy [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="2fe10-131">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="2fe10-132">HTTP/2 je ve výchozím nastavení zakázané.</span><span class="sxs-lookup"><span data-stu-id="2fe10-132">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="2fe10-133">Další informace o konfiguraci najdete v částech [Možnosti Kestrel](#kestrel-options) a [ListenOptions. Protocols](#listenoptionsprotocols) .</span><span class="sxs-lookup"><span data-stu-id="2fe10-133">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="2fe10-134">Kdy použít Kestrel s reverzním proxy serverem</span><span class="sxs-lookup"><span data-stu-id="2fe10-134">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="2fe10-135">Kestrel může používat sám sebe nebo s *reverzními proxy server*, jako je [Internetová informační služba (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="2fe10-135">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="2fe10-136">Reverzní proxy server přijímá požadavky HTTP ze sítě a přesměruje je na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2fe10-136">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="2fe10-137">Kestrel použitý jako hraniční webový server (internetový):</span><span class="sxs-lookup"><span data-stu-id="2fe10-137">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel komunikuje přímo s internetem bez zpětného proxy server](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="2fe10-139">Kestrel se používá v konfiguraci reverzního proxy serveru:</span><span class="sxs-lookup"><span data-stu-id="2fe10-139">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel nepřímo komunikuje s internetem prostřednictvím reverzní proxy server, jako je IIS, Nginx nebo Apache.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="2fe10-141">Buď konfigurace, s zpětným proxy server nebo bez něj, je podporovaná konfigurace hostování.</span><span class="sxs-lookup"><span data-stu-id="2fe10-141">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="2fe10-142">Kestrel používané jako hraniční Server bez reverzních proxy server nepodporuje sdílení stejné IP adresy a portu mezi více procesy.</span><span class="sxs-lookup"><span data-stu-id="2fe10-142">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="2fe10-143">Když je Kestrel nakonfigurovaný k naslouchání na portu, Kestrel zpracovává veškerý provoz tohoto portu bez ohledu na `Host` hlavičky požadavků.</span><span class="sxs-lookup"><span data-stu-id="2fe10-143">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="2fe10-144">Reverzní proxy server, který může sdílet porty, má schopnost přesměrovat požadavky na Kestrel na jedinečné IP adresy a porty.</span><span class="sxs-lookup"><span data-stu-id="2fe10-144">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="2fe10-145">I v případě, že se zpětný proxy server nepožaduje, může být použití reverzní proxy server vhodnou volbou.</span><span class="sxs-lookup"><span data-stu-id="2fe10-145">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="2fe10-146">Reverzní proxy:</span><span class="sxs-lookup"><span data-stu-id="2fe10-146">A reverse proxy:</span></span>

* <span data-ttu-id="2fe10-147">Může omezit vystavenou veřejnou plochu v aplikacích, které hostuje.</span><span class="sxs-lookup"><span data-stu-id="2fe10-147">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="2fe10-148">Poskytněte další vrstvu konfigurace a obrany.</span><span class="sxs-lookup"><span data-stu-id="2fe10-148">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="2fe10-149">Může se lépe integrovat se stávající infrastrukturou.</span><span class="sxs-lookup"><span data-stu-id="2fe10-149">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="2fe10-150">Zjednodušte vyrovnávání zatížení a konfiguraci zabezpečené komunikace (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="2fe10-150">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="2fe10-151">Pouze reverzní proxy server vyžaduje certifikát X. 509 a tento server může komunikovat se servery aplikace v interní síti pomocí prostého protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fe10-151">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="2fe10-152">Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="2fe10-152">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="kestrel-in-aspnet-core-apps"></a><span data-ttu-id="2fe10-153">Kestrel v aplikacích ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2fe10-153">Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="2fe10-154">ASP.NET Core šablony projektu ve výchozím nastavení používají Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2fe10-154">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="2fe10-155">V *program.cs* <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> metoda volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> :</span><span class="sxs-lookup"><span data-stu-id="2fe10-155">In *Program.cs*, the <xref:Microsoft.Extensions.Hosting.GenericHostBuilderExtensions.ConfigureWebHostDefaults*> method calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=8)]

<span data-ttu-id="2fe10-156">Další informace o vytváření hostitele najdete v oddílech nastavení *hostitele* a *výchozí tvůrce nastavení* v tématu <xref:fundamentals/host/generic-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="2fe10-156">For more information on building the host, see the *Set up a host* and *Default builder settings* sections of <xref:fundamentals/host/generic-host#set-up-a-host>.</span></span>

<span data-ttu-id="2fe10-157">Chcete-li po volání poskytnout další konfiguraci `ConfigureWebHostDefaults` , použijte `ConfigureKestrel` :</span><span class="sxs-lookup"><span data-stu-id="2fe10-157">To provide additional configuration after calling `ConfigureWebHostDefaults`, use `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="2fe10-158">Kestrel možnosti</span><span class="sxs-lookup"><span data-stu-id="2fe10-158">Kestrel options</span></span>

<span data-ttu-id="2fe10-159">Webový server Kestrel má možnosti konfigurace omezení, které jsou zvláště užitečné v internetových nasazeních.</span><span class="sxs-lookup"><span data-stu-id="2fe10-159">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="2fe10-160">Nastavte omezení pro <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> vlastnost <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> třídy.</span><span class="sxs-lookup"><span data-stu-id="2fe10-160">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="2fe10-161">`Limits`Vlastnost obsahuje instanci <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> třídy.</span><span class="sxs-lookup"><span data-stu-id="2fe10-161">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="2fe10-162">V následujících příkladech se používá <xref:Microsoft.AspNetCore.Server.Kestrel.Core> obor názvů:</span><span class="sxs-lookup"><span data-stu-id="2fe10-162">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="2fe10-163">V příkladech, které jsou uvedeny dále v tomto článku, jsou možnosti Kestrel konfigurovány v kódu jazyka C#.</span><span class="sxs-lookup"><span data-stu-id="2fe10-163">In examples shown later in this article, Kestrel options are configured in C# code.</span></span> <span data-ttu-id="2fe10-164">Možnosti Kestrel lze také nastavit pomocí [poskytovatele konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="2fe10-164">Kestrel options can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="2fe10-165">[Poskytovatel konfigurace souboru](xref:fundamentals/configuration/index#file-configuration-provider) může například načíst konfiguraci Kestrel z objektu *appSettings. JSON* nebo *appSettings. { Soubor Environment}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="2fe10-165">For example, the [File Configuration Provider](xref:fundamentals/configuration/index#file-configuration-provider) can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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
> <span data-ttu-id="2fe10-166"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>[Konfigurace koncových bodů](#endpoint-configuration) se dá konfigurovat z poskytovatelů konfigurace.</span><span class="sxs-lookup"><span data-stu-id="2fe10-166"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> and [endpoint configuration](#endpoint-configuration) are configurable from configuration providers.</span></span> <span data-ttu-id="2fe10-167">Zbývající konfigurace Kestrel musí být nakonfigurovaná v kódu C#.</span><span class="sxs-lookup"><span data-stu-id="2fe10-167">Remaining Kestrel configuration must be configured in C# code.</span></span>

<span data-ttu-id="2fe10-168">Použijte **jeden** z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="2fe10-168">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="2fe10-169">Nakonfigurovat Kestrel v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2fe10-169">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="2fe10-170">Vloží instanci `IConfiguration` do `Startup` třídy.</span><span class="sxs-lookup"><span data-stu-id="2fe10-170">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="2fe10-171">Následující příklad předpokládá, že vložená konfigurace je přiřazena `Configuration` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="2fe10-171">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="2fe10-172">V aplikaci `Startup.ConfigureServices` načtěte `Kestrel` část konfigurace do konfigurace Kestrel:</span><span class="sxs-lookup"><span data-stu-id="2fe10-172">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="2fe10-173">Nakonfigurovat Kestrel při sestavování hostitele:</span><span class="sxs-lookup"><span data-stu-id="2fe10-173">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="2fe10-174">V *program.cs*načtěte `Kestrel` část konfigurace do konfigurace Kestrel:</span><span class="sxs-lookup"><span data-stu-id="2fe10-174">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="2fe10-175">Obě předchozí přístupy fungují u libovolného [poskytovatele konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="2fe10-175">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="2fe10-176">Časový limit Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="2fe10-176">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="2fe10-177">Získá nebo nastaví [časový limit zachování](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="2fe10-177">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="2fe10-178">Výchozí hodnota je 2 minuty.</span><span class="sxs-lookup"><span data-stu-id="2fe10-178">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=19-20)]

### <a name="maximum-client-connections"></a><span data-ttu-id="2fe10-179">Maximální počet připojení klientů</span><span class="sxs-lookup"><span data-stu-id="2fe10-179">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="2fe10-180">Maximální počet souběžných otevřených připojení TCP lze pro celou aplikaci nastavit pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="2fe10-180">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="2fe10-181">Existuje zvláštní omezení pro připojení, která byla upgradována z protokolu HTTP nebo HTTPS na jiný protokol (například na žádost WebSockets).</span><span class="sxs-lookup"><span data-stu-id="2fe10-181">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="2fe10-182">Po upgradu připojení se nepočítá na základě `MaxConcurrentConnections` limitu.</span><span class="sxs-lookup"><span data-stu-id="2fe10-182">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="2fe10-183">Ve výchozím nastavení je maximální počet připojení neomezené (null).</span><span class="sxs-lookup"><span data-stu-id="2fe10-183">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="2fe10-184">Maximální velikost textu požadavku</span><span class="sxs-lookup"><span data-stu-id="2fe10-184">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="2fe10-185">Výchozí maximální velikost textu požadavku je 30 000 000 bajtů, což je přibližně 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="2fe10-185">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="2fe10-186">Doporučený postup pro přepsání limitu v aplikaci ASP.NET Core MVC je použití <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> atributu pro metodu akce:</span><span class="sxs-lookup"><span data-stu-id="2fe10-186">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="2fe10-187">Tady je příklad, který ukazuje, jak nakonfigurovat omezení pro aplikaci na každém požadavku:</span><span class="sxs-lookup"><span data-stu-id="2fe10-187">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="2fe10-188">Přepsat nastavení u konkrétního požadavku v middleware:</span><span class="sxs-lookup"><span data-stu-id="2fe10-188">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="2fe10-189">Výjimka je vyvolána, pokud aplikace po zahájení aplikace nakonfiguruje limit na žádost.</span><span class="sxs-lookup"><span data-stu-id="2fe10-189">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="2fe10-190">Existuje `IsReadOnly` vlastnost, která určuje, zda `MaxRequestBodySize` je vlastnost ve stavu jen pro čtení, což znamená, že je příliš pozdě pro konfiguraci limitu.</span><span class="sxs-lookup"><span data-stu-id="2fe10-190">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="2fe10-191">Když je aplikace spouštěna [mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za [ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module), je omezení velikosti textu požadavku Kestrel zakázané, protože služba IIS tento limit již nastavuje.</span><span class="sxs-lookup"><span data-stu-id="2fe10-191">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="2fe10-192">Minimální rychlost těla zprávy požadavku</span><span class="sxs-lookup"><span data-stu-id="2fe10-192">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="2fe10-193">Kestrel kontroluje každou sekundu, pokud data přicházejí za zadanou rychlost v bajtech za sekundu.</span><span class="sxs-lookup"><span data-stu-id="2fe10-193">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="2fe10-194">Pokud frekvence klesne pod minimální hodnotu, vypršel časový limit připojení. Doba odkladu je doba, kterou Kestrel umožňuje klientovi zvýšit rychlost odesílání až na minimum; frekvence není během této doby kontrolována.</span><span class="sxs-lookup"><span data-stu-id="2fe10-194">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="2fe10-195">Doba odkladu pomáhá vyhnout se vyřazení připojení, která při počátečním posílání dat zpomalují rychlost, kvůli pomalému spuštění protokolu TCP.</span><span class="sxs-lookup"><span data-stu-id="2fe10-195">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="2fe10-196">Výchozí minimální sazba je 240 bajtů za sekundu s obdobím 5 sekund odkladu.</span><span class="sxs-lookup"><span data-stu-id="2fe10-196">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="2fe10-197">Pro odpověď platí také minimální sazba.</span><span class="sxs-lookup"><span data-stu-id="2fe10-197">A minimum rate also applies to the response.</span></span> <span data-ttu-id="2fe10-198">Kód pro nastavení limitu požadavku a limit odezvy je stejný, s výjimkou, `RequestBody` že existují nebo `Response` v názvech vlastností a rozhraní.</span><span class="sxs-lookup"><span data-stu-id="2fe10-198">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="2fe10-199">Tady je příklad, který ukazuje, jak nakonfigurovat minimální datové tarify v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="2fe10-199">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-11)]

<span data-ttu-id="2fe10-200">Přepsat minimální limity přenosové rychlosti na žádost v middlewaru:</span><span class="sxs-lookup"><span data-stu-id="2fe10-200">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="2fe10-201"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature>Odkazovaná v předchozí ukázce není k dispozici `HttpContext.Features` pro požadavky HTTP/2, protože Změna omezení přenosové rychlosti u jednotlivých požadavků je obecně Nepodporovaná pro HTTP/2 kvůli podpoře protokolu pro multiplexování požadavků.</span><span class="sxs-lookup"><span data-stu-id="2fe10-201">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinResponseDataRateFeature> referenced in the prior sample is not present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis is generally not supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="2fe10-202"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> `HttpContext.Features` Pro požadavky HTTP/2 se ale stále používá, protože omezení četnosti čtení může být pořád *zcela zakázané* na základě jednotlivých požadavků nastavením `IHttpMinRequestBodyDataRateFeature.MinDataRate` na `null` i pro požadavek HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="2fe10-202">However, the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Features.IHttpMinRequestBodyDataRateFeature> is still present `HttpContext.Features` for HTTP/2 requests, because the read rate limit can still be *disabled entirely* on a per-request basis by setting `IHttpMinRequestBodyDataRateFeature.MinDataRate` to `null` even for an HTTP/2 request.</span></span> <span data-ttu-id="2fe10-203">Když se pokusíte o jeho navýšení `IHttpMinRequestBodyDataRateFeature.MinDataRate` nebo pokus o jeho nastavení na jinou hodnotu než, `null` vygeneruje se `NotSupportedException` mu požadavek HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="2fe10-203">Attempting to read `IHttpMinRequestBodyDataRateFeature.MinDataRate` or attempting to set it to a value other than `null` will result in a `NotSupportedException` being thrown given an HTTP/2 request.</span></span>

<span data-ttu-id="2fe10-204">Omezení přenosové rychlosti pro všechny servery jsou nakonfigurovaná přes `KestrelServerOptions.Limits` protokol HTTP/1. x i pro připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="2fe10-204">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="2fe10-205">Časový limit hlaviček žádostí</span><span class="sxs-lookup"><span data-stu-id="2fe10-205">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="2fe10-206">Získá nebo nastaví maximální dobu, po kterou server stráví příjem hlaviček požadavků.</span><span class="sxs-lookup"><span data-stu-id="2fe10-206">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="2fe10-207">Výchozí hodnota je 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="2fe10-207">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=21-22)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="2fe10-208">Maximální počet proudů na připojení</span><span class="sxs-lookup"><span data-stu-id="2fe10-208">Maximum streams per connection</span></span>

<span data-ttu-id="2fe10-209">`Http2.MaxStreamsPerConnection`omezuje počet souběžných datových proudů požadavků na připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="2fe10-209">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="2fe10-210">Nadbytečné proudy jsou odmítnuty.</span><span class="sxs-lookup"><span data-stu-id="2fe10-210">Excess streams are refused.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
});
```

<span data-ttu-id="2fe10-211">Výchozí hodnota je 100.</span><span class="sxs-lookup"><span data-stu-id="2fe10-211">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="2fe10-212">Velikost tabulky hlaviček</span><span class="sxs-lookup"><span data-stu-id="2fe10-212">Header table size</span></span>

<span data-ttu-id="2fe10-213">Dekodér HPACK dekomprimuje hlavičky HTTP pro připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="2fe10-213">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="2fe10-214">`Http2.HeaderTableSize`omezuje velikost kompresní tabulky hlaviček, kterou používá dekodér HPACK.</span><span class="sxs-lookup"><span data-stu-id="2fe10-214">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="2fe10-215">Hodnota je uvedena v oktetech a musí být větší než nula (0).</span><span class="sxs-lookup"><span data-stu-id="2fe10-215">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.HeaderTableSize = 4096;
});
```

<span data-ttu-id="2fe10-216">Výchozí hodnota je 4096.</span><span class="sxs-lookup"><span data-stu-id="2fe10-216">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="2fe10-217">Maximální velikost rámce</span><span class="sxs-lookup"><span data-stu-id="2fe10-217">Maximum frame size</span></span>

<span data-ttu-id="2fe10-218">`Http2.MaxFrameSize`Určuje maximální povolenou velikost datové části rámce připojení HTTP/2 přijatého nebo odesílaného serverem.</span><span class="sxs-lookup"><span data-stu-id="2fe10-218">`Http2.MaxFrameSize` indicates the maximum allowed size of an HTTP/2 connection frame payload received or sent by the server.</span></span> <span data-ttu-id="2fe10-219">Hodnota je uvedena v oktetech a musí být v rozmezí 2 ^ 14 (16 384) až 2 ^ 24-1 (16 777 215).</span><span class="sxs-lookup"><span data-stu-id="2fe10-219">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxFrameSize = 16384;
});
```

<span data-ttu-id="2fe10-220">Výchozí hodnota je 2 ^ 14 (16 384).</span><span class="sxs-lookup"><span data-stu-id="2fe10-220">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="2fe10-221">Maximální velikost hlavičky požadavku</span><span class="sxs-lookup"><span data-stu-id="2fe10-221">Maximum request header size</span></span>

<span data-ttu-id="2fe10-222">`Http2.MaxRequestHeaderFieldSize`Určuje maximální povolenou velikost v oktetech hodnot hlaviček požadavku.</span><span class="sxs-lookup"><span data-stu-id="2fe10-222">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="2fe10-223">Toto omezení se vztahuje na název i hodnotu v jejich komprimovaných a nekomprimovaných reprezentujcích.</span><span class="sxs-lookup"><span data-stu-id="2fe10-223">This limit applies to both name and value in their compressed and uncompressed representations.</span></span> <span data-ttu-id="2fe10-224">Hodnota musí být větší než nula (0).</span><span class="sxs-lookup"><span data-stu-id="2fe10-224">The value must be greater than zero (0).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
});
```

<span data-ttu-id="2fe10-225">Výchozí hodnota je 8 192.</span><span class="sxs-lookup"><span data-stu-id="2fe10-225">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="2fe10-226">Velikost okna počátečního připojení</span><span class="sxs-lookup"><span data-stu-id="2fe10-226">Initial connection window size</span></span>

<span data-ttu-id="2fe10-227">`Http2.InitialConnectionWindowSize`Určuje maximální velikost textu požadavku v bajtech, které jsou v jednom okamžiku agregovány v rámci všech požadavků (datových proudů) na připojení.</span><span class="sxs-lookup"><span data-stu-id="2fe10-227">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="2fe10-228">Žádosti jsou také omezené pomocí `Http2.InitialStreamWindowSize` .</span><span class="sxs-lookup"><span data-stu-id="2fe10-228">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="2fe10-229">Hodnota musí být větší než nebo rovna 65 535 a menší než 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="2fe10-229">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
});
```

<span data-ttu-id="2fe10-230">Výchozí hodnota je 128 KB (131 072).</span><span class="sxs-lookup"><span data-stu-id="2fe10-230">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="2fe10-231">Počáteční velikost okna datového proudu</span><span class="sxs-lookup"><span data-stu-id="2fe10-231">Initial stream window size</span></span>

<span data-ttu-id="2fe10-232">`Http2.InitialStreamWindowSize`Určuje maximální velikost textu požadavku v bajtech na jednu žádost (Stream) na jednom místě.</span><span class="sxs-lookup"><span data-stu-id="2fe10-232">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="2fe10-233">Žádosti jsou také omezené pomocí `Http2.InitialConnectionWindowSize` .</span><span class="sxs-lookup"><span data-stu-id="2fe10-233">Requests are also limited by `Http2.InitialConnectionWindowSize`.</span></span> <span data-ttu-id="2fe10-234">Hodnota musí být větší než nebo rovna 65 535 a menší než 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="2fe10-234">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
});
```

<span data-ttu-id="2fe10-235">Výchozí hodnota je 96 KB (98 304).</span><span class="sxs-lookup"><span data-stu-id="2fe10-235">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="2fe10-236">Synchronní vstupně-výstupní operace</span><span class="sxs-lookup"><span data-stu-id="2fe10-236">Synchronous I/O</span></span>

<span data-ttu-id="2fe10-237"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>Určuje, jestli je pro požadavek a odpověď povolený synchronní vstup/výstup.</span><span class="sxs-lookup"><span data-stu-id="2fe10-237"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="2fe10-238">Výchozí hodnota je `false`.</span><span class="sxs-lookup"><span data-stu-id="2fe10-238">The default value is `false`.</span></span>

> [!WARNING]
> <span data-ttu-id="2fe10-239">Velký počet blokujících synchronních vstupně-výstupních operací může vést ke vyčerpání fondu vláken, což aplikaci nereaguje.</span><span class="sxs-lookup"><span data-stu-id="2fe10-239">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="2fe10-240">Povolit pouze `AllowSynchronousIO` při použití knihovny, která nepodporuje asynchronní vstupně-výstupní operace.</span><span class="sxs-lookup"><span data-stu-id="2fe10-240">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="2fe10-241">Následující příklad povoluje synchronní vstupně-výstupní operace:</span><span class="sxs-lookup"><span data-stu-id="2fe10-241">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="2fe10-242">Informace o dalších možnostech a omezeních Kestrel najdete v tématech:</span><span class="sxs-lookup"><span data-stu-id="2fe10-242">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="2fe10-243">Konfigurace koncového bodu</span><span class="sxs-lookup"><span data-stu-id="2fe10-243">Endpoint configuration</span></span>

<span data-ttu-id="2fe10-244">Ve výchozím nastavení ASP.NET Core váže k:</span><span class="sxs-lookup"><span data-stu-id="2fe10-244">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="2fe10-245">`https://localhost:5001`(Pokud je k dispozici místní vývojový certifikát)</span><span class="sxs-lookup"><span data-stu-id="2fe10-245">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="2fe10-246">Zadejte adresy URL pomocí:</span><span class="sxs-lookup"><span data-stu-id="2fe10-246">Specify URLs using the:</span></span>

* <span data-ttu-id="2fe10-247">`ASPNETCORE_URLS`Proměnná prostředí.</span><span class="sxs-lookup"><span data-stu-id="2fe10-247">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="2fe10-248">`--urls`argument příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="2fe10-248">`--urls` command-line argument.</span></span>
* <span data-ttu-id="2fe10-249">`urls`konfigurační klíč hostitele.</span><span class="sxs-lookup"><span data-stu-id="2fe10-249">`urls` host configuration key.</span></span>
* <span data-ttu-id="2fe10-250">`UseUrls`Metoda rozšíření</span><span class="sxs-lookup"><span data-stu-id="2fe10-250">`UseUrls` extension method.</span></span>

<span data-ttu-id="2fe10-251">Hodnota zadaná pomocí těchto přístupů může být jeden nebo více koncových bodů HTTP a HTTPS (HTTPS, pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="2fe10-251">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="2fe10-252">Nakonfigurujte hodnotu jako seznam oddělený středníkem (například `"Urls": "http://localhost:8000;http://localhost:8001"` ).</span><span class="sxs-lookup"><span data-stu-id="2fe10-252">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="2fe10-253">Další informace o těchto přístupůch najdete v tématu [adresy URL serveru](xref:fundamentals/host/web-host#server-urls) a [potlačení konfigurace](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="2fe10-253">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="2fe10-254">Vytvoří se vývojový certifikát:</span><span class="sxs-lookup"><span data-stu-id="2fe10-254">A development certificate is created:</span></span>

* <span data-ttu-id="2fe10-255">Po instalaci [.NET Core SDK](/dotnet/core/sdk) .</span><span class="sxs-lookup"><span data-stu-id="2fe10-255">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="2fe10-256">[Nástroj pro vývoj a certifikáty](xref:aspnetcore-2.1#https) slouží k vytvoření certifikátu.</span><span class="sxs-lookup"><span data-stu-id="2fe10-256">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="2fe10-257">Některé prohlížeče vyžadují udělení explicitního oprávnění pro důvěřování místního vývojového certifikátu.</span><span class="sxs-lookup"><span data-stu-id="2fe10-257">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="2fe10-258">Šablony projektů konfigurují aplikace tak, aby běžely ve výchozím nastavení HTTPS a zahrnovaly [přesměrování https a podporu HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="2fe10-258">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="2fe10-259">Volání <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> nebo <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> metody pro <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> konfiguraci předpon adres URL a portů pro Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2fe10-259">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="2fe10-260">`UseUrls`, `--urls` argument příkazového řádku, `urls` konfigurační klíč hostitele a `ASPNETCORE_URLS` Proměnná prostředí fungují také, ale mají omezení zaznamenaná později v této části (pro konfiguraci koncového bodu https musí být k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="2fe10-260">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="2fe10-261">`KestrelServerOptions`rozšířeného</span><span class="sxs-lookup"><span data-stu-id="2fe10-261">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="2fe10-262">ConfigureEndpointDefaults (akce \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="2fe10-262">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="2fe10-263">Určuje konfiguraci `Action` , která se spustí pro každý zadaný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="2fe10-263">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="2fe10-264">Volání několikrát `ConfigureEndpointDefaults` nahrazuje předchozí `Action` `Action` zadanou parametr s.</span><span class="sxs-lookup"><span data-stu-id="2fe10-264">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="2fe10-265">Koncové body vytvořené voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **před** voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> nebudou použity výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="2fe10-265">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="2fe10-266">ConfigureHttpsDefaults (akce \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="2fe10-266">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="2fe10-267">Určuje konfiguraci `Action` , která se má spustit pro každý koncový bod HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2fe10-267">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="2fe10-268">Volání několikrát `ConfigureHttpsDefaults` nahrazuje předchozí `Action` `Action` zadanou parametr s.</span><span class="sxs-lookup"><span data-stu-id="2fe10-268">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="2fe10-269">Koncové body vytvořené voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **před** voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> nebudou použity výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="2fe10-269">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="2fe10-270">Konfigurace (IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="2fe10-270">Configure(IConfiguration)</span></span>

<span data-ttu-id="2fe10-271">Vytvoří zavaděč konfigurace pro nastavení Kestrel, který přebírá <xref:Microsoft.Extensions.Configuration.IConfiguration> jako vstup.</span><span class="sxs-lookup"><span data-stu-id="2fe10-271">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="2fe10-272">Konfigurace musí být vymezená na konfigurační oddíl pro Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2fe10-272">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="2fe10-273">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="2fe10-273">ListenOptions.UseHttps</span></span>

<span data-ttu-id="2fe10-274">Nakonfigurujte Kestrel tak, aby používal protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2fe10-274">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="2fe10-275">`ListenOptions.UseHttps`SND</span><span class="sxs-lookup"><span data-stu-id="2fe10-275">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="2fe10-276">`UseHttps`: Nakonfigurujte Kestrel tak, aby používal HTTPS s výchozím certifikátem.</span><span class="sxs-lookup"><span data-stu-id="2fe10-276">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="2fe10-277">Vyvolá výjimku, pokud není nakonfigurován žádný výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="2fe10-277">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="2fe10-278">`ListenOptions.UseHttps`ukazatelů</span><span class="sxs-lookup"><span data-stu-id="2fe10-278">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="2fe10-279">`filename`je cesta a název souboru certifikátu vzhledem k adresáři, který obsahuje soubory obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="2fe10-279">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="2fe10-280">`password`je vyžadováno heslo pro přístup k datům certifikátu X. 509.</span><span class="sxs-lookup"><span data-stu-id="2fe10-280">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="2fe10-281">`configureOptions`je `Action` ke konfiguraci `HttpsConnectionAdapterOptions` .</span><span class="sxs-lookup"><span data-stu-id="2fe10-281">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="2fe10-282">Vrátí `ListenOptions` .</span><span class="sxs-lookup"><span data-stu-id="2fe10-282">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="2fe10-283">`storeName`je úložiště certifikátů, ze kterého se má certifikát načíst.</span><span class="sxs-lookup"><span data-stu-id="2fe10-283">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="2fe10-284">`subject`je název subjektu certifikátu.</span><span class="sxs-lookup"><span data-stu-id="2fe10-284">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="2fe10-285">`allowInvalid`Určuje, zda by měly být zváženy neplatné certifikáty, například certifikáty podepsané svým držitelem.</span><span class="sxs-lookup"><span data-stu-id="2fe10-285">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="2fe10-286">`location`je umístěním úložiště, ze kterého se má certifikát načíst.</span><span class="sxs-lookup"><span data-stu-id="2fe10-286">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="2fe10-287">`serverCertificate`je certifikát X. 509.</span><span class="sxs-lookup"><span data-stu-id="2fe10-287">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="2fe10-288">V produkčním prostředí musí být HTTPS explicitně nakonfigurovaný.</span><span class="sxs-lookup"><span data-stu-id="2fe10-288">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="2fe10-289">Musí být zadán minimálně výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="2fe10-289">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="2fe10-290">Podporované konfigurace popsané dál:</span><span class="sxs-lookup"><span data-stu-id="2fe10-290">Supported configurations described next:</span></span>

* <span data-ttu-id="2fe10-291">Žádná konfigurace</span><span class="sxs-lookup"><span data-stu-id="2fe10-291">No configuration</span></span>
* <span data-ttu-id="2fe10-292">Nahradit výchozí certifikát z konfigurace</span><span class="sxs-lookup"><span data-stu-id="2fe10-292">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="2fe10-293">Změna výchozích hodnot v kódu</span><span class="sxs-lookup"><span data-stu-id="2fe10-293">Change the defaults in code</span></span>

<span data-ttu-id="2fe10-294">*Žádná konfigurace*</span><span class="sxs-lookup"><span data-stu-id="2fe10-294">*No configuration*</span></span>

<span data-ttu-id="2fe10-295">Kestrel naslouchá na `http://localhost:5000` a `https://localhost:5001` (Pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="2fe10-295">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="2fe10-296">*Nahradit výchozí certifikát z konfigurace*</span><span class="sxs-lookup"><span data-stu-id="2fe10-296">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="2fe10-297">`CreateDefaultBuilder`Při `Configure(context.Configuration.GetSection("Kestrel"))` načítání konfigurace Kestrel ve výchozím nastavení volá.</span><span class="sxs-lookup"><span data-stu-id="2fe10-297">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="2fe10-298">Pro Kestrel je k dispozici výchozí schéma konfigurace nastavení aplikace HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2fe10-298">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="2fe10-299">Nakonfigurujte několik koncových bodů, včetně adres URL a certifikátů, které se mají použít, buď ze souboru na disku, nebo z úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="2fe10-299">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="2fe10-300">V následujícím příkladu *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="2fe10-300">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="2fe10-301">Nastavte **AllowInvalid** na `true` , aby povolovala použití neplatných certifikátů (například certifikátů podepsaných svým držitelem).</span><span class="sxs-lookup"><span data-stu-id="2fe10-301">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="2fe10-302">Libovolný koncový bod HTTPS, který neurčuje certifikát (**HttpsDefaultCert** v následujícím příkladu), se vrátí k certifikátu definovanému v části **certifikáty** > **výchozí** nebo vývojový certifikát.</span><span class="sxs-lookup"><span data-stu-id="2fe10-302">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="2fe10-303">Alternativou k použití **cesty** a **hesla** pro libovolný uzel certifikátu je zadání certifikátu pomocí polí úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="2fe10-303">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="2fe10-304">Například **Certificates**  >  **výchozí** certifikát může být zadán jako:</span><span class="sxs-lookup"><span data-stu-id="2fe10-304">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="2fe10-305">Poznámky ke schématu:</span><span class="sxs-lookup"><span data-stu-id="2fe10-305">Schema notes:</span></span>

* <span data-ttu-id="2fe10-306">V názvech koncových bodů se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="2fe10-306">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="2fe10-307">Například `HTTPS` a `Https` jsou platné.</span><span class="sxs-lookup"><span data-stu-id="2fe10-307">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="2fe10-308">`Url`U každého koncového bodu je vyžadován parametr.</span><span class="sxs-lookup"><span data-stu-id="2fe10-308">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="2fe10-309">Formát pro tento parametr je stejný jako `Urls` konfigurační parametr nejvyšší úrovně s tím rozdílem, že je omezen na jedinou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="2fe10-309">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="2fe10-310">Tyto koncové body nahrazují hodnoty definované v konfiguraci nejvyšší úrovně `Urls` místo jejich přidání do těchto koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="2fe10-310">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="2fe10-311">Koncové body definované v kódu prostřednictvím `Listen` jsou kumulativní pomocí koncových bodů definovaných v konfiguračním oddílu.</span><span class="sxs-lookup"><span data-stu-id="2fe10-311">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="2fe10-312">`Certificate`Oddíl je nepovinný.</span><span class="sxs-lookup"><span data-stu-id="2fe10-312">The `Certificate` section is optional.</span></span> <span data-ttu-id="2fe10-313">Pokud `Certificate` není oddíl zadaný, použijí se výchozí hodnoty definované v dřívějších scénářích.</span><span class="sxs-lookup"><span data-stu-id="2fe10-313">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="2fe10-314">Pokud nejsou k dispozici žádné výchozí hodnoty, Server vyvolá výjimku a nemůže se spustit.</span><span class="sxs-lookup"><span data-stu-id="2fe10-314">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="2fe10-315">`Certificate`Oddíl podporuje jak heslo **cesty** &ndash; **Password** , tak **Subject** &ndash; certifikáty**úložiště** subjektu.</span><span class="sxs-lookup"><span data-stu-id="2fe10-315">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="2fe10-316">V takovém případě může být definován libovolný počet koncových bodů, pokud to nezpůsobí konflikty portů.</span><span class="sxs-lookup"><span data-stu-id="2fe10-316">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="2fe10-317">`options.Configure(context.Configuration.GetSection("{SECTION}"))`Vrátí `KestrelConfigurationLoader` `.Endpoint(string name, listenOptions => { })` metodu s metodou, kterou lze použít k doplnění nastavení nakonfigurovaného koncového bodu:</span><span class="sxs-lookup"><span data-stu-id="2fe10-317">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="2fe10-318">`KestrelServerOptions.ConfigurationLoader`k pokračování v iteraci u stávajícího zavaděče se dá získat přímý odkaz, jako je třeba ten, který poskytuje <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="2fe10-318">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="2fe10-319">Konfigurační oddíl pro každý koncový bod je k dispozici na možnostech v `Endpoint` metodě, aby bylo možné číst vlastní nastavení.</span><span class="sxs-lookup"><span data-stu-id="2fe10-319">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="2fe10-320">Více konfigurací může být načteno voláním `options.Configure(context.Configuration.GetSection("{SECTION}"))` znovu s jiným oddílem.</span><span class="sxs-lookup"><span data-stu-id="2fe10-320">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="2fe10-321">Je použita pouze poslední konfigurace, pokud `Load` není explicitně volána při předchozích instancích.</span><span class="sxs-lookup"><span data-stu-id="2fe10-321">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="2fe10-322">Metapackage nevolá `Load` , aby se jeho výchozí konfigurační oddíl mohl nahradit.</span><span class="sxs-lookup"><span data-stu-id="2fe10-322">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="2fe10-323">`KestrelConfigurationLoader`zrcadlí `Listen` rodinu rozhraní API z `KestrelServerOptions` as `Endpoint` přetížení, takže koncové body kódu a konfigurace lze konfigurovat na stejném místě.</span><span class="sxs-lookup"><span data-stu-id="2fe10-323">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="2fe10-324">Tato přetížení nepoužívají názvy a využívají pouze výchozí nastavení z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="2fe10-324">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="2fe10-325">*Změna výchozích hodnot v kódu*</span><span class="sxs-lookup"><span data-stu-id="2fe10-325">*Change the defaults in code*</span></span>

<span data-ttu-id="2fe10-326">`ConfigureEndpointDefaults`a `ConfigureHttpsDefaults` dá se použít ke změně výchozího nastavení pro `ListenOptions` a `HttpsConnectionAdapterOptions` , včetně přepsání výchozího certifikátu zadaného v předchozím scénáři.</span><span class="sxs-lookup"><span data-stu-id="2fe10-326">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="2fe10-327">`ConfigureEndpointDefaults`a `ConfigureHttpsDefaults` měla by být volána před konfigurací koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="2fe10-327">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="2fe10-328">*Podpora Kestrel pro SNI*</span><span class="sxs-lookup"><span data-stu-id="2fe10-328">*Kestrel support for SNI*</span></span>

<span data-ttu-id="2fe10-329">[Indikace názvu serveru (SNI)](https://tools.ietf.org/html/rfc6066#section-3) lze použít k hostování více domén na stejné IP adrese a portu.</span><span class="sxs-lookup"><span data-stu-id="2fe10-329">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="2fe10-330">Aby SNI fungovalo, klient pošle název hostitele pro zabezpečenou relaci serveru během metody handshake TLS, aby server mohl poskytovat správný certifikát.</span><span class="sxs-lookup"><span data-stu-id="2fe10-330">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="2fe10-331">Klient používá poskytnutý certifikát pro šifrovanou komunikaci se serverem během zabezpečené relace, která následuje za metodou handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="2fe10-331">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="2fe10-332">Kestrel podporuje SNI prostřednictvím `ServerCertificateSelector` zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="2fe10-332">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="2fe10-333">Zpětné volání je vyvoláno jednou za připojení, které aplikaci umožní zkontrolovat název hostitele a vybrat příslušný certifikát.</span><span class="sxs-lookup"><span data-stu-id="2fe10-333">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="2fe10-334">Podpora SNI vyžaduje:</span><span class="sxs-lookup"><span data-stu-id="2fe10-334">SNI support requires:</span></span>

* <span data-ttu-id="2fe10-335">Běží v cílovém rozhraní `netcoreapp2.1` nebo novějším.</span><span class="sxs-lookup"><span data-stu-id="2fe10-335">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="2fe10-336">V systému `net461` nebo novějším je zpětné volání vyvoláno, ale `name` je vždy `null` .</span><span class="sxs-lookup"><span data-stu-id="2fe10-336">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="2fe10-337">`name`Je také v `null` případě, že klient v TLS handshake neposkytne parametr názvu hostitele.</span><span class="sxs-lookup"><span data-stu-id="2fe10-337">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="2fe10-338">Všechny weby běží na stejné instanci Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2fe10-338">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="2fe10-339">Kestrel nepodporuje sdílení IP adresy a portu napříč několika instancemi bez reverzního proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="2fe10-339">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="2fe10-340">Protokolování připojení</span><span class="sxs-lookup"><span data-stu-id="2fe10-340">Connection logging</span></span>

<span data-ttu-id="2fe10-341">Volá <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> se, aby se vygenerovaly protokoly na úrovni ladění pro komunikaci na úrovni bajtového připojení.</span><span class="sxs-lookup"><span data-stu-id="2fe10-341">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="2fe10-342">Protokolování připojení je užitečné při řešení problémů v komunikaci nízké úrovně, například během šifrování TLS a za proxy servery.</span><span class="sxs-lookup"><span data-stu-id="2fe10-342">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="2fe10-343">Pokud `UseConnectionLogging` je umístěn před `UseHttps` , zaprotokoluje se zašifrovaný provoz.</span><span class="sxs-lookup"><span data-stu-id="2fe10-343">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="2fe10-344">Pokud `UseConnectionLogging` je umístěn po `UseHttps` , zaprotokoluje se dešifrovaný provoz.</span><span class="sxs-lookup"><span data-stu-id="2fe10-344">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="2fe10-345">Vytvoření vazby na soket TCP</span><span class="sxs-lookup"><span data-stu-id="2fe10-345">Bind to a TCP socket</span></span>

<span data-ttu-id="2fe10-346"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>Metoda se váže k soketu TCP a pomocí lambda možností povolíte konfiguraci certifikátu X. 509:</span><span class="sxs-lookup"><span data-stu-id="2fe10-346">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=12-18)]

<span data-ttu-id="2fe10-347">Příklad nakonfiguruje HTTPS pro koncový bod pomocí <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> .</span><span class="sxs-lookup"><span data-stu-id="2fe10-347">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="2fe10-348">Ke konfiguraci dalších nastavení Kestrel pro konkrétní koncové body použijte stejné rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="2fe10-348">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="2fe10-349">Vytvoření vazby na soket UNIX</span><span class="sxs-lookup"><span data-stu-id="2fe10-349">Bind to a Unix socket</span></span>

<span data-ttu-id="2fe10-350">Naslouchat na soketu se systémem UNIX <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> , aby se zlepšil výkon pomocí Nginx, jak je znázorněno v tomto příkladu:</span><span class="sxs-lookup"><span data-stu-id="2fe10-350">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="2fe10-351">V konfiguračním souboru Nginx nastavte `server`  >  `location`  >  `proxy_pass` položku na `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="2fe10-351">In the Nginx configuration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="2fe10-352">`{KESTREL SOCKET}`je název soketu poskytnutý <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (například `kestrel-test.sock` v předchozím příkladu).</span><span class="sxs-lookup"><span data-stu-id="2fe10-352">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="2fe10-353">Zajistěte, aby byl soket zapisovatelný pomocí Nginx (například `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="2fe10-353">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span>

### <a name="port-0"></a><span data-ttu-id="2fe10-354">Port 0</span><span class="sxs-lookup"><span data-stu-id="2fe10-354">Port 0</span></span>

<span data-ttu-id="2fe10-355">Po zadání čísla portu se `0` Kestrel dynamicky váže k dostupnému portu.</span><span class="sxs-lookup"><span data-stu-id="2fe10-355">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="2fe10-356">Následující příklad ukazuje, jak určit, který port Kestrel je skutečně vázaný za běhu:</span><span class="sxs-lookup"><span data-stu-id="2fe10-356">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/3.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="2fe10-357">Když se aplikace spustí, výstup okna konzoly indikuje dynamický port, na který se aplikace dá získat:</span><span class="sxs-lookup"><span data-stu-id="2fe10-357">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="2fe10-358">Omezení</span><span class="sxs-lookup"><span data-stu-id="2fe10-358">Limitations</span></span>

<span data-ttu-id="2fe10-359">Konfigurace koncových bodů pomocí následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="2fe10-359">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="2fe10-360">`--urls`argument příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="2fe10-360">`--urls` command-line argument</span></span>
* <span data-ttu-id="2fe10-361">`urls`konfigurační klíč hostitele</span><span class="sxs-lookup"><span data-stu-id="2fe10-361">`urls` host configuration key</span></span>
* <span data-ttu-id="2fe10-362">`ASPNETCORE_URLS`Proměnná prostředí</span><span class="sxs-lookup"><span data-stu-id="2fe10-362">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="2fe10-363">Tyto metody jsou užitečné, pokud chcete, aby kód pracoval na jiných serverech než Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2fe10-363">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="2fe10-364">Mějte ale na paměti následující omezení:</span><span class="sxs-lookup"><span data-stu-id="2fe10-364">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="2fe10-365">Protokol HTTPS nelze s těmito přístupy použít, pokud není k dispozici výchozí certifikát v konfiguraci koncového bodu HTTPS (například pomocí `KestrelServerOptions` Konfigurace nebo konfiguračního souboru, jak je uvedeno výše v tomto tématu).</span><span class="sxs-lookup"><span data-stu-id="2fe10-365">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="2fe10-366">Při `Listen` `UseUrls` současném použití obou přístupů a `Listen` koncových bodů se koncové body přepisují `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="2fe10-366">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="2fe10-367">Konfigurace koncového bodu služby IIS</span><span class="sxs-lookup"><span data-stu-id="2fe10-367">IIS endpoint configuration</span></span>

<span data-ttu-id="2fe10-368">Při použití služby IIS jsou vazby adresy URL pro přepsání vazby služby IIS nastaveny buď `Listen` nebo `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="2fe10-368">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="2fe10-369">Další informace najdete v tématu [ASP.NET Core modulu](xref:host-and-deploy/aspnet-core-module) .</span><span class="sxs-lookup"><span data-stu-id="2fe10-369">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="2fe10-370">ListenOptions. Protocols</span><span class="sxs-lookup"><span data-stu-id="2fe10-370">ListenOptions.Protocols</span></span>

<span data-ttu-id="2fe10-371">`Protocols`Vlastnost určuje protokoly HTTP ( `HttpProtocols` ) povolené pro koncový bod připojení nebo pro server.</span><span class="sxs-lookup"><span data-stu-id="2fe10-371">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="2fe10-372">Přiřaďte hodnotu `Protocols` vlastnosti z `HttpProtocols` výčtu.</span><span class="sxs-lookup"><span data-stu-id="2fe10-372">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="2fe10-373">`HttpProtocols`hodnota výčtu</span><span class="sxs-lookup"><span data-stu-id="2fe10-373">`HttpProtocols` enum value</span></span> | <span data-ttu-id="2fe10-374">Povolený protokol připojení</span><span class="sxs-lookup"><span data-stu-id="2fe10-374">Connection protocol permitted</span></span> |
| ---
<span data-ttu-id="2fe10-375">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-375">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-376">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-376">'Blazor'</span></span>
- <span data-ttu-id="2fe10-377">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-377">'Identity'</span></span>
- <span data-ttu-id="2fe10-378">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-378">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-379">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-379">'Razor'</span></span>
- <span data-ttu-id="2fe10-380">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-380">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fe10-381">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-381">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-382">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-382">'Blazor'</span></span>
- <span data-ttu-id="2fe10-383">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-383">'Identity'</span></span>
- <span data-ttu-id="2fe10-384">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-384">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-385">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-385">'Razor'</span></span>
- <span data-ttu-id="2fe10-386">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-386">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fe10-387">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-387">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-388">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-388">'Blazor'</span></span>
- <span data-ttu-id="2fe10-389">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-389">'Identity'</span></span>
- <span data-ttu-id="2fe10-390">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-390">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-391">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-391">'Razor'</span></span>
- <span data-ttu-id="2fe10-392">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-392">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fe10-393">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-393">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-394">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-394">'Blazor'</span></span>
- <span data-ttu-id="2fe10-395">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-395">'Identity'</span></span>
- <span data-ttu-id="2fe10-396">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-396">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-397">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-397">'Razor'</span></span>
- <span data-ttu-id="2fe10-398">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-398">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fe10-399">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-399">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-400">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-400">'Blazor'</span></span>
- <span data-ttu-id="2fe10-401">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-401">'Identity'</span></span>
- <span data-ttu-id="2fe10-402">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-402">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-403">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-403">'Razor'</span></span>
- <span data-ttu-id="2fe10-404">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-404">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fe10-405">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-405">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-406">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-406">'Blazor'</span></span>
- <span data-ttu-id="2fe10-407">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-407">'Identity'</span></span>
- <span data-ttu-id="2fe10-408">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-408">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-409">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-409">'Razor'</span></span>
- <span data-ttu-id="2fe10-410">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-410">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fe10-411">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-411">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-412">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-412">'Blazor'</span></span>
- <span data-ttu-id="2fe10-413">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-413">'Identity'</span></span>
- <span data-ttu-id="2fe10-414">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-414">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-415">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-415">'Razor'</span></span>
- <span data-ttu-id="2fe10-416">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-416">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fe10-417">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-417">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-418">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-418">'Blazor'</span></span>
- <span data-ttu-id="2fe10-419">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-419">'Identity'</span></span>
- <span data-ttu-id="2fe10-420">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-420">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-421">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-421">'Razor'</span></span>
- <span data-ttu-id="2fe10-422">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-422">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fe10-423">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-423">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-424">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-424">'Blazor'</span></span>
- <span data-ttu-id="2fe10-425">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-425">'Identity'</span></span>
- <span data-ttu-id="2fe10-426">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-426">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-427">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-427">'Razor'</span></span>
- <span data-ttu-id="2fe10-428">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-428">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fe10-429">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-429">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-430">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-430">'Blazor'</span></span>
- <span data-ttu-id="2fe10-431">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-431">'Identity'</span></span>
- <span data-ttu-id="2fe10-432">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-432">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-433">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-433">'Razor'</span></span>
- <span data-ttu-id="2fe10-434">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-434">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fe10-435">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-435">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-436">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-436">'Blazor'</span></span>
- <span data-ttu-id="2fe10-437">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-437">'Identity'</span></span>
- <span data-ttu-id="2fe10-438">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-438">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-439">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-439">'Razor'</span></span>
- <span data-ttu-id="2fe10-440">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-440">'SignalR' uid:</span></span> 

<span data-ttu-id="2fe10-441">------------- | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-441">------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-442">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-442">'Blazor'</span></span>
- <span data-ttu-id="2fe10-443">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-443">'Identity'</span></span>
- <span data-ttu-id="2fe10-444">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-444">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-445">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-445">'Razor'</span></span>
- <span data-ttu-id="2fe10-446">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-446">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fe10-447">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-447">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-448">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-448">'Blazor'</span></span>
- <span data-ttu-id="2fe10-449">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-449">'Identity'</span></span>
- <span data-ttu-id="2fe10-450">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-450">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-451">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-451">'Razor'</span></span>
- <span data-ttu-id="2fe10-452">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-452">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fe10-453">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-453">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-454">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-454">'Blazor'</span></span>
- <span data-ttu-id="2fe10-455">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-455">'Identity'</span></span>
- <span data-ttu-id="2fe10-456">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-456">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-457">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-457">'Razor'</span></span>
- <span data-ttu-id="2fe10-458">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-458">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fe10-459">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-459">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-460">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-460">'Blazor'</span></span>
- <span data-ttu-id="2fe10-461">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-461">'Identity'</span></span>
- <span data-ttu-id="2fe10-462">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-462">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-463">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-463">'Razor'</span></span>
- <span data-ttu-id="2fe10-464">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-464">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fe10-465">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-465">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-466">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-466">'Blazor'</span></span>
- <span data-ttu-id="2fe10-467">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-467">'Identity'</span></span>
- <span data-ttu-id="2fe10-468">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-468">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-469">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-469">'Razor'</span></span>
- <span data-ttu-id="2fe10-470">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-470">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fe10-471">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-471">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-472">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-472">'Blazor'</span></span>
- <span data-ttu-id="2fe10-473">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-473">'Identity'</span></span>
- <span data-ttu-id="2fe10-474">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-474">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-475">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-475">'Razor'</span></span>
- <span data-ttu-id="2fe10-476">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-476">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fe10-477">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-477">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-478">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-478">'Blazor'</span></span>
- <span data-ttu-id="2fe10-479">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-479">'Identity'</span></span>
- <span data-ttu-id="2fe10-480">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-480">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-481">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-481">'Razor'</span></span>
- <span data-ttu-id="2fe10-482">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-482">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fe10-483">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-483">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-484">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-484">'Blazor'</span></span>
- <span data-ttu-id="2fe10-485">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-485">'Identity'</span></span>
- <span data-ttu-id="2fe10-486">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-486">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-487">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-487">'Razor'</span></span>
- <span data-ttu-id="2fe10-488">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-488">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fe10-489">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-489">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-490">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-490">'Blazor'</span></span>
- <span data-ttu-id="2fe10-491">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-491">'Identity'</span></span>
- <span data-ttu-id="2fe10-492">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-492">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-493">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-493">'Razor'</span></span>
- <span data-ttu-id="2fe10-494">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-494">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fe10-495">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-495">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-496">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-496">'Blazor'</span></span>
- <span data-ttu-id="2fe10-497">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-497">'Identity'</span></span>
- <span data-ttu-id="2fe10-498">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-498">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-499">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-499">'Razor'</span></span>
- <span data-ttu-id="2fe10-500">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-500">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fe10-501">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-501">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-502">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-502">'Blazor'</span></span>
- <span data-ttu-id="2fe10-503">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-503">'Identity'</span></span>
- <span data-ttu-id="2fe10-504">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-504">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-505">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-505">'Razor'</span></span>
- <span data-ttu-id="2fe10-506">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-506">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fe10-507">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-507">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-508">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-508">'Blazor'</span></span>
- <span data-ttu-id="2fe10-509">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-509">'Identity'</span></span>
- <span data-ttu-id="2fe10-510">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-510">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-511">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-511">'Razor'</span></span>
- <span data-ttu-id="2fe10-512">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-512">'SignalR' uid:</span></span> 

<span data-ttu-id="2fe10-513">--------------- | | `Http1`                    | Pouze HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="2fe10-513">--------------- | | `Http1`                    | HTTP/1.1 only.</span></span> <span data-ttu-id="2fe10-514">Dá se použít s TLS nebo bez něj.</span><span class="sxs-lookup"><span data-stu-id="2fe10-514">Can be used with or without TLS.</span></span> <span data-ttu-id="2fe10-515">| | `Http2`                    | Pouze HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="2fe10-515">| | `Http2`                    | HTTP/2 only.</span></span> <span data-ttu-id="2fe10-516">Dá se použít bez TLS jenom v případě, že klient podporuje [předchozí režim znalostní báze](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="2fe10-516">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> <span data-ttu-id="2fe10-517">| | `Http1AndHttp2`            | HTTP/1.1 a HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="2fe10-517">| | `Http1AndHttp2`            | HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="2fe10-518">HTTP/2 vyžaduje, aby klient v rámci metody handshake protokolu TLS [(ALPN)](https://tools.ietf.org/html/rfc7301#section-3) vybral http/2. v opačném případě se připojení nastaví jako výchozí HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="2fe10-518">HTTP/2 requires the client to select HTTP/2 in the TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="2fe10-519">Výchozí `ListenOptions.Protocols` hodnota pro libovolný koncový bod je `HttpProtocols.Http1AndHttp2` .</span><span class="sxs-lookup"><span data-stu-id="2fe10-519">The default `ListenOptions.Protocols` value for any endpoint is `HttpProtocols.Http1AndHttp2`.</span></span>

<span data-ttu-id="2fe10-520">Omezení TLS pro HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="2fe10-520">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="2fe10-521">TLS verze 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="2fe10-521">TLS version 1.2 or later</span></span>
* <span data-ttu-id="2fe10-522">Opětovné vyjednávání je zakázané.</span><span class="sxs-lookup"><span data-stu-id="2fe10-522">Renegotiation disabled</span></span>
* <span data-ttu-id="2fe10-523">Komprese vypnuta</span><span class="sxs-lookup"><span data-stu-id="2fe10-523">Compression disabled</span></span>
* <span data-ttu-id="2fe10-524">Minimální velikosti dočasného výměny klíčů:</span><span class="sxs-lookup"><span data-stu-id="2fe10-524">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="2fe10-525">Eliptická křivka Diffie-Hellman (ECDH) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; : 224 bity minima</span><span class="sxs-lookup"><span data-stu-id="2fe10-525">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="2fe10-526">Omezené pole Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; : minimální počet bitů 2048</span><span class="sxs-lookup"><span data-stu-id="2fe10-526">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="2fe10-527">Šifrovací sada není zakázaná.</span><span class="sxs-lookup"><span data-stu-id="2fe10-527">Cipher suite not blacklisted</span></span>

<span data-ttu-id="2fe10-528">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack;`TLS-ECDHE`&rbrack; &lbrack; `FIPS186` &rbrack; ve výchozím nastavení je podporována eliptická křivka P-256.</span><span class="sxs-lookup"><span data-stu-id="2fe10-528">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="2fe10-529">Následující příklad povoluje připojení HTTP/1.1 a HTTP/2 na portu 8000.</span><span class="sxs-lookup"><span data-stu-id="2fe10-529">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="2fe10-530">Připojení TLS zabezpečuje pomocí zadaného certifikátu:</span><span class="sxs-lookup"><span data-stu-id="2fe10-530">Connections are secured by TLS with a supplied certificate:</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseHttps("testCert.pfx", "testPassword");
    });
});
```

<span data-ttu-id="2fe10-531">Pomocí middleware připojení můžete v případě potřeby filtrovat handshake na základě jednotlivých připojení pro konkrétní šifry.</span><span class="sxs-lookup"><span data-stu-id="2fe10-531">Use Connection Middleware to filter TLS handshakes on a per-connection basis for specific ciphers if required.</span></span>

<span data-ttu-id="2fe10-532">Následující příklad vyvolá <xref:System.NotSupportedException> jakýkoli šifrovací algoritmus, který aplikace nepodporuje.</span><span class="sxs-lookup"><span data-stu-id="2fe10-532">The following example throws <xref:System.NotSupportedException> for any cipher algorithm that the app doesn't support.</span></span> <span data-ttu-id="2fe10-533">Případně můžete definovat a porovnat [ITlsHandshakeFeature. CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) se seznamem přijatelných šifrovacích sad.</span><span class="sxs-lookup"><span data-stu-id="2fe10-533">Alternatively, define and compare [ITlsHandshakeFeature.CipherAlgorithm](xref:Microsoft.AspNetCore.Connections.Features.ITlsHandshakeFeature.CipherAlgorithm) to a list of acceptable cipher suites.</span></span>

<span data-ttu-id="2fe10-534">Pro šifrovací algoritmus [CipherAlgorithmType. null](xref:System.Security.Authentication.CipherAlgorithmType) se nepoužívá žádné šifrování.</span><span class="sxs-lookup"><span data-stu-id="2fe10-534">No encryption is used with a [CipherAlgorithmType.Null](xref:System.Security.Authentication.CipherAlgorithmType) cipher algorithm.</span></span>

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

<span data-ttu-id="2fe10-535">Filtrování připojení lze také nakonfigurovat prostřednictvím <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span><span class="sxs-lookup"><span data-stu-id="2fe10-535">Connection filtering can also be configured via an <xref:Microsoft.AspNetCore.Connections.IConnectionBuilder> lambda:</span></span>

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

<span data-ttu-id="2fe10-536">V systému Linux se <xref:System.Net.Security.CipherSuitesPolicy> dá použít k filtrování ověřování TLS metodou handshake na základě jednotlivých připojení:</span><span class="sxs-lookup"><span data-stu-id="2fe10-536">On Linux, <xref:System.Net.Security.CipherSuitesPolicy> can be used to filter TLS handshakes on a per-connection basis:</span></span>

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

<span data-ttu-id="2fe10-537">*Nastavit protokol z konfigurace*</span><span class="sxs-lookup"><span data-stu-id="2fe10-537">*Set the protocol from configuration*</span></span>

<span data-ttu-id="2fe10-538">`CreateDefaultBuilder`Při `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` načítání konfigurace Kestrel ve výchozím nastavení volá.</span><span class="sxs-lookup"><span data-stu-id="2fe10-538">`CreateDefaultBuilder` calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="2fe10-539">Následující příklad *appSettings. JSON* vytvoří HTTP/1.1 jako výchozí protokol připojení pro všechny koncové body:</span><span class="sxs-lookup"><span data-stu-id="2fe10-539">The following *appsettings.json* example establishes HTTP/1.1 as the default connection protocol for all endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1"
    }
  }
}
```

<span data-ttu-id="2fe10-540">Následující příklad *appSettings. JSON* vytvoří protokol připojení HTTP/1.1 pro konkrétní koncový bod:</span><span class="sxs-lookup"><span data-stu-id="2fe10-540">The following *appsettings.json* example establishes the HTTP/1.1 connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="2fe10-541">Protokoly zadané v hodnotách přepisu kódu nastavených podle konfigurace</span><span class="sxs-lookup"><span data-stu-id="2fe10-541">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="2fe10-542">Konfigurace přenosu</span><span class="sxs-lookup"><span data-stu-id="2fe10-542">Transport configuration</span></span>

<span data-ttu-id="2fe10-543">Pro projekty, které vyžadují použití Libuv ( <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> ):</span><span class="sxs-lookup"><span data-stu-id="2fe10-543">For projects that require the use of Libuv (<xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>):</span></span>

* <span data-ttu-id="2fe10-544">Přidejte do souboru projektu aplikace závislost pro balíček [Microsoft. AspNetCore. Server. Kestrel. Transport. Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) :</span><span class="sxs-lookup"><span data-stu-id="2fe10-544">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

   ```xml
   <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                     Version="{VERSION}" />
   ```

* <span data-ttu-id="2fe10-545">Zavolat <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> na `IWebHostBuilder` :</span><span class="sxs-lookup"><span data-stu-id="2fe10-545">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> on the `IWebHostBuilder`:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="2fe10-546">Předpony adresy URL</span><span class="sxs-lookup"><span data-stu-id="2fe10-546">URL prefixes</span></span>

<span data-ttu-id="2fe10-547">Při použití `UseUrls` , `--urls` argumentu příkazového řádku, `urls` konfiguračního klíče hostitele nebo `ASPNETCORE_URLS` proměnné prostředí můžou být předpony adresy URL v některém z následujících formátů.</span><span class="sxs-lookup"><span data-stu-id="2fe10-547">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="2fe10-548">Platné jsou pouze předpony adresy URL protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fe10-548">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="2fe10-549">Kestrel nepodporuje protokol HTTPS při konfiguraci vazeb URL pomocí `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="2fe10-549">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="2fe10-550">Adresa IPv4 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="2fe10-550">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="2fe10-551">`0.0.0.0`je zvláštní případ, který se váže ke všem adresám IPv4.</span><span class="sxs-lookup"><span data-stu-id="2fe10-551">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="2fe10-552">Adresa IPv6 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="2fe10-552">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="2fe10-553">`[::]`je ekvivalentem protokolu IPv4 pro protokol IPv6 `0.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="2fe10-553">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="2fe10-554">Název hostitele s číslem portu</span><span class="sxs-lookup"><span data-stu-id="2fe10-554">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="2fe10-555">Názvy hostitelů, `*` a `+` nejsou speciální.</span><span class="sxs-lookup"><span data-stu-id="2fe10-555">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="2fe10-556">Cokoli se nerozpoznalo jako platná IP adresa nebo `localhost` se váže ke všem IP adresám IPv4 a IPv6.</span><span class="sxs-lookup"><span data-stu-id="2fe10-556">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="2fe10-557">Pokud chcete navazovat různé názvy hostitelů na různé ASP.NET Core aplikace na stejném portu, použijte [http. sys](xref:fundamentals/servers/httpsys) nebo reverzní proxy server, třeba IIS, Nginx nebo Apache.</span><span class="sxs-lookup"><span data-stu-id="2fe10-557">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="2fe10-558">Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="2fe10-558">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="2fe10-559">`localhost`Název hostitele s číslem portu nebo IP adresou zpětné smyčky s číslem portu</span><span class="sxs-lookup"><span data-stu-id="2fe10-559">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="2fe10-560">Když `localhost` je zadaný, Kestrel se pokusí vytvořit propojení s rozhraními zpětné smyčky IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="2fe10-560">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="2fe10-561">Pokud je požadovaný port používán jinou službou na rozhraní zpětné smyčky, spuštění Kestrel se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="2fe10-561">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="2fe10-562">Pokud je rozhraní zpětné smyčky v žádném jiném případě nedostupné (většinou kvůli tomu, že protokol IPv6 není podporovaný), Kestrel zaznamená upozornění.</span><span class="sxs-lookup"><span data-stu-id="2fe10-562">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="2fe10-563">Filtrování hostitele</span><span class="sxs-lookup"><span data-stu-id="2fe10-563">Host filtering</span></span>

<span data-ttu-id="2fe10-564">I když Kestrel podporuje konfiguraci na základě prefixů `http://example.com:5000` , jako je, Kestrel převážně ignoruje název hostitele.</span><span class="sxs-lookup"><span data-stu-id="2fe10-564">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="2fe10-565">Hostitel `localhost` je zvláštní případ, který se používá pro vazbu na adresy zpětné smyčky.</span><span class="sxs-lookup"><span data-stu-id="2fe10-565">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="2fe10-566">Každý hostitel, který je jiný než explicitní IP adresa, se váže na všechny veřejné IP adresy.</span><span class="sxs-lookup"><span data-stu-id="2fe10-566">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="2fe10-567">`Host`hlavičky nejsou ověřené.</span><span class="sxs-lookup"><span data-stu-id="2fe10-567">`Host` headers aren't validated.</span></span>

<span data-ttu-id="2fe10-568">Jako alternativní řešení použijte middleware pro filtrování hostitele.</span><span class="sxs-lookup"><span data-stu-id="2fe10-568">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="2fe10-569">Middleware pro filtrování hostitele poskytuje balíček [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , který je implicitně k dispozici pro ASP.NET Core aplikace.</span><span class="sxs-lookup"><span data-stu-id="2fe10-569">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is implicitly provided for ASP.NET Core apps.</span></span> <span data-ttu-id="2fe10-570">Middleware je přidána pomocí <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> , který volá <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> :</span><span class="sxs-lookup"><span data-stu-id="2fe10-570">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="2fe10-571">Middleware pro filtrování hostitele je ve výchozím nastavení zakázáno.</span><span class="sxs-lookup"><span data-stu-id="2fe10-571">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="2fe10-572">Chcete-li povolit middleware, definujte `AllowedHosts` klíč v souboru *appSettings. JSON* / *appSettings. \<EnvironmentName> JSON*.</span><span class="sxs-lookup"><span data-stu-id="2fe10-572">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="2fe10-573">Hodnota je seznam názvů hostitelů oddělených středníkem bez čísel portů:</span><span class="sxs-lookup"><span data-stu-id="2fe10-573">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="2fe10-574">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="2fe10-574">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="2fe10-575">[Middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer) má také <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> možnost.</span><span class="sxs-lookup"><span data-stu-id="2fe10-575">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="2fe10-576">Middleware předávaných hlaviček a middleware pro filtrování hostitele mají podobné funkce pro různé scénáře.</span><span class="sxs-lookup"><span data-stu-id="2fe10-576">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="2fe10-577">Nastavení `AllowedHosts` pomocí předávaných middlewarových hlaviček je vhodné v případě, že `Host` Hlavička není zachována při předávání požadavků pomocí reverzního proxy server nebo nástroje pro vyrovnávání zatížení.</span><span class="sxs-lookup"><span data-stu-id="2fe10-577">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="2fe10-578">Nastavení `AllowedHosts` pomocí middlewaru pro filtrování hostitele je vhodné, když se Kestrel používá jako veřejný hraniční Server nebo když se `Host` Hlavička přímo přepošle.</span><span class="sxs-lookup"><span data-stu-id="2fe10-578">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="2fe10-579">Další informace o middlewaru předávaných hlaviček najdete v tématu <xref:host-and-deploy/proxy-load-balancer> .</span><span class="sxs-lookup"><span data-stu-id="2fe10-579">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="2fe10-580">Kestrel je [webový server pro různé platformy pro ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="2fe10-580">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="2fe10-581">Kestrel je webový server, který je ve výchozím nastavení součástí ASP.NET Core šablon projektů.</span><span class="sxs-lookup"><span data-stu-id="2fe10-581">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="2fe10-582">Kestrel podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="2fe10-582">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="2fe10-583">HTTPS</span><span class="sxs-lookup"><span data-stu-id="2fe10-583">HTTPS</span></span>
* <span data-ttu-id="2fe10-584">Neprůhledný upgrade používaný k povolení [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="2fe10-584">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="2fe10-585">Technologie UNIX Sockets s vysokým výkonem za Nginx</span><span class="sxs-lookup"><span data-stu-id="2fe10-585">Unix sockets for high performance behind Nginx</span></span>
* <span data-ttu-id="2fe10-586">HTTP/2 (kromě macOS &dagger; )</span><span class="sxs-lookup"><span data-stu-id="2fe10-586">HTTP/2 (except on macOS&dagger;)</span></span>

<span data-ttu-id="2fe10-587">&dagger;HTTP/2 se v budoucí verzi podporuje v macOS.</span><span class="sxs-lookup"><span data-stu-id="2fe10-587">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>

<span data-ttu-id="2fe10-588">Kestrel se podporuje na všech platformách a verzích, které podporuje .NET Core.</span><span class="sxs-lookup"><span data-stu-id="2fe10-588">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="2fe10-589">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2fe10-589">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="http2-support"></a><span data-ttu-id="2fe10-590">Podpora HTTP/2</span><span class="sxs-lookup"><span data-stu-id="2fe10-590">HTTP/2 support</span></span>

<span data-ttu-id="2fe10-591">[Http/2](https://httpwg.org/specs/rfc7540.html) je k dispozici pro ASP.NET Core aplikace, pokud jsou splněné následující základní požadavky:</span><span class="sxs-lookup"><span data-stu-id="2fe10-591">[HTTP/2](https://httpwg.org/specs/rfc7540.html) is available for ASP.NET Core apps if the following base requirements are met:</span></span>

* <span data-ttu-id="2fe10-592">Operační systém&dagger;</span><span class="sxs-lookup"><span data-stu-id="2fe10-592">Operating system&dagger;</span></span>
  * <span data-ttu-id="2fe10-593">Windows Server 2016/Windows 10 nebo novější&Dagger;</span><span class="sxs-lookup"><span data-stu-id="2fe10-593">Windows Server 2016/Windows 10 or later&Dagger;</span></span>
  * <span data-ttu-id="2fe10-594">Linux s OpenSSL 1.0.2 nebo novějším (například Ubuntu 16,04 nebo novější)</span><span class="sxs-lookup"><span data-stu-id="2fe10-594">Linux with OpenSSL 1.0.2 or later (for example, Ubuntu 16.04 or later)</span></span>
* <span data-ttu-id="2fe10-595">Cílová architektura: .NET Core 2,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="2fe10-595">Target framework: .NET Core 2.2 or later</span></span>
* <span data-ttu-id="2fe10-596">Připojení [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3)</span><span class="sxs-lookup"><span data-stu-id="2fe10-596">[Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection</span></span>
* <span data-ttu-id="2fe10-597">Připojení TLS 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="2fe10-597">TLS 1.2 or later connection</span></span>

<span data-ttu-id="2fe10-598">&dagger;HTTP/2 se v budoucí verzi podporuje v macOS.</span><span class="sxs-lookup"><span data-stu-id="2fe10-598">&dagger;HTTP/2 will be supported on macOS in a future release.</span></span>
<span data-ttu-id="2fe10-599">&Dagger;Kestrel má omezená podpora HTTP/2 na Windows Serveru 2012 R2 a Windows 8.1.</span><span class="sxs-lookup"><span data-stu-id="2fe10-599">&Dagger;Kestrel has limited support for HTTP/2 on Windows Server 2012 R2 and Windows 8.1.</span></span> <span data-ttu-id="2fe10-600">Podpora je omezená, protože seznam podporovaných šifrovacích sad TLS, které jsou k dispozici v těchto operačních systémech, je omezený.</span><span class="sxs-lookup"><span data-stu-id="2fe10-600">Support is limited because the list of supported TLS cipher suites available on these operating systems is limited.</span></span> <span data-ttu-id="2fe10-601">Pro zabezpečení připojení TLS může být vyžadován certifikát vygenerovaný pomocí algoritmu ECDSA (s připojením typu eliptická křivka).</span><span class="sxs-lookup"><span data-stu-id="2fe10-601">A certificate generated using an Elliptic Curve Digital Signature Algorithm (ECDSA) may be required to secure TLS connections.</span></span>

<span data-ttu-id="2fe10-602">Pokud je vytvořeno připojení HTTP/2, sestavy [HttpRequest. Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) `HTTP/2` .</span><span class="sxs-lookup"><span data-stu-id="2fe10-602">If an HTTP/2 connection is established, [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) reports `HTTP/2`.</span></span>

<span data-ttu-id="2fe10-603">HTTP/2 je ve výchozím nastavení zakázané.</span><span class="sxs-lookup"><span data-stu-id="2fe10-603">HTTP/2 is disabled by default.</span></span> <span data-ttu-id="2fe10-604">Další informace o konfiguraci najdete v částech [Možnosti Kestrel](#kestrel-options) a [ListenOptions. Protocols](#listenoptionsprotocols) .</span><span class="sxs-lookup"><span data-stu-id="2fe10-604">For more information on configuration, see the [Kestrel options](#kestrel-options) and [ListenOptions.Protocols](#listenoptionsprotocols) sections.</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="2fe10-605">Kdy použít Kestrel s reverzním proxy serverem</span><span class="sxs-lookup"><span data-stu-id="2fe10-605">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="2fe10-606">Kestrel může používat sám sebe nebo s *reverzními proxy server*, jako je [Internetová informační služba (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="2fe10-606">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="2fe10-607">Reverzní proxy server přijímá požadavky HTTP ze sítě a přesměruje je na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2fe10-607">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="2fe10-608">Kestrel použitý jako hraniční webový server (internetový):</span><span class="sxs-lookup"><span data-stu-id="2fe10-608">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel komunikuje přímo s internetem bez zpětného proxy server](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="2fe10-610">Kestrel se používá v konfiguraci reverzního proxy serveru:</span><span class="sxs-lookup"><span data-stu-id="2fe10-610">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel nepřímo komunikuje s internetem prostřednictvím reverzní proxy server, jako je IIS, Nginx nebo Apache.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="2fe10-612">Buď konfigurace, s zpětným proxy server nebo bez něj, je podporovaná konfigurace hostování.</span><span class="sxs-lookup"><span data-stu-id="2fe10-612">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="2fe10-613">Kestrel používané jako hraniční Server bez reverzních proxy server nepodporuje sdílení stejné IP adresy a portu mezi více procesy.</span><span class="sxs-lookup"><span data-stu-id="2fe10-613">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="2fe10-614">Když je Kestrel nakonfigurovaný k naslouchání na portu, Kestrel zpracovává veškerý provoz tohoto portu bez ohledu na `Host` hlavičky požadavků.</span><span class="sxs-lookup"><span data-stu-id="2fe10-614">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="2fe10-615">Reverzní proxy server, který může sdílet porty, má schopnost přesměrovat požadavky na Kestrel na jedinečné IP adresy a porty.</span><span class="sxs-lookup"><span data-stu-id="2fe10-615">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="2fe10-616">I v případě, že se zpětný proxy server nepožaduje, může být použití reverzní proxy server vhodnou volbou.</span><span class="sxs-lookup"><span data-stu-id="2fe10-616">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="2fe10-617">Reverzní proxy:</span><span class="sxs-lookup"><span data-stu-id="2fe10-617">A reverse proxy:</span></span>

* <span data-ttu-id="2fe10-618">Může omezit vystavenou veřejnou plochu v aplikacích, které hostuje.</span><span class="sxs-lookup"><span data-stu-id="2fe10-618">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="2fe10-619">Poskytněte další vrstvu konfigurace a obrany.</span><span class="sxs-lookup"><span data-stu-id="2fe10-619">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="2fe10-620">Může se lépe integrovat se stávající infrastrukturou.</span><span class="sxs-lookup"><span data-stu-id="2fe10-620">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="2fe10-621">Zjednodušte vyrovnávání zatížení a konfiguraci zabezpečené komunikace (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="2fe10-621">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="2fe10-622">Pouze reverzní proxy server vyžaduje certifikát X. 509 a tento server může komunikovat se servery aplikace v interní síti pomocí prostého protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fe10-622">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="2fe10-623">Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="2fe10-623">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="2fe10-624">Použití Kestrel v aplikacích ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2fe10-624">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="2fe10-625">Balíček [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) je součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="2fe10-625">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="2fe10-626">ASP.NET Core šablony projektu ve výchozím nastavení používají Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2fe10-626">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="2fe10-627">V *program.cs*kód šablony volá <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> , který volá na pozadí <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> .</span><span class="sxs-lookup"><span data-stu-id="2fe10-627">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_DefaultBuilder&highlight=7)]

<span data-ttu-id="2fe10-628">Další informace o `CreateDefaultBuilder` a sestavení hostitele naleznete v části *Nastavení hostitele* v tématu <xref:fundamentals/host/web-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="2fe10-628">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

<span data-ttu-id="2fe10-629">Chcete-li po volání poskytnout další konfiguraci `CreateDefaultBuilder` , použijte `ConfigureKestrel` :</span><span class="sxs-lookup"><span data-stu-id="2fe10-629">To provide additional configuration after calling `CreateDefaultBuilder`, use `ConfigureKestrel`:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="2fe10-630">Pokud aplikace nevolá `CreateDefaultBuilder` pro nastavení hostitele, zavolejte <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **před** voláním `ConfigureKestrel` :</span><span class="sxs-lookup"><span data-stu-id="2fe10-630">If the app doesn't call `CreateDefaultBuilder` to set up the host, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> **before** calling `ConfigureKestrel`:</span></span>

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

## <a name="kestrel-options"></a><span data-ttu-id="2fe10-631">Kestrel možnosti</span><span class="sxs-lookup"><span data-stu-id="2fe10-631">Kestrel options</span></span>

<span data-ttu-id="2fe10-632">Webový server Kestrel má možnosti konfigurace omezení, které jsou zvláště užitečné v internetových nasazeních.</span><span class="sxs-lookup"><span data-stu-id="2fe10-632">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="2fe10-633">Nastavte omezení pro <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> vlastnost <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> třídy.</span><span class="sxs-lookup"><span data-stu-id="2fe10-633">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="2fe10-634">`Limits`Vlastnost obsahuje instanci <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> třídy.</span><span class="sxs-lookup"><span data-stu-id="2fe10-634">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="2fe10-635">V následujících příkladech se používá <xref:Microsoft.AspNetCore.Server.Kestrel.Core> obor názvů:</span><span class="sxs-lookup"><span data-stu-id="2fe10-635">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="2fe10-636">Možnosti Kestrel, které jsou konfigurovány v kódu jazyka C# v následujících příkladech, lze také nastavit pomocí [poskytovatele konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="2fe10-636">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="2fe10-637">Poskytovatel konfigurace souboru může například načíst konfiguraci Kestrel z objektu *appSettings. JSON* nebo *appSettings. { Soubor Environment}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="2fe10-637">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="2fe10-638">Použijte **jeden** z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="2fe10-638">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="2fe10-639">Nakonfigurovat Kestrel v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2fe10-639">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="2fe10-640">Vloží instanci `IConfiguration` do `Startup` třídy.</span><span class="sxs-lookup"><span data-stu-id="2fe10-640">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="2fe10-641">Následující příklad předpokládá, že vložená konfigurace je přiřazena `Configuration` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="2fe10-641">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="2fe10-642">V aplikaci `Startup.ConfigureServices` načtěte `Kestrel` část konfigurace do konfigurace Kestrel:</span><span class="sxs-lookup"><span data-stu-id="2fe10-642">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="2fe10-643">Nakonfigurovat Kestrel při sestavování hostitele:</span><span class="sxs-lookup"><span data-stu-id="2fe10-643">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="2fe10-644">V *program.cs*načtěte `Kestrel` část konfigurace do konfigurace Kestrel:</span><span class="sxs-lookup"><span data-stu-id="2fe10-644">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="2fe10-645">Obě předchozí přístupy fungují u libovolného [poskytovatele konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="2fe10-645">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="2fe10-646">Časový limit Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="2fe10-646">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="2fe10-647">Získá nebo nastaví [časový limit zachování](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="2fe10-647">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="2fe10-648">Výchozí hodnota je 2 minuty.</span><span class="sxs-lookup"><span data-stu-id="2fe10-648">Defaults to 2 minutes.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=15)]

### <a name="maximum-client-connections"></a><span data-ttu-id="2fe10-649">Maximální počet připojení klientů</span><span class="sxs-lookup"><span data-stu-id="2fe10-649">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="2fe10-650">Maximální počet souběžných otevřených připojení TCP lze pro celou aplikaci nastavit pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="2fe10-650">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=3)]

<span data-ttu-id="2fe10-651">Existuje zvláštní omezení pro připojení, která byla upgradována z protokolu HTTP nebo HTTPS na jiný protokol (například na žádost WebSockets).</span><span class="sxs-lookup"><span data-stu-id="2fe10-651">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="2fe10-652">Po upgradu připojení se nepočítá na základě `MaxConcurrentConnections` limitu.</span><span class="sxs-lookup"><span data-stu-id="2fe10-652">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=4)]

<span data-ttu-id="2fe10-653">Ve výchozím nastavení je maximální počet připojení neomezené (null).</span><span class="sxs-lookup"><span data-stu-id="2fe10-653">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="2fe10-654">Maximální velikost textu požadavku</span><span class="sxs-lookup"><span data-stu-id="2fe10-654">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="2fe10-655">Výchozí maximální velikost textu požadavku je 30 000 000 bajtů, což je přibližně 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="2fe10-655">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="2fe10-656">Doporučený postup pro přepsání limitu v aplikaci ASP.NET Core MVC je použití <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> atributu pro metodu akce:</span><span class="sxs-lookup"><span data-stu-id="2fe10-656">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="2fe10-657">Tady je příklad, který ukazuje, jak nakonfigurovat omezení pro aplikaci na každém požadavku:</span><span class="sxs-lookup"><span data-stu-id="2fe10-657">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=5)]

<span data-ttu-id="2fe10-658">Přepsat nastavení u konkrétního požadavku v middleware:</span><span class="sxs-lookup"><span data-stu-id="2fe10-658">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="2fe10-659">Výjimka je vyvolána, pokud aplikace po zahájení aplikace nakonfiguruje limit na žádost.</span><span class="sxs-lookup"><span data-stu-id="2fe10-659">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="2fe10-660">Existuje `IsReadOnly` vlastnost, která určuje, zda `MaxRequestBodySize` je vlastnost ve stavu jen pro čtení, což znamená, že je příliš pozdě pro konfiguraci limitu.</span><span class="sxs-lookup"><span data-stu-id="2fe10-660">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="2fe10-661">Když je aplikace spouštěna [mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za [ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module), je omezení velikosti textu požadavku Kestrel zakázané, protože služba IIS tento limit již nastavuje.</span><span class="sxs-lookup"><span data-stu-id="2fe10-661">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="2fe10-662">Minimální rychlost těla zprávy požadavku</span><span class="sxs-lookup"><span data-stu-id="2fe10-662">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="2fe10-663">Kestrel kontroluje každou sekundu, pokud data přicházejí za zadanou rychlost v bajtech za sekundu.</span><span class="sxs-lookup"><span data-stu-id="2fe10-663">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="2fe10-664">Pokud frekvence klesne pod minimální hodnotu, vypršel časový limit připojení. Doba odkladu je doba, kterou Kestrel umožňuje klientovi zvýšit rychlost odesílání až na minimum; frekvence není během této doby kontrolována.</span><span class="sxs-lookup"><span data-stu-id="2fe10-664">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="2fe10-665">Doba odkladu pomáhá vyhnout se vyřazení připojení, která při počátečním posílání dat zpomalují rychlost, kvůli pomalému spuštění protokolu TCP.</span><span class="sxs-lookup"><span data-stu-id="2fe10-665">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="2fe10-666">Výchozí minimální sazba je 240 bajtů za sekundu s obdobím 5 sekund odkladu.</span><span class="sxs-lookup"><span data-stu-id="2fe10-666">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="2fe10-667">Pro odpověď platí také minimální sazba.</span><span class="sxs-lookup"><span data-stu-id="2fe10-667">A minimum rate also applies to the response.</span></span> <span data-ttu-id="2fe10-668">Kód pro nastavení limitu požadavku a limit odezvy je stejný, s výjimkou, `RequestBody` že existují nebo `Response` v názvech vlastností a rozhraní.</span><span class="sxs-lookup"><span data-stu-id="2fe10-668">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="2fe10-669">Tady je příklad, který ukazuje, jak nakonfigurovat minimální datové tarify v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="2fe10-669">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=6-9)]

<span data-ttu-id="2fe10-670">Přepsat minimální limity přenosové rychlosti na žádost v middlewaru:</span><span class="sxs-lookup"><span data-stu-id="2fe10-670">Override the minimum rate limits per request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=6-21)]

<span data-ttu-id="2fe10-671">Pro požadavky HTTP/2 se v předchozí ukázce neodkazuje žádná míra funkce, `HttpContext.Features` protože Změna omezení přenosové rychlosti na základě jednotlivých požadavků není podporovaná pro HTTP/2 kvůli podpoře protokolu pro multiplexování požadavků.</span><span class="sxs-lookup"><span data-stu-id="2fe10-671">Neither rate feature referenced in the prior sample are present in `HttpContext.Features` for HTTP/2 requests because modifying rate limits on a per-request basis isn't supported for HTTP/2 due to the protocol's support for request multiplexing.</span></span> <span data-ttu-id="2fe10-672">Omezení přenosové rychlosti pro všechny servery jsou nakonfigurovaná přes `KestrelServerOptions.Limits` protokol HTTP/1. x i pro připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="2fe10-672">Server-wide rate limits configured via `KestrelServerOptions.Limits` still apply to both HTTP/1.x and HTTP/2 connections.</span></span>

### <a name="request-headers-timeout"></a><span data-ttu-id="2fe10-673">Časový limit hlaviček žádostí</span><span class="sxs-lookup"><span data-stu-id="2fe10-673">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="2fe10-674">Získá nebo nastaví maximální dobu, po kterou server stráví příjem hlaviček požadavků.</span><span class="sxs-lookup"><span data-stu-id="2fe10-674">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="2fe10-675">Výchozí hodnota je 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="2fe10-675">Defaults to 30 seconds.</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_Limits&highlight=16)]

### <a name="maximum-streams-per-connection"></a><span data-ttu-id="2fe10-676">Maximální počet proudů na připojení</span><span class="sxs-lookup"><span data-stu-id="2fe10-676">Maximum streams per connection</span></span>

<span data-ttu-id="2fe10-677">`Http2.MaxStreamsPerConnection`omezuje počet souběžných datových proudů požadavků na připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="2fe10-677">`Http2.MaxStreamsPerConnection` limits the number of concurrent request streams per HTTP/2 connection.</span></span> <span data-ttu-id="2fe10-678">Nadbytečné proudy jsou odmítnuty.</span><span class="sxs-lookup"><span data-stu-id="2fe10-678">Excess streams are refused.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxStreamsPerConnection = 100;
        });
```

<span data-ttu-id="2fe10-679">Výchozí hodnota je 100.</span><span class="sxs-lookup"><span data-stu-id="2fe10-679">The default value is 100.</span></span>

### <a name="header-table-size"></a><span data-ttu-id="2fe10-680">Velikost tabulky hlaviček</span><span class="sxs-lookup"><span data-stu-id="2fe10-680">Header table size</span></span>

<span data-ttu-id="2fe10-681">Dekodér HPACK dekomprimuje hlavičky HTTP pro připojení HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="2fe10-681">The HPACK decoder decompresses HTTP headers for HTTP/2 connections.</span></span> <span data-ttu-id="2fe10-682">`Http2.HeaderTableSize`omezuje velikost kompresní tabulky hlaviček, kterou používá dekodér HPACK.</span><span class="sxs-lookup"><span data-stu-id="2fe10-682">`Http2.HeaderTableSize` limits the size of the header compression table that the HPACK decoder uses.</span></span> <span data-ttu-id="2fe10-683">Hodnota je uvedena v oktetech a musí být větší než nula (0).</span><span class="sxs-lookup"><span data-stu-id="2fe10-683">The value is provided in octets and must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.HeaderTableSize = 4096;
        });
```

<span data-ttu-id="2fe10-684">Výchozí hodnota je 4096.</span><span class="sxs-lookup"><span data-stu-id="2fe10-684">The default value is 4096.</span></span>

### <a name="maximum-frame-size"></a><span data-ttu-id="2fe10-685">Maximální velikost rámce</span><span class="sxs-lookup"><span data-stu-id="2fe10-685">Maximum frame size</span></span>

<span data-ttu-id="2fe10-686">`Http2.MaxFrameSize`Určuje maximální velikost datové části rámce připojení HTTP/2, která se má přijmout.</span><span class="sxs-lookup"><span data-stu-id="2fe10-686">`Http2.MaxFrameSize` indicates the maximum size of the HTTP/2 connection frame payload to receive.</span></span> <span data-ttu-id="2fe10-687">Hodnota je uvedena v oktetech a musí být v rozmezí 2 ^ 14 (16 384) až 2 ^ 24-1 (16 777 215).</span><span class="sxs-lookup"><span data-stu-id="2fe10-687">The value is provided in octets and must be between 2^14 (16,384) and 2^24-1 (16,777,215).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxFrameSize = 16384;
        });
```

<span data-ttu-id="2fe10-688">Výchozí hodnota je 2 ^ 14 (16 384).</span><span class="sxs-lookup"><span data-stu-id="2fe10-688">The default value is 2^14 (16,384).</span></span>

### <a name="maximum-request-header-size"></a><span data-ttu-id="2fe10-689">Maximální velikost hlavičky požadavku</span><span class="sxs-lookup"><span data-stu-id="2fe10-689">Maximum request header size</span></span>

<span data-ttu-id="2fe10-690">`Http2.MaxRequestHeaderFieldSize`Určuje maximální povolenou velikost v oktetech hodnot hlaviček požadavku.</span><span class="sxs-lookup"><span data-stu-id="2fe10-690">`Http2.MaxRequestHeaderFieldSize` indicates the maximum allowed size in octets of request header values.</span></span> <span data-ttu-id="2fe10-691">Toto omezení platí pro názvy i hodnoty společně v komprimovaných a nekomprimovaných reprezentujcích.</span><span class="sxs-lookup"><span data-stu-id="2fe10-691">This limit applies to both name and value together in their compressed and uncompressed representations.</span></span> <span data-ttu-id="2fe10-692">Hodnota musí být větší než nula (0).</span><span class="sxs-lookup"><span data-stu-id="2fe10-692">The value must be greater than zero (0).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.MaxRequestHeaderFieldSize = 8192;
        });
```

<span data-ttu-id="2fe10-693">Výchozí hodnota je 8 192.</span><span class="sxs-lookup"><span data-stu-id="2fe10-693">The default value is 8,192.</span></span>

### <a name="initial-connection-window-size"></a><span data-ttu-id="2fe10-694">Velikost okna počátečního připojení</span><span class="sxs-lookup"><span data-stu-id="2fe10-694">Initial connection window size</span></span>

<span data-ttu-id="2fe10-695">`Http2.InitialConnectionWindowSize`Určuje maximální velikost textu požadavku v bajtech, které jsou v jednom okamžiku agregovány v rámci všech požadavků (datových proudů) na připojení.</span><span class="sxs-lookup"><span data-stu-id="2fe10-695">`Http2.InitialConnectionWindowSize` indicates the maximum request body data in bytes the server buffers at one time aggregated across all requests (streams) per connection.</span></span> <span data-ttu-id="2fe10-696">Žádosti jsou také omezené pomocí `Http2.InitialStreamWindowSize` .</span><span class="sxs-lookup"><span data-stu-id="2fe10-696">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="2fe10-697">Hodnota musí být větší než nebo rovna 65 535 a menší než 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="2fe10-697">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialConnectionWindowSize = 131072;
        });
```

<span data-ttu-id="2fe10-698">Výchozí hodnota je 128 KB (131 072).</span><span class="sxs-lookup"><span data-stu-id="2fe10-698">The default value is 128 KB (131,072).</span></span>

### <a name="initial-stream-window-size"></a><span data-ttu-id="2fe10-699">Počáteční velikost okna datového proudu</span><span class="sxs-lookup"><span data-stu-id="2fe10-699">Initial stream window size</span></span>

<span data-ttu-id="2fe10-700">`Http2.InitialStreamWindowSize`Určuje maximální velikost textu požadavku v bajtech na jednu žádost (Stream) na jednom místě.</span><span class="sxs-lookup"><span data-stu-id="2fe10-700">`Http2.InitialStreamWindowSize` indicates the maximum request body data in bytes the server buffers at one time per request (stream).</span></span> <span data-ttu-id="2fe10-701">Žádosti jsou také omezené pomocí `Http2.InitialStreamWindowSize` .</span><span class="sxs-lookup"><span data-stu-id="2fe10-701">Requests are also limited by `Http2.InitialStreamWindowSize`.</span></span> <span data-ttu-id="2fe10-702">Hodnota musí být větší než nebo rovna 65 535 a menší než 2 ^ 31 (2 147 483 648).</span><span class="sxs-lookup"><span data-stu-id="2fe10-702">The value must be greater than or equal to 65,535 and less than 2^31 (2,147,483,648).</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .ConfigureKestrel((context, serverOptions) =>
        {
            serverOptions.Limits.Http2.InitialStreamWindowSize = 98304;
        });
```

<span data-ttu-id="2fe10-703">Výchozí hodnota je 96 KB (98 304).</span><span class="sxs-lookup"><span data-stu-id="2fe10-703">The default value is 96 KB (98,304).</span></span>

### <a name="synchronous-io"></a><span data-ttu-id="2fe10-704">Synchronní vstupně-výstupní operace</span><span class="sxs-lookup"><span data-stu-id="2fe10-704">Synchronous I/O</span></span>

<span data-ttu-id="2fe10-705"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>Určuje, jestli je pro požadavek a odpověď povolený synchronní vstup/výstup.</span><span class="sxs-lookup"><span data-stu-id="2fe10-705"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="2fe10-706">Výchozí hodnota je `true` .</span><span class="sxs-lookup"><span data-stu-id="2fe10-706">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="2fe10-707">Velký počet blokujících synchronních vstupně-výstupních operací může vést ke vyčerpání fondu vláken, což aplikaci nereaguje.</span><span class="sxs-lookup"><span data-stu-id="2fe10-707">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="2fe10-708">Povolit pouze `AllowSynchronousIO` při použití knihovny, která nepodporuje asynchronní vstupně-výstupní operace.</span><span class="sxs-lookup"><span data-stu-id="2fe10-708">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="2fe10-709">Následující příklad povoluje synchronní vstupně-výstupní operace:</span><span class="sxs-lookup"><span data-stu-id="2fe10-709">The following example enables synchronous I/O:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_SyncIO)]

<span data-ttu-id="2fe10-710">Informace o dalších možnostech a omezeních Kestrel najdete v tématech:</span><span class="sxs-lookup"><span data-stu-id="2fe10-710">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="2fe10-711">Konfigurace koncového bodu</span><span class="sxs-lookup"><span data-stu-id="2fe10-711">Endpoint configuration</span></span>

<span data-ttu-id="2fe10-712">Ve výchozím nastavení ASP.NET Core váže k:</span><span class="sxs-lookup"><span data-stu-id="2fe10-712">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="2fe10-713">`https://localhost:5001`(Pokud je k dispozici místní vývojový certifikát)</span><span class="sxs-lookup"><span data-stu-id="2fe10-713">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="2fe10-714">Zadejte adresy URL pomocí:</span><span class="sxs-lookup"><span data-stu-id="2fe10-714">Specify URLs using the:</span></span>

* <span data-ttu-id="2fe10-715">`ASPNETCORE_URLS`Proměnná prostředí.</span><span class="sxs-lookup"><span data-stu-id="2fe10-715">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="2fe10-716">`--urls`argument příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="2fe10-716">`--urls` command-line argument.</span></span>
* <span data-ttu-id="2fe10-717">`urls`konfigurační klíč hostitele.</span><span class="sxs-lookup"><span data-stu-id="2fe10-717">`urls` host configuration key.</span></span>
* <span data-ttu-id="2fe10-718">`UseUrls`Metoda rozšíření</span><span class="sxs-lookup"><span data-stu-id="2fe10-718">`UseUrls` extension method.</span></span>

<span data-ttu-id="2fe10-719">Hodnota zadaná pomocí těchto přístupů může být jeden nebo více koncových bodů HTTP a HTTPS (HTTPS, pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="2fe10-719">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="2fe10-720">Nakonfigurujte hodnotu jako seznam oddělený středníkem (například `"Urls": "http://localhost:8000;http://localhost:8001"` ).</span><span class="sxs-lookup"><span data-stu-id="2fe10-720">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="2fe10-721">Další informace o těchto přístupůch najdete v tématu [adresy URL serveru](xref:fundamentals/host/web-host#server-urls) a [potlačení konfigurace](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="2fe10-721">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="2fe10-722">Vytvoří se vývojový certifikát:</span><span class="sxs-lookup"><span data-stu-id="2fe10-722">A development certificate is created:</span></span>

* <span data-ttu-id="2fe10-723">Po instalaci [.NET Core SDK](/dotnet/core/sdk) .</span><span class="sxs-lookup"><span data-stu-id="2fe10-723">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="2fe10-724">[Nástroj pro vývoj a certifikáty](xref:aspnetcore-2.1#https) slouží k vytvoření certifikátu.</span><span class="sxs-lookup"><span data-stu-id="2fe10-724">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="2fe10-725">Některé prohlížeče vyžadují udělení explicitního oprávnění pro důvěřování místního vývojového certifikátu.</span><span class="sxs-lookup"><span data-stu-id="2fe10-725">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="2fe10-726">Šablony projektů konfigurují aplikace tak, aby běžely ve výchozím nastavení HTTPS a zahrnovaly [přesměrování https a podporu HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="2fe10-726">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="2fe10-727">Volání <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> nebo <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> metody pro <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> konfiguraci předpon adres URL a portů pro Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2fe10-727">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="2fe10-728">`UseUrls`, `--urls` argument příkazového řádku, `urls` konfigurační klíč hostitele a `ASPNETCORE_URLS` Proměnná prostředí fungují také, ale mají omezení zaznamenaná později v této části (pro konfiguraci koncového bodu https musí být k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="2fe10-728">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="2fe10-729">`KestrelServerOptions`rozšířeného</span><span class="sxs-lookup"><span data-stu-id="2fe10-729">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="2fe10-730">ConfigureEndpointDefaults (akce \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="2fe10-730">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="2fe10-731">Určuje konfiguraci `Action` , která se spustí pro každý zadaný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="2fe10-731">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="2fe10-732">Volání několikrát `ConfigureEndpointDefaults` nahrazuje předchozí `Action` `Action` zadanou parametr s.</span><span class="sxs-lookup"><span data-stu-id="2fe10-732">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="2fe10-733">Koncové body vytvořené voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **před** voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> nebudou použity výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="2fe10-733">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="2fe10-734">ConfigureHttpsDefaults (akce \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="2fe10-734">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="2fe10-735">Určuje konfiguraci `Action` , která se má spustit pro každý koncový bod HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2fe10-735">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="2fe10-736">Volání několikrát `ConfigureHttpsDefaults` nahrazuje předchozí `Action` `Action` zadanou parametr s.</span><span class="sxs-lookup"><span data-stu-id="2fe10-736">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="2fe10-737">Koncové body vytvořené voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **před** voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> nebudou použity výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="2fe10-737">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>


### <a name="configureiconfiguration"></a><span data-ttu-id="2fe10-738">Konfigurace (IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="2fe10-738">Configure(IConfiguration)</span></span>

<span data-ttu-id="2fe10-739">Vytvoří zavaděč konfigurace pro nastavení Kestrel, který přebírá <xref:Microsoft.Extensions.Configuration.IConfiguration> jako vstup.</span><span class="sxs-lookup"><span data-stu-id="2fe10-739">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="2fe10-740">Konfigurace musí být vymezená na konfigurační oddíl pro Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2fe10-740">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="2fe10-741">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="2fe10-741">ListenOptions.UseHttps</span></span>

<span data-ttu-id="2fe10-742">Nakonfigurujte Kestrel tak, aby používal protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2fe10-742">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="2fe10-743">`ListenOptions.UseHttps`SND</span><span class="sxs-lookup"><span data-stu-id="2fe10-743">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="2fe10-744">`UseHttps`: Nakonfigurujte Kestrel tak, aby používal HTTPS s výchozím certifikátem.</span><span class="sxs-lookup"><span data-stu-id="2fe10-744">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="2fe10-745">Vyvolá výjimku, pokud není nakonfigurován žádný výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="2fe10-745">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="2fe10-746">`ListenOptions.UseHttps`ukazatelů</span><span class="sxs-lookup"><span data-stu-id="2fe10-746">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="2fe10-747">`filename`je cesta a název souboru certifikátu vzhledem k adresáři, který obsahuje soubory obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="2fe10-747">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="2fe10-748">`password`je vyžadováno heslo pro přístup k datům certifikátu X. 509.</span><span class="sxs-lookup"><span data-stu-id="2fe10-748">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="2fe10-749">`configureOptions`je `Action` ke konfiguraci `HttpsConnectionAdapterOptions` .</span><span class="sxs-lookup"><span data-stu-id="2fe10-749">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="2fe10-750">Vrátí `ListenOptions` .</span><span class="sxs-lookup"><span data-stu-id="2fe10-750">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="2fe10-751">`storeName`je úložiště certifikátů, ze kterého se má certifikát načíst.</span><span class="sxs-lookup"><span data-stu-id="2fe10-751">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="2fe10-752">`subject`je název subjektu certifikátu.</span><span class="sxs-lookup"><span data-stu-id="2fe10-752">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="2fe10-753">`allowInvalid`Určuje, zda by měly být zváženy neplatné certifikáty, například certifikáty podepsané svým držitelem.</span><span class="sxs-lookup"><span data-stu-id="2fe10-753">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="2fe10-754">`location`je umístěním úložiště, ze kterého se má certifikát načíst.</span><span class="sxs-lookup"><span data-stu-id="2fe10-754">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="2fe10-755">`serverCertificate`je certifikát X. 509.</span><span class="sxs-lookup"><span data-stu-id="2fe10-755">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="2fe10-756">V produkčním prostředí musí být HTTPS explicitně nakonfigurovaný.</span><span class="sxs-lookup"><span data-stu-id="2fe10-756">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="2fe10-757">Musí být zadán minimálně výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="2fe10-757">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="2fe10-758">Podporované konfigurace popsané dál:</span><span class="sxs-lookup"><span data-stu-id="2fe10-758">Supported configurations described next:</span></span>

* <span data-ttu-id="2fe10-759">Žádná konfigurace</span><span class="sxs-lookup"><span data-stu-id="2fe10-759">No configuration</span></span>
* <span data-ttu-id="2fe10-760">Nahradit výchozí certifikát z konfigurace</span><span class="sxs-lookup"><span data-stu-id="2fe10-760">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="2fe10-761">Změna výchozích hodnot v kódu</span><span class="sxs-lookup"><span data-stu-id="2fe10-761">Change the defaults in code</span></span>

<span data-ttu-id="2fe10-762">*Žádná konfigurace*</span><span class="sxs-lookup"><span data-stu-id="2fe10-762">*No configuration*</span></span>

<span data-ttu-id="2fe10-763">Kestrel naslouchá na `http://localhost:5000` a `https://localhost:5001` (Pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="2fe10-763">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="2fe10-764">*Nahradit výchozí certifikát z konfigurace*</span><span class="sxs-lookup"><span data-stu-id="2fe10-764">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="2fe10-765">`CreateDefaultBuilder`Při `Configure(context.Configuration.GetSection("Kestrel"))` načítání konfigurace Kestrel ve výchozím nastavení volá.</span><span class="sxs-lookup"><span data-stu-id="2fe10-765">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="2fe10-766">Pro Kestrel je k dispozici výchozí schéma konfigurace nastavení aplikace HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2fe10-766">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="2fe10-767">Nakonfigurujte několik koncových bodů, včetně adres URL a certifikátů, které se mají použít, buď ze souboru na disku, nebo z úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="2fe10-767">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="2fe10-768">V následujícím příkladu *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="2fe10-768">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="2fe10-769">Nastavte **AllowInvalid** na `true` , aby povolovala použití neplatných certifikátů (například certifikátů podepsaných svým držitelem).</span><span class="sxs-lookup"><span data-stu-id="2fe10-769">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="2fe10-770">Libovolný koncový bod HTTPS, který neurčuje certifikát (**HttpsDefaultCert** v následujícím příkladu), se vrátí k certifikátu definovanému v části **certifikáty** > **výchozí** nebo vývojový certifikát.</span><span class="sxs-lookup"><span data-stu-id="2fe10-770">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="2fe10-771">Alternativou k použití **cesty** a **hesla** pro libovolný uzel certifikátu je zadání certifikátu pomocí polí úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="2fe10-771">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="2fe10-772">Například **Certificates**  >  **výchozí** certifikát může být zadán jako:</span><span class="sxs-lookup"><span data-stu-id="2fe10-772">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="2fe10-773">Poznámky ke schématu:</span><span class="sxs-lookup"><span data-stu-id="2fe10-773">Schema notes:</span></span>

* <span data-ttu-id="2fe10-774">V názvech koncových bodů se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="2fe10-774">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="2fe10-775">Například `HTTPS` a `Https` jsou platné.</span><span class="sxs-lookup"><span data-stu-id="2fe10-775">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="2fe10-776">`Url`U každého koncového bodu je vyžadován parametr.</span><span class="sxs-lookup"><span data-stu-id="2fe10-776">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="2fe10-777">Formát pro tento parametr je stejný jako `Urls` konfigurační parametr nejvyšší úrovně s tím rozdílem, že je omezen na jedinou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="2fe10-777">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="2fe10-778">Tyto koncové body nahrazují hodnoty definované v konfiguraci nejvyšší úrovně `Urls` místo jejich přidání do těchto koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="2fe10-778">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="2fe10-779">Koncové body definované v kódu prostřednictvím `Listen` jsou kumulativní pomocí koncových bodů definovaných v konfiguračním oddílu.</span><span class="sxs-lookup"><span data-stu-id="2fe10-779">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="2fe10-780">`Certificate`Oddíl je nepovinný.</span><span class="sxs-lookup"><span data-stu-id="2fe10-780">The `Certificate` section is optional.</span></span> <span data-ttu-id="2fe10-781">Pokud `Certificate` není oddíl zadaný, použijí se výchozí hodnoty definované v dřívějších scénářích.</span><span class="sxs-lookup"><span data-stu-id="2fe10-781">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="2fe10-782">Pokud nejsou k dispozici žádné výchozí hodnoty, Server vyvolá výjimku a nemůže se spustit.</span><span class="sxs-lookup"><span data-stu-id="2fe10-782">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="2fe10-783">`Certificate`Oddíl podporuje jak heslo **cesty** &ndash; **Password** , tak **Subject** &ndash; certifikáty**úložiště** subjektu.</span><span class="sxs-lookup"><span data-stu-id="2fe10-783">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="2fe10-784">V takovém případě může být definován libovolný počet koncových bodů, pokud to nezpůsobí konflikty portů.</span><span class="sxs-lookup"><span data-stu-id="2fe10-784">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="2fe10-785">`options.Configure(context.Configuration.GetSection("{SECTION}"))`Vrátí `KestrelConfigurationLoader` `.Endpoint(string name, listenOptions => { })` metodu s metodou, kterou lze použít k doplnění nastavení nakonfigurovaného koncového bodu:</span><span class="sxs-lookup"><span data-stu-id="2fe10-785">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="2fe10-786">`KestrelServerOptions.ConfigurationLoader`k pokračování v iteraci u stávajícího zavaděče se dá získat přímý odkaz, jako je třeba ten, který poskytuje <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="2fe10-786">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="2fe10-787">Konfigurační oddíl pro každý koncový bod je k dispozici na možnostech v `Endpoint` metodě, aby bylo možné číst vlastní nastavení.</span><span class="sxs-lookup"><span data-stu-id="2fe10-787">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="2fe10-788">Více konfigurací může být načteno voláním `options.Configure(context.Configuration.GetSection("{SECTION}"))` znovu s jiným oddílem.</span><span class="sxs-lookup"><span data-stu-id="2fe10-788">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="2fe10-789">Je použita pouze poslední konfigurace, pokud `Load` není explicitně volána při předchozích instancích.</span><span class="sxs-lookup"><span data-stu-id="2fe10-789">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="2fe10-790">Metapackage nevolá `Load` , aby se jeho výchozí konfigurační oddíl mohl nahradit.</span><span class="sxs-lookup"><span data-stu-id="2fe10-790">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="2fe10-791">`KestrelConfigurationLoader`zrcadlí `Listen` rodinu rozhraní API z `KestrelServerOptions` as `Endpoint` přetížení, takže koncové body kódu a konfigurace lze konfigurovat na stejném místě.</span><span class="sxs-lookup"><span data-stu-id="2fe10-791">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="2fe10-792">Tato přetížení nepoužívají názvy a využívají pouze výchozí nastavení z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="2fe10-792">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="2fe10-793">*Změna výchozích hodnot v kódu*</span><span class="sxs-lookup"><span data-stu-id="2fe10-793">*Change the defaults in code*</span></span>

<span data-ttu-id="2fe10-794">`ConfigureEndpointDefaults`a `ConfigureHttpsDefaults` dá se použít ke změně výchozího nastavení pro `ListenOptions` a `HttpsConnectionAdapterOptions` , včetně přepsání výchozího certifikátu zadaného v předchozím scénáři.</span><span class="sxs-lookup"><span data-stu-id="2fe10-794">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="2fe10-795">`ConfigureEndpointDefaults`a `ConfigureHttpsDefaults` měla by být volána před konfigurací koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="2fe10-795">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="2fe10-796">*Podpora Kestrel pro SNI*</span><span class="sxs-lookup"><span data-stu-id="2fe10-796">*Kestrel support for SNI*</span></span>

<span data-ttu-id="2fe10-797">[Indikace názvu serveru (SNI)](https://tools.ietf.org/html/rfc6066#section-3) lze použít k hostování více domén na stejné IP adrese a portu.</span><span class="sxs-lookup"><span data-stu-id="2fe10-797">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="2fe10-798">Aby SNI fungovalo, klient pošle název hostitele pro zabezpečenou relaci serveru během metody handshake TLS, aby server mohl poskytovat správný certifikát.</span><span class="sxs-lookup"><span data-stu-id="2fe10-798">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="2fe10-799">Klient používá poskytnutý certifikát pro šifrovanou komunikaci se serverem během zabezpečené relace, která následuje za metodou handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="2fe10-799">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="2fe10-800">Kestrel podporuje SNI prostřednictvím `ServerCertificateSelector` zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="2fe10-800">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="2fe10-801">Zpětné volání je vyvoláno jednou za připojení, které aplikaci umožní zkontrolovat název hostitele a vybrat příslušný certifikát.</span><span class="sxs-lookup"><span data-stu-id="2fe10-801">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="2fe10-802">Podpora SNI vyžaduje:</span><span class="sxs-lookup"><span data-stu-id="2fe10-802">SNI support requires:</span></span>

* <span data-ttu-id="2fe10-803">Běží v cílovém rozhraní `netcoreapp2.1` nebo novějším.</span><span class="sxs-lookup"><span data-stu-id="2fe10-803">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="2fe10-804">V systému `net461` nebo novějším je zpětné volání vyvoláno, ale `name` je vždy `null` .</span><span class="sxs-lookup"><span data-stu-id="2fe10-804">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="2fe10-805">`name`Je také v `null` případě, že klient v TLS handshake neposkytne parametr názvu hostitele.</span><span class="sxs-lookup"><span data-stu-id="2fe10-805">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="2fe10-806">Všechny weby běží na stejné instanci Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2fe10-806">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="2fe10-807">Kestrel nepodporuje sdílení IP adresy a portu napříč několika instancemi bez reverzního proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="2fe10-807">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="2fe10-808">Protokolování připojení</span><span class="sxs-lookup"><span data-stu-id="2fe10-808">Connection logging</span></span>

<span data-ttu-id="2fe10-809">Volá <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> se, aby se vygenerovaly protokoly na úrovni ladění pro komunikaci na úrovni bajtového připojení.</span><span class="sxs-lookup"><span data-stu-id="2fe10-809">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="2fe10-810">Protokolování připojení je užitečné při řešení problémů v komunikaci nízké úrovně, například během šifrování TLS a za proxy servery.</span><span class="sxs-lookup"><span data-stu-id="2fe10-810">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="2fe10-811">Pokud `UseConnectionLogging` je umístěn před `UseHttps` , zaprotokoluje se zašifrovaný provoz.</span><span class="sxs-lookup"><span data-stu-id="2fe10-811">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="2fe10-812">Pokud `UseConnectionLogging` je umístěn po `UseHttps` , zaprotokoluje se dešifrovaný provoz.</span><span class="sxs-lookup"><span data-stu-id="2fe10-812">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="2fe10-813">Vytvoření vazby na soket TCP</span><span class="sxs-lookup"><span data-stu-id="2fe10-813">Bind to a TCP socket</span></span>

<span data-ttu-id="2fe10-814"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>Metoda se váže k soketu TCP a pomocí lambda možností povolíte konfiguraci certifikátu X. 509:</span><span class="sxs-lookup"><span data-stu-id="2fe10-814">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_TCPSocket&highlight=9-16)]

<span data-ttu-id="2fe10-815">Příklad nakonfiguruje HTTPS pro koncový bod pomocí <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> .</span><span class="sxs-lookup"><span data-stu-id="2fe10-815">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="2fe10-816">Ke konfiguraci dalších nastavení Kestrel pro konkrétní koncové body použijte stejné rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="2fe10-816">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="2fe10-817">Vytvoření vazby na soket UNIX</span><span class="sxs-lookup"><span data-stu-id="2fe10-817">Bind to a Unix socket</span></span>

<span data-ttu-id="2fe10-818">Naslouchat na soketu se systémem UNIX <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> , aby se zlepšil výkon pomocí Nginx, jak je znázorněno v tomto příkladu:</span><span class="sxs-lookup"><span data-stu-id="2fe10-818">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Program.cs?name=snippet_UnixSocket)]

* <span data-ttu-id="2fe10-819">V souboru Nginx confiuguration nastavte `server`  >  `location`  >  `proxy_pass` položku na `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="2fe10-819">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="2fe10-820">`{KESTREL SOCKET}`je název soketu poskytnutý <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (například `kestrel-test.sock` v předchozím příkladu).</span><span class="sxs-lookup"><span data-stu-id="2fe10-820">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="2fe10-821">Zajistěte, aby byl soket zapisovatelný pomocí Nginx (například `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="2fe10-821">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="2fe10-822">Port 0</span><span class="sxs-lookup"><span data-stu-id="2fe10-822">Port 0</span></span>

<span data-ttu-id="2fe10-823">Po zadání čísla portu se `0` Kestrel dynamicky váže k dostupnému portu.</span><span class="sxs-lookup"><span data-stu-id="2fe10-823">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="2fe10-824">Následující příklad ukazuje, jak určit, který port Kestrel je skutečně vázaný za běhu:</span><span class="sxs-lookup"><span data-stu-id="2fe10-824">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="2fe10-825">Když se aplikace spustí, výstup okna konzoly indikuje dynamický port, na který se aplikace dá získat:</span><span class="sxs-lookup"><span data-stu-id="2fe10-825">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="2fe10-826">Omezení</span><span class="sxs-lookup"><span data-stu-id="2fe10-826">Limitations</span></span>

<span data-ttu-id="2fe10-827">Konfigurace koncových bodů pomocí následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="2fe10-827">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="2fe10-828">`--urls`argument příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="2fe10-828">`--urls` command-line argument</span></span>
* <span data-ttu-id="2fe10-829">`urls`konfigurační klíč hostitele</span><span class="sxs-lookup"><span data-stu-id="2fe10-829">`urls` host configuration key</span></span>
* <span data-ttu-id="2fe10-830">`ASPNETCORE_URLS`Proměnná prostředí</span><span class="sxs-lookup"><span data-stu-id="2fe10-830">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="2fe10-831">Tyto metody jsou užitečné, pokud chcete, aby kód pracoval na jiných serverech než Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2fe10-831">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="2fe10-832">Mějte ale na paměti následující omezení:</span><span class="sxs-lookup"><span data-stu-id="2fe10-832">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="2fe10-833">Protokol HTTPS nelze s těmito přístupy použít, pokud není k dispozici výchozí certifikát v konfiguraci koncového bodu HTTPS (například pomocí `KestrelServerOptions` Konfigurace nebo konfiguračního souboru, jak je uvedeno výše v tomto tématu).</span><span class="sxs-lookup"><span data-stu-id="2fe10-833">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="2fe10-834">Při `Listen` `UseUrls` současném použití obou přístupů a `Listen` koncových bodů se koncové body přepisují `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="2fe10-834">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="2fe10-835">Konfigurace koncového bodu služby IIS</span><span class="sxs-lookup"><span data-stu-id="2fe10-835">IIS endpoint configuration</span></span>

<span data-ttu-id="2fe10-836">Při použití služby IIS jsou vazby adresy URL pro přepsání vazby služby IIS nastaveny buď `Listen` nebo `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="2fe10-836">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="2fe10-837">Další informace najdete v tématu [ASP.NET Core modulu](xref:host-and-deploy/aspnet-core-module) .</span><span class="sxs-lookup"><span data-stu-id="2fe10-837">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

### <a name="listenoptionsprotocols"></a><span data-ttu-id="2fe10-838">ListenOptions. Protocols</span><span class="sxs-lookup"><span data-stu-id="2fe10-838">ListenOptions.Protocols</span></span>

<span data-ttu-id="2fe10-839">`Protocols`Vlastnost určuje protokoly HTTP ( `HttpProtocols` ) povolené pro koncový bod připojení nebo pro server.</span><span class="sxs-lookup"><span data-stu-id="2fe10-839">The `Protocols` property establishes the HTTP protocols (`HttpProtocols`) enabled on a connection endpoint or for the server.</span></span> <span data-ttu-id="2fe10-840">Přiřaďte hodnotu `Protocols` vlastnosti z `HttpProtocols` výčtu.</span><span class="sxs-lookup"><span data-stu-id="2fe10-840">Assign a value to the `Protocols` property from the `HttpProtocols` enum.</span></span>

| <span data-ttu-id="2fe10-841">`HttpProtocols`hodnota výčtu</span><span class="sxs-lookup"><span data-stu-id="2fe10-841">`HttpProtocols` enum value</span></span> | <span data-ttu-id="2fe10-842">Povolený protokol připojení</span><span class="sxs-lookup"><span data-stu-id="2fe10-842">Connection protocol permitted</span></span> |
| ---
<span data-ttu-id="2fe10-843">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-843">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-844">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-844">'Blazor'</span></span>
- <span data-ttu-id="2fe10-845">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-845">'Identity'</span></span>
- <span data-ttu-id="2fe10-846">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-846">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-847">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-847">'Razor'</span></span>
- <span data-ttu-id="2fe10-848">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-848">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fe10-849">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-849">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-850">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-850">'Blazor'</span></span>
- <span data-ttu-id="2fe10-851">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-851">'Identity'</span></span>
- <span data-ttu-id="2fe10-852">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-852">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-853">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-853">'Razor'</span></span>
- <span data-ttu-id="2fe10-854">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-854">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fe10-855">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-855">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-856">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-856">'Blazor'</span></span>
- <span data-ttu-id="2fe10-857">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-857">'Identity'</span></span>
- <span data-ttu-id="2fe10-858">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-858">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-859">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-859">'Razor'</span></span>
- <span data-ttu-id="2fe10-860">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-860">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fe10-861">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-861">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-862">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-862">'Blazor'</span></span>
- <span data-ttu-id="2fe10-863">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-863">'Identity'</span></span>
- <span data-ttu-id="2fe10-864">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-864">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-865">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-865">'Razor'</span></span>
- <span data-ttu-id="2fe10-866">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-866">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fe10-867">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-867">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-868">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-868">'Blazor'</span></span>
- <span data-ttu-id="2fe10-869">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-869">'Identity'</span></span>
- <span data-ttu-id="2fe10-870">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-870">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-871">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-871">'Razor'</span></span>
- <span data-ttu-id="2fe10-872">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-872">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fe10-873">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-873">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-874">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-874">'Blazor'</span></span>
- <span data-ttu-id="2fe10-875">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-875">'Identity'</span></span>
- <span data-ttu-id="2fe10-876">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-876">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-877">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-877">'Razor'</span></span>
- <span data-ttu-id="2fe10-878">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-878">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fe10-879">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-879">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-880">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-880">'Blazor'</span></span>
- <span data-ttu-id="2fe10-881">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-881">'Identity'</span></span>
- <span data-ttu-id="2fe10-882">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-882">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-883">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-883">'Razor'</span></span>
- <span data-ttu-id="2fe10-884">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-884">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fe10-885">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-885">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-886">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-886">'Blazor'</span></span>
- <span data-ttu-id="2fe10-887">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-887">'Identity'</span></span>
- <span data-ttu-id="2fe10-888">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-888">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-889">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-889">'Razor'</span></span>
- <span data-ttu-id="2fe10-890">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-890">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fe10-891">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-891">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-892">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-892">'Blazor'</span></span>
- <span data-ttu-id="2fe10-893">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-893">'Identity'</span></span>
- <span data-ttu-id="2fe10-894">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-894">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-895">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-895">'Razor'</span></span>
- <span data-ttu-id="2fe10-896">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-896">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fe10-897">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-897">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-898">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-898">'Blazor'</span></span>
- <span data-ttu-id="2fe10-899">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-899">'Identity'</span></span>
- <span data-ttu-id="2fe10-900">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-900">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-901">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-901">'Razor'</span></span>
- <span data-ttu-id="2fe10-902">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-902">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fe10-903">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-903">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-904">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-904">'Blazor'</span></span>
- <span data-ttu-id="2fe10-905">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-905">'Identity'</span></span>
- <span data-ttu-id="2fe10-906">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-906">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-907">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-907">'Razor'</span></span>
- <span data-ttu-id="2fe10-908">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-908">'SignalR' uid:</span></span> 

<span data-ttu-id="2fe10-909">------------- | ---Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-909">------------- | --- title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-910">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-910">'Blazor'</span></span>
- <span data-ttu-id="2fe10-911">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-911">'Identity'</span></span>
- <span data-ttu-id="2fe10-912">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-912">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-913">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-913">'Razor'</span></span>
- <span data-ttu-id="2fe10-914">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-914">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fe10-915">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-915">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-916">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-916">'Blazor'</span></span>
- <span data-ttu-id="2fe10-917">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-917">'Identity'</span></span>
- <span data-ttu-id="2fe10-918">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-918">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-919">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-919">'Razor'</span></span>
- <span data-ttu-id="2fe10-920">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-920">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fe10-921">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-921">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-922">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-922">'Blazor'</span></span>
- <span data-ttu-id="2fe10-923">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-923">'Identity'</span></span>
- <span data-ttu-id="2fe10-924">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-924">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-925">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-925">'Razor'</span></span>
- <span data-ttu-id="2fe10-926">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-926">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fe10-927">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-927">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-928">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-928">'Blazor'</span></span>
- <span data-ttu-id="2fe10-929">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-929">'Identity'</span></span>
- <span data-ttu-id="2fe10-930">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-930">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-931">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-931">'Razor'</span></span>
- <span data-ttu-id="2fe10-932">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-932">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fe10-933">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-933">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-934">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-934">'Blazor'</span></span>
- <span data-ttu-id="2fe10-935">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-935">'Identity'</span></span>
- <span data-ttu-id="2fe10-936">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-936">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-937">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-937">'Razor'</span></span>
- <span data-ttu-id="2fe10-938">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-938">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fe10-939">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-939">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-940">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-940">'Blazor'</span></span>
- <span data-ttu-id="2fe10-941">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-941">'Identity'</span></span>
- <span data-ttu-id="2fe10-942">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-942">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-943">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-943">'Razor'</span></span>
- <span data-ttu-id="2fe10-944">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-944">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fe10-945">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-945">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-946">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-946">'Blazor'</span></span>
- <span data-ttu-id="2fe10-947">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-947">'Identity'</span></span>
- <span data-ttu-id="2fe10-948">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-948">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-949">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-949">'Razor'</span></span>
- <span data-ttu-id="2fe10-950">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-950">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fe10-951">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-951">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-952">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-952">'Blazor'</span></span>
- <span data-ttu-id="2fe10-953">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-953">'Identity'</span></span>
- <span data-ttu-id="2fe10-954">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-954">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-955">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-955">'Razor'</span></span>
- <span data-ttu-id="2fe10-956">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-956">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fe10-957">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-957">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-958">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-958">'Blazor'</span></span>
- <span data-ttu-id="2fe10-959">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-959">'Identity'</span></span>
- <span data-ttu-id="2fe10-960">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-960">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-961">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-961">'Razor'</span></span>
- <span data-ttu-id="2fe10-962">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-962">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fe10-963">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-963">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-964">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-964">'Blazor'</span></span>
- <span data-ttu-id="2fe10-965">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-965">'Identity'</span></span>
- <span data-ttu-id="2fe10-966">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-966">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-967">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-967">'Razor'</span></span>
- <span data-ttu-id="2fe10-968">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-968">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fe10-969">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-969">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-970">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-970">'Blazor'</span></span>
- <span data-ttu-id="2fe10-971">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-971">'Identity'</span></span>
- <span data-ttu-id="2fe10-972">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-972">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-973">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-973">'Razor'</span></span>
- <span data-ttu-id="2fe10-974">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-974">'SignalR' uid:</span></span> 

-
<span data-ttu-id="2fe10-975">Název: Autor: Popis: monikerRange: MS. Author: MS. Custom: MS. Date: No-Loc:</span><span class="sxs-lookup"><span data-stu-id="2fe10-975">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="2fe10-976">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-976">'Blazor'</span></span>
- <span data-ttu-id="2fe10-977">'Identity'</span><span class="sxs-lookup"><span data-stu-id="2fe10-977">'Identity'</span></span>
- <span data-ttu-id="2fe10-978">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="2fe10-978">'Let's Encrypt'</span></span>
- <span data-ttu-id="2fe10-979">'Razor'</span><span class="sxs-lookup"><span data-stu-id="2fe10-979">'Razor'</span></span>
- <span data-ttu-id="2fe10-980">SignalRUID:</span><span class="sxs-lookup"><span data-stu-id="2fe10-980">'SignalR' uid:</span></span> 

<span data-ttu-id="2fe10-981">--------------- | | `Http1`                    | Pouze HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="2fe10-981">--------------- | | `Http1`                    | HTTP/1.1 only.</span></span> <span data-ttu-id="2fe10-982">Dá se použít s TLS nebo bez něj.</span><span class="sxs-lookup"><span data-stu-id="2fe10-982">Can be used with or without TLS.</span></span> <span data-ttu-id="2fe10-983">| | `Http2`                    | Pouze HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="2fe10-983">| | `Http2`                    | HTTP/2 only.</span></span> <span data-ttu-id="2fe10-984">Dá se použít bez TLS jenom v případě, že klient podporuje [předchozí režim znalostní báze](https://tools.ietf.org/html/rfc7540#section-3.4).</span><span class="sxs-lookup"><span data-stu-id="2fe10-984">May be used without TLS only if the client supports a [Prior Knowledge mode](https://tools.ietf.org/html/rfc7540#section-3.4).</span></span> <span data-ttu-id="2fe10-985">| | `Http1AndHttp2`            | HTTP/1.1 a HTTP/2.</span><span class="sxs-lookup"><span data-stu-id="2fe10-985">| | `Http1AndHttp2`            | HTTP/1.1 and HTTP/2.</span></span> <span data-ttu-id="2fe10-986">HTTP/2 vyžaduje připojení TLS a [ALPN (Application-Layer Protocol Negotiation)](https://tools.ietf.org/html/rfc7301#section-3) ; v opačném případě se připojení nastaví jako výchozí HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="2fe10-986">HTTP/2 requires a TLS and [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) connection; otherwise, the connection defaults to HTTP/1.1.</span></span> |

<span data-ttu-id="2fe10-987">Výchozí protokol HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="2fe10-987">The default protocol is HTTP/1.1.</span></span>

<span data-ttu-id="2fe10-988">Omezení TLS pro HTTP/2:</span><span class="sxs-lookup"><span data-stu-id="2fe10-988">TLS restrictions for HTTP/2:</span></span>

* <span data-ttu-id="2fe10-989">TLS verze 1,2 nebo novější</span><span class="sxs-lookup"><span data-stu-id="2fe10-989">TLS version 1.2 or later</span></span>
* <span data-ttu-id="2fe10-990">Opětovné vyjednávání je zakázané.</span><span class="sxs-lookup"><span data-stu-id="2fe10-990">Renegotiation disabled</span></span>
* <span data-ttu-id="2fe10-991">Komprese vypnuta</span><span class="sxs-lookup"><span data-stu-id="2fe10-991">Compression disabled</span></span>
* <span data-ttu-id="2fe10-992">Minimální velikosti dočasného výměny klíčů:</span><span class="sxs-lookup"><span data-stu-id="2fe10-992">Minimum ephemeral key exchange sizes:</span></span>
  * <span data-ttu-id="2fe10-993">Eliptická křivka Diffie-Hellman (ECDH) &lbrack; [RFC4492](https://www.ietf.org/rfc/rfc4492.txt) &rbrack; : 224 bity minima</span><span class="sxs-lookup"><span data-stu-id="2fe10-993">Elliptic curve Diffie-Hellman (ECDHE) &lbrack;[RFC4492](https://www.ietf.org/rfc/rfc4492.txt)&rbrack;: 224 bits minimum</span></span>
  * <span data-ttu-id="2fe10-994">Omezené pole Diffie-Hellman (DHE) &lbrack; `TLS12` &rbrack; : minimální počet bitů 2048</span><span class="sxs-lookup"><span data-stu-id="2fe10-994">Finite field Diffie-Hellman (DHE) &lbrack;`TLS12`&rbrack;: 2048 bits minimum</span></span>
* <span data-ttu-id="2fe10-995">Šifrovací sada není zakázaná.</span><span class="sxs-lookup"><span data-stu-id="2fe10-995">Cipher suite not blacklisted</span></span>

<span data-ttu-id="2fe10-996">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`&lbrack;`TLS-ECDHE`&rbrack; &lbrack; `FIPS186` &rbrack; ve výchozím nastavení je podporována eliptická křivka P-256.</span><span class="sxs-lookup"><span data-stu-id="2fe10-996">`TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256` &lbrack;`TLS-ECDHE`&rbrack; with the P-256 elliptic curve &lbrack;`FIPS186`&rbrack; is supported by default.</span></span>

<span data-ttu-id="2fe10-997">Následující příklad povoluje připojení HTTP/1.1 a HTTP/2 na portu 8000.</span><span class="sxs-lookup"><span data-stu-id="2fe10-997">The following example permits HTTP/1.1 and HTTP/2 connections on port 8000.</span></span> <span data-ttu-id="2fe10-998">Připojení TLS zabezpečuje pomocí zadaného certifikátu:</span><span class="sxs-lookup"><span data-stu-id="2fe10-998">Connections are secured by TLS with a supplied certificate:</span></span>

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

<span data-ttu-id="2fe10-999">Volitelně můžete vytvořit `IConnectionAdapter` implementaci pro filtrování ověřování TLS metodou handshake na základě jednotlivých připojení pro konkrétní šifry:</span><span class="sxs-lookup"><span data-stu-id="2fe10-999">Optionally create an `IConnectionAdapter` implementation to filter TLS handshakes on a per-connection basis for specific ciphers:</span></span>

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

<span data-ttu-id="2fe10-1000">*Nastavit protokol z konfigurace*</span><span class="sxs-lookup"><span data-stu-id="2fe10-1000">*Set the protocol from configuration*</span></span>

<span data-ttu-id="2fe10-1001"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>Při `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` načítání konfigurace Kestrel ve výchozím nastavení volá.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1001"><xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> calls `serverOptions.Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span>

<span data-ttu-id="2fe10-1002">V následujícím příkladu *appSettings. JSON* se pro všechny koncové body Kestrel vytvoří výchozí protokol připojení (http/1.1 a HTTP/2):</span><span class="sxs-lookup"><span data-stu-id="2fe10-1002">In the following *appsettings.json* example, a default connection protocol (HTTP/1.1 and HTTP/2) is established for all of Kestrel's endpoints:</span></span>

```json
{
  "Kestrel": {
    "EndpointDefaults": {
      "Protocols": "Http1AndHttp2"
    }
  }
}
```

<span data-ttu-id="2fe10-1003">Následující příklad konfiguračního souboru vytvoří protokol připojení pro konkrétní koncový bod:</span><span class="sxs-lookup"><span data-stu-id="2fe10-1003">The following configuration file example establishes a connection protocol for a specific endpoint:</span></span>

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

<span data-ttu-id="2fe10-1004">Protokoly zadané v hodnotách přepisu kódu nastavených podle konfigurace</span><span class="sxs-lookup"><span data-stu-id="2fe10-1004">Protocols specified in code override values set by configuration.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="2fe10-1005">Konfigurace přenosu</span><span class="sxs-lookup"><span data-stu-id="2fe10-1005">Transport configuration</span></span>

<span data-ttu-id="2fe10-1006">S vydáním ASP.NET Core 2,1 není výchozí přenos Kestrel založen na Libuv, ale na základě spravovaných soketů.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1006">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="2fe10-1007">Jedná se o zásadní změnu pro aplikace ASP.NET Core 2,0 upgradované na 2,1, které volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> a závisí na jednom z následujících balíčků:</span><span class="sxs-lookup"><span data-stu-id="2fe10-1007">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="2fe10-1008">[Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (přímý odkaz na balíček)</span><span class="sxs-lookup"><span data-stu-id="2fe10-1008">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="2fe10-1009">Microsoft. AspNetCore. app</span><span class="sxs-lookup"><span data-stu-id="2fe10-1009">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="2fe10-1010">Pro projekty, které vyžadují použití Libuv:</span><span class="sxs-lookup"><span data-stu-id="2fe10-1010">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="2fe10-1011">Přidejte do souboru projektu aplikace závislost pro balíček [Microsoft. AspNetCore. Server. Kestrel. Transport. Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) :</span><span class="sxs-lookup"><span data-stu-id="2fe10-1011">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="2fe10-1012">Volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> :</span><span class="sxs-lookup"><span data-stu-id="2fe10-1012">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="2fe10-1013">Předpony adresy URL</span><span class="sxs-lookup"><span data-stu-id="2fe10-1013">URL prefixes</span></span>

<span data-ttu-id="2fe10-1014">Při použití `UseUrls` , `--urls` argumentu příkazového řádku, `urls` konfiguračního klíče hostitele nebo `ASPNETCORE_URLS` proměnné prostředí můžou být předpony adresy URL v některém z následujících formátů.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1014">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="2fe10-1015">Platné jsou pouze předpony adresy URL protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1015">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="2fe10-1016">Kestrel nepodporuje protokol HTTPS při konfiguraci vazeb URL pomocí `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="2fe10-1016">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="2fe10-1017">Adresa IPv4 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="2fe10-1017">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="2fe10-1018">`0.0.0.0`je zvláštní případ, který se váže ke všem adresám IPv4.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1018">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="2fe10-1019">Adresa IPv6 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="2fe10-1019">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="2fe10-1020">`[::]`je ekvivalentem protokolu IPv4 pro protokol IPv6 `0.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="2fe10-1020">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="2fe10-1021">Název hostitele s číslem portu</span><span class="sxs-lookup"><span data-stu-id="2fe10-1021">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="2fe10-1022">Názvy hostitelů, `*` a `+` nejsou speciální.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1022">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="2fe10-1023">Cokoli se nerozpoznalo jako platná IP adresa nebo `localhost` se váže ke všem IP adresám IPv4 a IPv6.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1023">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="2fe10-1024">Pokud chcete navazovat různé názvy hostitelů na různé ASP.NET Core aplikace na stejném portu, použijte [http. sys](xref:fundamentals/servers/httpsys) nebo reverzní proxy server, třeba IIS, Nginx nebo Apache.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1024">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="2fe10-1025">Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="2fe10-1025">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="2fe10-1026">`localhost`Název hostitele s číslem portu nebo IP adresou zpětné smyčky s číslem portu</span><span class="sxs-lookup"><span data-stu-id="2fe10-1026">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="2fe10-1027">Když `localhost` je zadaný, Kestrel se pokusí vytvořit propojení s rozhraními zpětné smyčky IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1027">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="2fe10-1028">Pokud je požadovaný port používán jinou službou na rozhraní zpětné smyčky, spuštění Kestrel se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1028">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="2fe10-1029">Pokud je rozhraní zpětné smyčky v žádném jiném případě nedostupné (většinou kvůli tomu, že protokol IPv6 není podporovaný), Kestrel zaznamená upozornění.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1029">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="2fe10-1030">Filtrování hostitele</span><span class="sxs-lookup"><span data-stu-id="2fe10-1030">Host filtering</span></span>

<span data-ttu-id="2fe10-1031">I když Kestrel podporuje konfiguraci na základě prefixů `http://example.com:5000` , jako je, Kestrel převážně ignoruje název hostitele.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1031">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="2fe10-1032">Hostitel `localhost` je zvláštní případ, který se používá pro vazbu na adresy zpětné smyčky.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1032">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="2fe10-1033">Každý hostitel, který je jiný než explicitní IP adresa, se váže na všechny veřejné IP adresy.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1033">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="2fe10-1034">`Host`hlavičky nejsou ověřené.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1034">`Host` headers aren't validated.</span></span>

<span data-ttu-id="2fe10-1035">Jako alternativní řešení použijte middleware pro filtrování hostitele.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1035">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="2fe10-1036">Middleware pro filtrování hostitele poskytuje balíček [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , který je součástí [Microsoft. AspNetCore. App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 nebo 2,2).</span><span class="sxs-lookup"><span data-stu-id="2fe10-1036">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="2fe10-1037">Middleware je přidána pomocí <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> , který volá <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> :</span><span class="sxs-lookup"><span data-stu-id="2fe10-1037">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="2fe10-1038">Middleware pro filtrování hostitele je ve výchozím nastavení zakázáno.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1038">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="2fe10-1039">Chcete-li povolit middleware, definujte `AllowedHosts` klíč v souboru *appSettings. JSON* / *appSettings. \<EnvironmentName> JSON*.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1039">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="2fe10-1040">Hodnota je seznam názvů hostitelů oddělených středníkem bez čísel portů:</span><span class="sxs-lookup"><span data-stu-id="2fe10-1040">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="2fe10-1041">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="2fe10-1041">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="2fe10-1042">[Middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer) má také <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> možnost.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1042">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="2fe10-1043">Middleware předávaných hlaviček a middleware pro filtrování hostitele mají podobné funkce pro různé scénáře.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1043">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="2fe10-1044">Nastavení `AllowedHosts` pomocí předávaných middlewarových hlaviček je vhodné v případě, že `Host` Hlavička není zachována při předávání požadavků pomocí reverzního proxy server nebo nástroje pro vyrovnávání zatížení.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1044">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="2fe10-1045">Nastavení `AllowedHosts` pomocí middlewaru pro filtrování hostitele je vhodné, když se Kestrel používá jako veřejný hraniční Server nebo když se `Host` Hlavička přímo přepošle.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1045">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="2fe10-1046">Další informace o middlewaru předávaných hlaviček najdete v tématu <xref:host-and-deploy/proxy-load-balancer> .</span><span class="sxs-lookup"><span data-stu-id="2fe10-1046">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.1"

<span data-ttu-id="2fe10-1047">Kestrel je [webový server pro různé platformy pro ASP.NET Core](xref:fundamentals/servers/index).</span><span class="sxs-lookup"><span data-stu-id="2fe10-1047">Kestrel is a cross-platform [web server for ASP.NET Core](xref:fundamentals/servers/index).</span></span> <span data-ttu-id="2fe10-1048">Kestrel je webový server, který je ve výchozím nastavení součástí ASP.NET Core šablon projektů.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1048">Kestrel is the web server that's included by default in ASP.NET Core project templates.</span></span>

<span data-ttu-id="2fe10-1049">Kestrel podporuje následující scénáře:</span><span class="sxs-lookup"><span data-stu-id="2fe10-1049">Kestrel supports the following scenarios:</span></span>

* <span data-ttu-id="2fe10-1050">HTTPS</span><span class="sxs-lookup"><span data-stu-id="2fe10-1050">HTTPS</span></span>
* <span data-ttu-id="2fe10-1051">Neprůhledný upgrade používaný k povolení [WebSockets](https://github.com/aspnet/websockets)</span><span class="sxs-lookup"><span data-stu-id="2fe10-1051">Opaque upgrade used to enable [WebSockets](https://github.com/aspnet/websockets)</span></span>
* <span data-ttu-id="2fe10-1052">Technologie UNIX Sockets s vysokým výkonem za Nginx</span><span class="sxs-lookup"><span data-stu-id="2fe10-1052">Unix sockets for high performance behind Nginx</span></span>

<span data-ttu-id="2fe10-1053">Kestrel se podporuje na všech platformách a verzích, které podporuje .NET Core.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1053">Kestrel is supported on all platforms and versions that .NET Core supports.</span></span>

<span data-ttu-id="2fe10-1054">[Zobrazit nebo stáhnout ukázkový kód](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([Jak stáhnout](xref:index#how-to-download-a-sample))</span><span class="sxs-lookup"><span data-stu-id="2fe10-1054">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/servers/kestrel/samples) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="when-to-use-kestrel-with-a-reverse-proxy"></a><span data-ttu-id="2fe10-1055">Kdy použít Kestrel s reverzním proxy serverem</span><span class="sxs-lookup"><span data-stu-id="2fe10-1055">When to use Kestrel with a reverse proxy</span></span>

<span data-ttu-id="2fe10-1056">Kestrel může používat sám sebe nebo s *reverzními proxy server*, jako je [Internetová informační služba (IIS)](https://www.iis.net/), [Nginx](https://nginx.org)nebo [Apache](https://httpd.apache.org/).</span><span class="sxs-lookup"><span data-stu-id="2fe10-1056">Kestrel can be used by itself or with a *reverse proxy server*, such as [Internet Information Services (IIS)](https://www.iis.net/), [Nginx](https://nginx.org), or [Apache](https://httpd.apache.org/).</span></span> <span data-ttu-id="2fe10-1057">Reverzní proxy server přijímá požadavky HTTP ze sítě a přesměruje je na Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1057">A reverse proxy server receives HTTP requests from the network and forwards them to Kestrel.</span></span>

<span data-ttu-id="2fe10-1058">Kestrel použitý jako hraniční webový server (internetový):</span><span class="sxs-lookup"><span data-stu-id="2fe10-1058">Kestrel used as an edge (Internet-facing) web server:</span></span>

![Kestrel komunikuje přímo s internetem bez zpětného proxy server](kestrel/_static/kestrel-to-internet2.png)

<span data-ttu-id="2fe10-1060">Kestrel se používá v konfiguraci reverzního proxy serveru:</span><span class="sxs-lookup"><span data-stu-id="2fe10-1060">Kestrel used in a reverse proxy configuration:</span></span>

![Kestrel nepřímo komunikuje s internetem prostřednictvím reverzní proxy server, jako je IIS, Nginx nebo Apache.](kestrel/_static/kestrel-to-internet.png)

<span data-ttu-id="2fe10-1062">Buď konfigurace, s zpětným proxy server nebo bez něj, je podporovaná konfigurace hostování.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1062">Either configuration, with or without a reverse proxy server, is a supported hosting configuration.</span></span>

<span data-ttu-id="2fe10-1063">Kestrel používané jako hraniční Server bez reverzních proxy server nepodporuje sdílení stejné IP adresy a portu mezi více procesy.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1063">Kestrel used as an edge server without a reverse proxy server doesn't support sharing the same IP and port among multiple processes.</span></span> <span data-ttu-id="2fe10-1064">Když je Kestrel nakonfigurovaný k naslouchání na portu, Kestrel zpracovává veškerý provoz tohoto portu bez ohledu na `Host` hlavičky požadavků.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1064">When Kestrel is configured to listen on a port, Kestrel handles all of the traffic for that port regardless of requests' `Host` headers.</span></span> <span data-ttu-id="2fe10-1065">Reverzní proxy server, který může sdílet porty, má schopnost přesměrovat požadavky na Kestrel na jedinečné IP adresy a porty.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1065">A reverse proxy that can share ports has the ability to forward requests to Kestrel on a unique IP and port.</span></span>

<span data-ttu-id="2fe10-1066">I v případě, že se zpětný proxy server nepožaduje, může být použití reverzní proxy server vhodnou volbou.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1066">Even if a reverse proxy server isn't required, using a reverse proxy server might be a good choice.</span></span>

<span data-ttu-id="2fe10-1067">Reverzní proxy:</span><span class="sxs-lookup"><span data-stu-id="2fe10-1067">A reverse proxy:</span></span>

* <span data-ttu-id="2fe10-1068">Může omezit vystavenou veřejnou plochu v aplikacích, které hostuje.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1068">Can limit the exposed public surface area of the apps that it hosts.</span></span>
* <span data-ttu-id="2fe10-1069">Poskytněte další vrstvu konfigurace a obrany.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1069">Provide an additional layer of configuration and defense.</span></span>
* <span data-ttu-id="2fe10-1070">Může se lépe integrovat se stávající infrastrukturou.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1070">Might integrate better with existing infrastructure.</span></span>
* <span data-ttu-id="2fe10-1071">Zjednodušte vyrovnávání zatížení a konfiguraci zabezpečené komunikace (HTTPS).</span><span class="sxs-lookup"><span data-stu-id="2fe10-1071">Simplify load balancing and secure communication (HTTPS) configuration.</span></span> <span data-ttu-id="2fe10-1072">Pouze reverzní proxy server vyžaduje certifikát X. 509 a tento server může komunikovat se servery aplikace v interní síti pomocí prostého protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1072">Only the reverse proxy server requires an X.509 certificate, and that server can communicate with the app's servers on the internal network using plain HTTP.</span></span>

> [!WARNING]
> <span data-ttu-id="2fe10-1073">Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="2fe10-1073">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

## <a name="how-to-use-kestrel-in-aspnet-core-apps"></a><span data-ttu-id="2fe10-1074">Použití Kestrel v aplikacích ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="2fe10-1074">How to use Kestrel in ASP.NET Core apps</span></span>

<span data-ttu-id="2fe10-1075">Balíček [Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) je součástí [Microsoft. AspNetCore. app Metapackage](xref:fundamentals/metapackage-app).</span><span class="sxs-lookup"><span data-stu-id="2fe10-1075">The [Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) package is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="2fe10-1076">ASP.NET Core šablony projektu ve výchozím nastavení používají Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1076">ASP.NET Core project templates use Kestrel by default.</span></span> <span data-ttu-id="2fe10-1077">V *program.cs*kód šablony volá <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> , který volá na pozadí <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> .</span><span class="sxs-lookup"><span data-stu-id="2fe10-1077">In *Program.cs*, the template code calls <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> behind the scenes.</span></span>

<span data-ttu-id="2fe10-1078">Chcete-li po volání poskytnout další konfiguraci `CreateDefaultBuilder` , zavolejte <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*> :</span><span class="sxs-lookup"><span data-stu-id="2fe10-1078">To provide additional configuration after calling `CreateDefaultBuilder`, call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderKestrelExtensions.UseKestrel*>:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            // Set properties and call methods on serverOptions
        });
```

<span data-ttu-id="2fe10-1079">Další informace o `CreateDefaultBuilder` a sestavení hostitele naleznete v části *Nastavení hostitele* v tématu <xref:fundamentals/host/web-host#set-up-a-host> .</span><span class="sxs-lookup"><span data-stu-id="2fe10-1079">For more information on `CreateDefaultBuilder` and building the host, see the *Set up a host* section of <xref:fundamentals/host/web-host#set-up-a-host>.</span></span>

## <a name="kestrel-options"></a><span data-ttu-id="2fe10-1080">Kestrel možnosti</span><span class="sxs-lookup"><span data-stu-id="2fe10-1080">Kestrel options</span></span>

<span data-ttu-id="2fe10-1081">Webový server Kestrel má možnosti konfigurace omezení, které jsou zvláště užitečné v internetových nasazeních.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1081">The Kestrel web server has constraint configuration options that are especially useful in Internet-facing deployments.</span></span>

<span data-ttu-id="2fe10-1082">Nastavte omezení pro <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> vlastnost <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> třídy.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1082">Set constraints on the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Limits> property of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> class.</span></span> <span data-ttu-id="2fe10-1083">`Limits`Vlastnost obsahuje instanci <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> třídy.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1083">The `Limits` property holds an instance of the <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits> class.</span></span>

<span data-ttu-id="2fe10-1084">V následujících příkladech se používá <xref:Microsoft.AspNetCore.Server.Kestrel.Core> obor názvů:</span><span class="sxs-lookup"><span data-stu-id="2fe10-1084">The following examples use the <xref:Microsoft.AspNetCore.Server.Kestrel.Core> namespace:</span></span>

```csharp
using Microsoft.AspNetCore.Server.Kestrel.Core;
```

<span data-ttu-id="2fe10-1085">Možnosti Kestrel, které jsou konfigurovány v kódu jazyka C# v následujících příkladech, lze také nastavit pomocí [poskytovatele konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="2fe10-1085">Kestrel options, which are configured in C# code in the following examples, can also be set using a [configuration provider](xref:fundamentals/configuration/index).</span></span> <span data-ttu-id="2fe10-1086">Poskytovatel konfigurace souboru může například načíst konfiguraci Kestrel z objektu *appSettings. JSON* nebo *appSettings. { Soubor Environment}. JSON* :</span><span class="sxs-lookup"><span data-stu-id="2fe10-1086">For example, the File Configuration Provider can load Kestrel configuration from an *appsettings.json* or *appsettings.{Environment}.json* file:</span></span>

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

<span data-ttu-id="2fe10-1087">Použijte **jeden** z následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="2fe10-1087">Use **one** of the following approaches:</span></span>

* <span data-ttu-id="2fe10-1088">Nakonfigurovat Kestrel v `Startup.ConfigureServices` :</span><span class="sxs-lookup"><span data-stu-id="2fe10-1088">Configure Kestrel in `Startup.ConfigureServices`:</span></span>

  1. <span data-ttu-id="2fe10-1089">Vloží instanci `IConfiguration` do `Startup` třídy.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1089">Inject an instance of `IConfiguration` into the `Startup` class.</span></span> <span data-ttu-id="2fe10-1090">Následující příklad předpokládá, že vložená konfigurace je přiřazena `Configuration` Vlastnosti.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1090">The following example assumes that the injected configuration is assigned to the `Configuration` property.</span></span>
  2. <span data-ttu-id="2fe10-1091">V aplikaci `Startup.ConfigureServices` načtěte `Kestrel` část konfigurace do konfigurace Kestrel:</span><span class="sxs-lookup"><span data-stu-id="2fe10-1091">In `Startup.ConfigureServices`, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

* <span data-ttu-id="2fe10-1092">Nakonfigurovat Kestrel při sestavování hostitele:</span><span class="sxs-lookup"><span data-stu-id="2fe10-1092">Configure Kestrel when building the host:</span></span>

  <span data-ttu-id="2fe10-1093">V *program.cs*načtěte `Kestrel` část konfigurace do konfigurace Kestrel:</span><span class="sxs-lookup"><span data-stu-id="2fe10-1093">In *Program.cs*, load the `Kestrel` section of configuration into Kestrel's configuration:</span></span>

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

<span data-ttu-id="2fe10-1094">Obě předchozí přístupy fungují u libovolného [poskytovatele konfigurace](xref:fundamentals/configuration/index).</span><span class="sxs-lookup"><span data-stu-id="2fe10-1094">Both of the preceding approaches work with any [configuration provider](xref:fundamentals/configuration/index).</span></span>

### <a name="keep-alive-timeout"></a><span data-ttu-id="2fe10-1095">Časový limit Keep-Alive</span><span class="sxs-lookup"><span data-stu-id="2fe10-1095">Keep-alive timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.KeepAliveTimeout>

<span data-ttu-id="2fe10-1096">Získá nebo nastaví [časový limit zachování](https://tools.ietf.org/html/rfc7230#section-6.5).</span><span class="sxs-lookup"><span data-stu-id="2fe10-1096">Gets or sets the [keep-alive timeout](https://tools.ietf.org/html/rfc7230#section-6.5).</span></span> <span data-ttu-id="2fe10-1097">Výchozí hodnota je 2 minuty.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1097">Defaults to 2 minutes.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.KeepAliveTimeout = TimeSpan.FromMinutes(2);
        });
```

### <a name="maximum-client-connections"></a><span data-ttu-id="2fe10-1098">Maximální počet připojení klientů</span><span class="sxs-lookup"><span data-stu-id="2fe10-1098">Maximum client connections</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentConnections>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxConcurrentUpgradedConnections>

<span data-ttu-id="2fe10-1099">Maximální počet souběžných otevřených připojení TCP lze pro celou aplikaci nastavit pomocí následujícího kódu:</span><span class="sxs-lookup"><span data-stu-id="2fe10-1099">The maximum number of concurrent open TCP connections can be set for the entire app with the following code:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentConnections = 100;
        });
```

<span data-ttu-id="2fe10-1100">Existuje zvláštní omezení pro připojení, která byla upgradována z protokolu HTTP nebo HTTPS na jiný protokol (například na žádost WebSockets).</span><span class="sxs-lookup"><span data-stu-id="2fe10-1100">There's a separate limit for connections that have been upgraded from HTTP or HTTPS to another protocol (for example, on a WebSockets request).</span></span> <span data-ttu-id="2fe10-1101">Po upgradu připojení se nepočítá na základě `MaxConcurrentConnections` limitu.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1101">After a connection is upgraded, it isn't counted against the `MaxConcurrentConnections` limit.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxConcurrentUpgradedConnections = 100;
        });
```

<span data-ttu-id="2fe10-1102">Ve výchozím nastavení je maximální počet připojení neomezené (null).</span><span class="sxs-lookup"><span data-stu-id="2fe10-1102">The maximum number of connections is unlimited (null) by default.</span></span>

### <a name="maximum-request-body-size"></a><span data-ttu-id="2fe10-1103">Maximální velikost textu požadavku</span><span class="sxs-lookup"><span data-stu-id="2fe10-1103">Maximum request body size</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MaxRequestBodySize>

<span data-ttu-id="2fe10-1104">Výchozí maximální velikost textu požadavku je 30 000 000 bajtů, což je přibližně 28,6 MB.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1104">The default maximum request body size is 30,000,000 bytes, which is approximately 28.6 MB.</span></span>

<span data-ttu-id="2fe10-1105">Doporučený postup pro přepsání limitu v aplikaci ASP.NET Core MVC je použití <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> atributu pro metodu akce:</span><span class="sxs-lookup"><span data-stu-id="2fe10-1105">The recommended approach to override the limit in an ASP.NET Core MVC app is to use the <xref:Microsoft.AspNetCore.Mvc.RequestSizeLimitAttribute> attribute on an action method:</span></span>

```csharp
[RequestSizeLimit(100000000)]
public IActionResult MyActionMethod()
```

<span data-ttu-id="2fe10-1106">Tady je příklad, který ukazuje, jak nakonfigurovat omezení pro aplikaci na každém požadavku:</span><span class="sxs-lookup"><span data-stu-id="2fe10-1106">Here's an example that shows how to configure the constraint for the app on every request:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.MaxRequestBodySize = 10 * 1024;
        });
```

<span data-ttu-id="2fe10-1107">Přepsat nastavení u konkrétního požadavku v middleware:</span><span class="sxs-lookup"><span data-stu-id="2fe10-1107">Override the setting on a specific request in middleware:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Limits&highlight=3-4)]

<span data-ttu-id="2fe10-1108">Výjimka je vyvolána, pokud aplikace po zahájení aplikace nakonfiguruje limit na žádost.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1108">An exception is thrown if the app configures the limit on a request after the app has started to read the request.</span></span> <span data-ttu-id="2fe10-1109">Existuje `IsReadOnly` vlastnost, která určuje, zda `MaxRequestBodySize` je vlastnost ve stavu jen pro čtení, což znamená, že je příliš pozdě pro konfiguraci limitu.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1109">There's an `IsReadOnly` property that indicates if the `MaxRequestBodySize` property is in read-only state, meaning it's too late to configure the limit.</span></span>

<span data-ttu-id="2fe10-1110">Když je aplikace spouštěna [mimo proces](xref:host-and-deploy/iis/index#out-of-process-hosting-model) za [ASP.NET Core modul](xref:host-and-deploy/aspnet-core-module), je omezení velikosti textu požadavku Kestrel zakázané, protože služba IIS tento limit již nastavuje.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1110">When an app is run [out-of-process](xref:host-and-deploy/iis/index#out-of-process-hosting-model) behind the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module), Kestrel's request body size limit is disabled because IIS already sets the limit.</span></span>

### <a name="minimum-request-body-data-rate"></a><span data-ttu-id="2fe10-1111">Minimální rychlost těla zprávy požadavku</span><span class="sxs-lookup"><span data-stu-id="2fe10-1111">Minimum request body data rate</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinRequestBodyDataRate>
<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.MinResponseDataRate>

<span data-ttu-id="2fe10-1112">Kestrel kontroluje každou sekundu, pokud data přicházejí za zadanou rychlost v bajtech za sekundu.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1112">Kestrel checks every second if data is arriving at the specified rate in bytes/second.</span></span> <span data-ttu-id="2fe10-1113">Pokud frekvence klesne pod minimální hodnotu, vypršel časový limit připojení. Doba odkladu je doba, kterou Kestrel umožňuje klientovi zvýšit rychlost odesílání až na minimum; frekvence není během této doby kontrolována.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1113">If the rate drops below the minimum, the connection is timed out. The grace period is the amount of time that Kestrel gives the client to increase its send rate up to the minimum; the rate isn't checked during that time.</span></span> <span data-ttu-id="2fe10-1114">Doba odkladu pomáhá vyhnout se vyřazení připojení, která při počátečním posílání dat zpomalují rychlost, kvůli pomalému spuštění protokolu TCP.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1114">The grace period helps avoid dropping connections that are initially sending data at a slow rate due to TCP slow-start.</span></span>

<span data-ttu-id="2fe10-1115">Výchozí minimální sazba je 240 bajtů za sekundu s obdobím 5 sekund odkladu.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1115">The default minimum rate is 240 bytes/second with a 5 second grace period.</span></span>

<span data-ttu-id="2fe10-1116">Pro odpověď platí také minimální sazba.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1116">A minimum rate also applies to the response.</span></span> <span data-ttu-id="2fe10-1117">Kód pro nastavení limitu požadavku a limit odezvy je stejný, s výjimkou, `RequestBody` že existují nebo `Response` v názvech vlastností a rozhraní.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1117">The code to set the request limit and the response limit is the same except for having `RequestBody` or `Response` in the property and interface names.</span></span>

<span data-ttu-id="2fe10-1118">Tady je příklad, který ukazuje, jak nakonfigurovat minimální datové tarify v *program.cs*:</span><span class="sxs-lookup"><span data-stu-id="2fe10-1118">Here's an example that shows how to configure the minimum data rates in *Program.cs*:</span></span>

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

### <a name="request-headers-timeout"></a><span data-ttu-id="2fe10-1119">Časový limit hlaviček žádostí</span><span class="sxs-lookup"><span data-stu-id="2fe10-1119">Request headers timeout</span></span>

<xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits.RequestHeadersTimeout>

<span data-ttu-id="2fe10-1120">Získá nebo nastaví maximální dobu, po kterou server stráví příjem hlaviček požadavků.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1120">Gets or sets the maximum amount of time the server spends receiving request headers.</span></span> <span data-ttu-id="2fe10-1121">Výchozí hodnota je 30 sekund.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1121">Defaults to 30 seconds.</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.Limits.RequestHeadersTimeout = TimeSpan.FromMinutes(1);
        });
```

### <a name="synchronous-io"></a><span data-ttu-id="2fe10-1122">Synchronní vstupně-výstupní operace</span><span class="sxs-lookup"><span data-stu-id="2fe10-1122">Synchronous I/O</span></span>

<span data-ttu-id="2fe10-1123"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO>Určuje, jestli je pro požadavek a odpověď povolený synchronní vstup/výstup.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1123"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.AllowSynchronousIO> controls whether synchronous I/O is allowed for the request and response.</span></span> <span data-ttu-id="2fe10-1124">Výchozí hodnota je `true` .</span><span class="sxs-lookup"><span data-stu-id="2fe10-1124">The  default value is `true`.</span></span>

> [!WARNING]
> <span data-ttu-id="2fe10-1125">Velký počet blokujících synchronních vstupně-výstupních operací může vést ke vyčerpání fondu vláken, což aplikaci nereaguje.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1125">A large number of blocking synchronous I/O operations can lead to thread pool starvation, which makes the app unresponsive.</span></span> <span data-ttu-id="2fe10-1126">Povolit pouze `AllowSynchronousIO` při použití knihovny, která nepodporuje asynchronní vstupně-výstupní operace.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1126">Only enable `AllowSynchronousIO` when using a library that doesn't support asynchronous I/O.</span></span>

<span data-ttu-id="2fe10-1127">Následující příklad zakáže synchronní vstupně-výstupní operace:</span><span class="sxs-lookup"><span data-stu-id="2fe10-1127">The following example disables synchronous I/O:</span></span>

```csharp
public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
    WebHost.CreateDefaultBuilder(args)
        .UseStartup<Startup>()
        .UseKestrel(serverOptions =>
        {
            serverOptions.AllowSynchronousIO = false;
        });
```

<span data-ttu-id="2fe10-1128">Informace o dalších možnostech a omezeních Kestrel najdete v tématech:</span><span class="sxs-lookup"><span data-stu-id="2fe10-1128">For information about other Kestrel options and limits, see:</span></span>

* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerLimits>
* <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>

## <a name="endpoint-configuration"></a><span data-ttu-id="2fe10-1129">Konfigurace koncového bodu</span><span class="sxs-lookup"><span data-stu-id="2fe10-1129">Endpoint configuration</span></span>

<span data-ttu-id="2fe10-1130">Ve výchozím nastavení ASP.NET Core váže k:</span><span class="sxs-lookup"><span data-stu-id="2fe10-1130">By default, ASP.NET Core binds to:</span></span>

* `http://localhost:5000`
* <span data-ttu-id="2fe10-1131">`https://localhost:5001`(Pokud je k dispozici místní vývojový certifikát)</span><span class="sxs-lookup"><span data-stu-id="2fe10-1131">`https://localhost:5001` (when a local development certificate is present)</span></span>

<span data-ttu-id="2fe10-1132">Zadejte adresy URL pomocí:</span><span class="sxs-lookup"><span data-stu-id="2fe10-1132">Specify URLs using the:</span></span>

* <span data-ttu-id="2fe10-1133">`ASPNETCORE_URLS`Proměnná prostředí.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1133">`ASPNETCORE_URLS` environment variable.</span></span>
* <span data-ttu-id="2fe10-1134">`--urls`argument příkazového řádku.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1134">`--urls` command-line argument.</span></span>
* <span data-ttu-id="2fe10-1135">`urls`konfigurační klíč hostitele.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1135">`urls` host configuration key.</span></span>
* <span data-ttu-id="2fe10-1136">`UseUrls`Metoda rozšíření</span><span class="sxs-lookup"><span data-stu-id="2fe10-1136">`UseUrls` extension method.</span></span>

<span data-ttu-id="2fe10-1137">Hodnota zadaná pomocí těchto přístupů může být jeden nebo více koncových bodů HTTP a HTTPS (HTTPS, pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="2fe10-1137">The value provided using these approaches can be one or more HTTP and HTTPS endpoints (HTTPS if a default cert is available).</span></span> <span data-ttu-id="2fe10-1138">Nakonfigurujte hodnotu jako seznam oddělený středníkem (například `"Urls": "http://localhost:8000;http://localhost:8001"` ).</span><span class="sxs-lookup"><span data-stu-id="2fe10-1138">Configure the value as a semicolon-separated list (for example, `"Urls": "http://localhost:8000;http://localhost:8001"`).</span></span>

<span data-ttu-id="2fe10-1139">Další informace o těchto přístupůch najdete v tématu [adresy URL serveru](xref:fundamentals/host/web-host#server-urls) a [potlačení konfigurace](xref:fundamentals/host/web-host#override-configuration).</span><span class="sxs-lookup"><span data-stu-id="2fe10-1139">For more information on these approaches, see [Server URLs](xref:fundamentals/host/web-host#server-urls) and [Override configuration](xref:fundamentals/host/web-host#override-configuration).</span></span>

<span data-ttu-id="2fe10-1140">Vytvoří se vývojový certifikát:</span><span class="sxs-lookup"><span data-stu-id="2fe10-1140">A development certificate is created:</span></span>

* <span data-ttu-id="2fe10-1141">Po instalaci [.NET Core SDK](/dotnet/core/sdk) .</span><span class="sxs-lookup"><span data-stu-id="2fe10-1141">When the [.NET Core SDK](/dotnet/core/sdk) is installed.</span></span>
* <span data-ttu-id="2fe10-1142">[Nástroj pro vývoj a certifikáty](xref:aspnetcore-2.1#https) slouží k vytvoření certifikátu.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1142">The [dev-certs tool](xref:aspnetcore-2.1#https) is used to create a certificate.</span></span>

<span data-ttu-id="2fe10-1143">Některé prohlížeče vyžadují udělení explicitního oprávnění pro důvěřování místního vývojového certifikátu.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1143">Some browsers require granting explicit permission to trust the local development certificate.</span></span>

<span data-ttu-id="2fe10-1144">Šablony projektů konfigurují aplikace tak, aby běžely ve výchozím nastavení HTTPS a zahrnovaly [přesměrování https a podporu HSTS](xref:security/enforcing-ssl).</span><span class="sxs-lookup"><span data-stu-id="2fe10-1144">Project templates configure apps to run on HTTPS by default and include [HTTPS redirection and HSTS support](xref:security/enforcing-ssl).</span></span>

<span data-ttu-id="2fe10-1145">Volání <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> nebo <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> metody pro <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> konfiguraci předpon adres URL a portů pro Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1145">Call <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> or <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> methods on <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions> to configure URL prefixes and ports for Kestrel.</span></span>

<span data-ttu-id="2fe10-1146">`UseUrls`, `--urls` argument příkazového řádku, `urls` konfigurační klíč hostitele a `ASPNETCORE_URLS` Proměnná prostředí fungují také, ale mají omezení zaznamenaná později v této části (pro konfiguraci koncového bodu https musí být k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="2fe10-1146">`UseUrls`, the `--urls` command-line argument, `urls` host configuration key, and the `ASPNETCORE_URLS` environment variable also work but have the limitations noted later in this section (a default certificate must be available for HTTPS endpoint configuration).</span></span>

<span data-ttu-id="2fe10-1147">`KestrelServerOptions`rozšířeného</span><span class="sxs-lookup"><span data-stu-id="2fe10-1147">`KestrelServerOptions` configuration:</span></span>

### <a name="configureendpointdefaultsactionlistenoptions"></a><span data-ttu-id="2fe10-1148">ConfigureEndpointDefaults (akce \<ListenOptions> )</span><span class="sxs-lookup"><span data-stu-id="2fe10-1148">ConfigureEndpointDefaults(Action\<ListenOptions>)</span></span>

<span data-ttu-id="2fe10-1149">Určuje konfiguraci `Action` , která se spustí pro každý zadaný koncový bod.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1149">Specifies a configuration `Action` to run for each specified endpoint.</span></span> <span data-ttu-id="2fe10-1150">Volání několikrát `ConfigureEndpointDefaults` nahrazuje předchozí `Action` `Action` zadanou parametr s.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1150">Calling `ConfigureEndpointDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="2fe10-1151">Koncové body vytvořené voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **před** voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> nebudou použity výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1151">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureEndpointDefaults*> won't have the defaults applied.</span></span>

### <a name="configurehttpsdefaultsactionhttpsconnectionadapteroptions"></a><span data-ttu-id="2fe10-1152">ConfigureHttpsDefaults (akce \<HttpsConnectionAdapterOptions> )</span><span class="sxs-lookup"><span data-stu-id="2fe10-1152">ConfigureHttpsDefaults(Action\<HttpsConnectionAdapterOptions>)</span></span>

<span data-ttu-id="2fe10-1153">Určuje konfiguraci `Action` , která se má spustit pro každý koncový bod HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1153">Specifies a configuration `Action` to run for each HTTPS endpoint.</span></span> <span data-ttu-id="2fe10-1154">Volání několikrát `ConfigureHttpsDefaults` nahrazuje předchozí `Action` `Action` zadanou parametr s.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1154">Calling `ConfigureHttpsDefaults` multiple times replaces prior `Action`s with the last `Action` specified.</span></span>

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
> <span data-ttu-id="2fe10-1155">Koncové body vytvořené voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **před** voláním <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> nebudou použity výchozí hodnoty.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1155">Endpoints created by calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> **before** calling <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ConfigureHttpsDefaults*> won't have the defaults applied.</span></span>

### <a name="configureiconfiguration"></a><span data-ttu-id="2fe10-1156">Konfigurace (IConfiguration)</span><span class="sxs-lookup"><span data-stu-id="2fe10-1156">Configure(IConfiguration)</span></span>

<span data-ttu-id="2fe10-1157">Vytvoří zavaděč konfigurace pro nastavení Kestrel, který přebírá <xref:Microsoft.Extensions.Configuration.IConfiguration> jako vstup.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1157">Creates a configuration loader for setting up Kestrel that takes an <xref:Microsoft.Extensions.Configuration.IConfiguration> as input.</span></span> <span data-ttu-id="2fe10-1158">Konfigurace musí být vymezená na konfigurační oddíl pro Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1158">The configuration must be scoped to the configuration section for Kestrel.</span></span>

### <a name="listenoptionsusehttps"></a><span data-ttu-id="2fe10-1159">ListenOptions.UseHttps</span><span class="sxs-lookup"><span data-stu-id="2fe10-1159">ListenOptions.UseHttps</span></span>

<span data-ttu-id="2fe10-1160">Nakonfigurujte Kestrel tak, aby používal protokol HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1160">Configure Kestrel to use HTTPS.</span></span>

<span data-ttu-id="2fe10-1161">`ListenOptions.UseHttps`SND</span><span class="sxs-lookup"><span data-stu-id="2fe10-1161">`ListenOptions.UseHttps` extensions:</span></span>

* <span data-ttu-id="2fe10-1162">`UseHttps`: Nakonfigurujte Kestrel tak, aby používal HTTPS s výchozím certifikátem.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1162">`UseHttps`: Configure Kestrel to use HTTPS with the default certificate.</span></span> <span data-ttu-id="2fe10-1163">Vyvolá výjimku, pokud není nakonfigurován žádný výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1163">Throws an exception if no default certificate is configured.</span></span>
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

<span data-ttu-id="2fe10-1164">`ListenOptions.UseHttps`ukazatelů</span><span class="sxs-lookup"><span data-stu-id="2fe10-1164">`ListenOptions.UseHttps` parameters:</span></span>

* <span data-ttu-id="2fe10-1165">`filename`je cesta a název souboru certifikátu vzhledem k adresáři, který obsahuje soubory obsahu aplikace.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1165">`filename` is the path and file name of a certificate file, relative to the directory that contains the app's content files.</span></span>
* <span data-ttu-id="2fe10-1166">`password`je vyžadováno heslo pro přístup k datům certifikátu X. 509.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1166">`password` is the password required to access the X.509 certificate data.</span></span>
* <span data-ttu-id="2fe10-1167">`configureOptions`je `Action` ke konfiguraci `HttpsConnectionAdapterOptions` .</span><span class="sxs-lookup"><span data-stu-id="2fe10-1167">`configureOptions` is an `Action` to configure the `HttpsConnectionAdapterOptions`.</span></span> <span data-ttu-id="2fe10-1168">Vrátí `ListenOptions` .</span><span class="sxs-lookup"><span data-stu-id="2fe10-1168">Returns the `ListenOptions`.</span></span>
* <span data-ttu-id="2fe10-1169">`storeName`je úložiště certifikátů, ze kterého se má certifikát načíst.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1169">`storeName` is the certificate store from which to load the certificate.</span></span>
* <span data-ttu-id="2fe10-1170">`subject`je název subjektu certifikátu.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1170">`subject` is the subject name for the certificate.</span></span>
* <span data-ttu-id="2fe10-1171">`allowInvalid`Určuje, zda by měly být zváženy neplatné certifikáty, například certifikáty podepsané svým držitelem.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1171">`allowInvalid` indicates if invalid certificates should be considered, such as self-signed certificates.</span></span>
* <span data-ttu-id="2fe10-1172">`location`je umístěním úložiště, ze kterého se má certifikát načíst.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1172">`location` is the store location to load the certificate from.</span></span>
* <span data-ttu-id="2fe10-1173">`serverCertificate`je certifikát X. 509.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1173">`serverCertificate` is the X.509 certificate.</span></span>

<span data-ttu-id="2fe10-1174">V produkčním prostředí musí být HTTPS explicitně nakonfigurovaný.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1174">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="2fe10-1175">Musí být zadán minimálně výchozí certifikát.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1175">At a minimum, a default certificate must be provided.</span></span>

<span data-ttu-id="2fe10-1176">Podporované konfigurace popsané dál:</span><span class="sxs-lookup"><span data-stu-id="2fe10-1176">Supported configurations described next:</span></span>

* <span data-ttu-id="2fe10-1177">Žádná konfigurace</span><span class="sxs-lookup"><span data-stu-id="2fe10-1177">No configuration</span></span>
* <span data-ttu-id="2fe10-1178">Nahradit výchozí certifikát z konfigurace</span><span class="sxs-lookup"><span data-stu-id="2fe10-1178">Replace the default certificate from configuration</span></span>
* <span data-ttu-id="2fe10-1179">Změna výchozích hodnot v kódu</span><span class="sxs-lookup"><span data-stu-id="2fe10-1179">Change the defaults in code</span></span>

<span data-ttu-id="2fe10-1180">*Žádná konfigurace*</span><span class="sxs-lookup"><span data-stu-id="2fe10-1180">*No configuration*</span></span>

<span data-ttu-id="2fe10-1181">Kestrel naslouchá na `http://localhost:5000` a `https://localhost:5001` (Pokud je k dispozici výchozí certifikát).</span><span class="sxs-lookup"><span data-stu-id="2fe10-1181">Kestrel listens on `http://localhost:5000` and `https://localhost:5001` (if a default cert is available).</span></span>

<a name="configuration"></a>

<span data-ttu-id="2fe10-1182">*Nahradit výchozí certifikát z konfigurace*</span><span class="sxs-lookup"><span data-stu-id="2fe10-1182">*Replace the default certificate from configuration*</span></span>

<span data-ttu-id="2fe10-1183">`CreateDefaultBuilder`Při `Configure(context.Configuration.GetSection("Kestrel"))` načítání konfigurace Kestrel ve výchozím nastavení volá.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1183">`CreateDefaultBuilder` calls `Configure(context.Configuration.GetSection("Kestrel"))` by default to load Kestrel configuration.</span></span> <span data-ttu-id="2fe10-1184">Pro Kestrel je k dispozici výchozí schéma konfigurace nastavení aplikace HTTPS.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1184">A default HTTPS app settings configuration schema is available for Kestrel.</span></span> <span data-ttu-id="2fe10-1185">Nakonfigurujte několik koncových bodů, včetně adres URL a certifikátů, které se mají použít, buď ze souboru na disku, nebo z úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1185">Configure multiple endpoints, including the URLs and the certificates to use, either from a file on disk or from a certificate store.</span></span>

<span data-ttu-id="2fe10-1186">V následujícím příkladu *appSettings. JSON* :</span><span class="sxs-lookup"><span data-stu-id="2fe10-1186">In the following *appsettings.json* example:</span></span>

* <span data-ttu-id="2fe10-1187">Nastavte **AllowInvalid** na `true` , aby povolovala použití neplatných certifikátů (například certifikátů podepsaných svým držitelem).</span><span class="sxs-lookup"><span data-stu-id="2fe10-1187">Set **AllowInvalid** to `true` to permit the use of invalid certificates (for example, self-signed certificates).</span></span>
* <span data-ttu-id="2fe10-1188">Libovolný koncový bod HTTPS, který neurčuje certifikát (**HttpsDefaultCert** v následujícím příkladu), se vrátí k certifikátu definovanému v části **certifikáty** > **výchozí** nebo vývojový certifikát.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1188">Any HTTPS endpoint that doesn't specify a certificate (**HttpsDefaultCert** in the example that follows) falls back to the cert defined under **Certificates** > **Default** or the development certificate.</span></span>

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

<span data-ttu-id="2fe10-1189">Alternativou k použití **cesty** a **hesla** pro libovolný uzel certifikátu je zadání certifikátu pomocí polí úložiště certifikátů.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1189">An alternative to using **Path** and **Password** for any certificate node is to specify the certificate using certificate store fields.</span></span> <span data-ttu-id="2fe10-1190">Například **Certificates**  >  **výchozí** certifikát může být zadán jako:</span><span class="sxs-lookup"><span data-stu-id="2fe10-1190">For example, the **Certificates** > **Default** certificate can be specified as:</span></span>

```json
"Default": {
  "Subject": "<subject; required>",
  "Store": "<cert store; required>",
  "Location": "<location; defaults to CurrentUser>",
  "AllowInvalid": "<true or false; defaults to false>"
}
```

<span data-ttu-id="2fe10-1191">Poznámky ke schématu:</span><span class="sxs-lookup"><span data-stu-id="2fe10-1191">Schema notes:</span></span>

* <span data-ttu-id="2fe10-1192">V názvech koncových bodů se nerozlišují malá a velká písmena.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1192">Endpoints names are case-insensitive.</span></span> <span data-ttu-id="2fe10-1193">Například `HTTPS` a `Https` jsou platné.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1193">For example, `HTTPS` and `Https` are valid.</span></span>
* <span data-ttu-id="2fe10-1194">`Url`U každého koncového bodu je vyžadován parametr.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1194">The `Url` parameter is required for each endpoint.</span></span> <span data-ttu-id="2fe10-1195">Formát pro tento parametr je stejný jako `Urls` konfigurační parametr nejvyšší úrovně s tím rozdílem, že je omezen na jedinou hodnotu.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1195">The format for this parameter is the same as the top-level `Urls` configuration parameter except that it's limited to a single value.</span></span>
* <span data-ttu-id="2fe10-1196">Tyto koncové body nahrazují hodnoty definované v konfiguraci nejvyšší úrovně `Urls` místo jejich přidání do těchto koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1196">These endpoints replace those defined in the top-level `Urls` configuration rather than adding to them.</span></span> <span data-ttu-id="2fe10-1197">Koncové body definované v kódu prostřednictvím `Listen` jsou kumulativní pomocí koncových bodů definovaných v konfiguračním oddílu.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1197">Endpoints defined in code via `Listen` are cumulative with the endpoints defined in the configuration section.</span></span>
* <span data-ttu-id="2fe10-1198">`Certificate`Oddíl je nepovinný.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1198">The `Certificate` section is optional.</span></span> <span data-ttu-id="2fe10-1199">Pokud `Certificate` není oddíl zadaný, použijí se výchozí hodnoty definované v dřívějších scénářích.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1199">If the `Certificate` section isn't specified, the defaults defined in earlier scenarios are used.</span></span> <span data-ttu-id="2fe10-1200">Pokud nejsou k dispozici žádné výchozí hodnoty, Server vyvolá výjimku a nemůže se spustit.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1200">If no defaults are available, the server throws an exception and fails to start.</span></span>
* <span data-ttu-id="2fe10-1201">`Certificate`Oddíl podporuje jak heslo **cesty** &ndash; **Password** , tak **Subject** &ndash; certifikáty**úložiště** subjektu.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1201">The `Certificate` section supports both **Path**&ndash;**Password** and **Subject**&ndash;**Store** certificates.</span></span>
* <span data-ttu-id="2fe10-1202">V takovém případě může být definován libovolný počet koncových bodů, pokud to nezpůsobí konflikty portů.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1202">Any number of endpoints may be defined in this way so long as they don't cause port conflicts.</span></span>
* <span data-ttu-id="2fe10-1203">`options.Configure(context.Configuration.GetSection("{SECTION}"))`Vrátí `KestrelConfigurationLoader` `.Endpoint(string name, listenOptions => { })` metodu s metodou, kterou lze použít k doplnění nastavení nakonfigurovaného koncového bodu:</span><span class="sxs-lookup"><span data-stu-id="2fe10-1203">`options.Configure(context.Configuration.GetSection("{SECTION}"))` returns a `KestrelConfigurationLoader` with an `.Endpoint(string name, listenOptions => { })` method that can be used to supplement a configured endpoint's settings:</span></span>

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

<span data-ttu-id="2fe10-1204">`KestrelServerOptions.ConfigurationLoader`k pokračování v iteraci u stávajícího zavaděče se dá získat přímý odkaz, jako je třeba ten, který poskytuje <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> .</span><span class="sxs-lookup"><span data-stu-id="2fe10-1204">`KestrelServerOptions.ConfigurationLoader` can be directly accessed to continue iterating on the existing loader, such as the one provided by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>.</span></span>

* <span data-ttu-id="2fe10-1205">Konfigurační oddíl pro každý koncový bod je k dispozici na možnostech v `Endpoint` metodě, aby bylo možné číst vlastní nastavení.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1205">The configuration section for each endpoint is available on the options in the `Endpoint` method so that custom settings may be read.</span></span>
* <span data-ttu-id="2fe10-1206">Více konfigurací může být načteno voláním `options.Configure(context.Configuration.GetSection("{SECTION}"))` znovu s jiným oddílem.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1206">Multiple configurations may be loaded by calling `options.Configure(context.Configuration.GetSection("{SECTION}"))` again with another section.</span></span> <span data-ttu-id="2fe10-1207">Je použita pouze poslední konfigurace, pokud `Load` není explicitně volána při předchozích instancích.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1207">Only the last configuration is used, unless `Load` is explicitly called on prior instances.</span></span> <span data-ttu-id="2fe10-1208">Metapackage nevolá `Load` , aby se jeho výchozí konfigurační oddíl mohl nahradit.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1208">The metapackage doesn't call `Load` so that its default configuration section may be replaced.</span></span>
* <span data-ttu-id="2fe10-1209">`KestrelConfigurationLoader`zrcadlí `Listen` rodinu rozhraní API z `KestrelServerOptions` as `Endpoint` přetížení, takže koncové body kódu a konfigurace lze konfigurovat na stejném místě.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1209">`KestrelConfigurationLoader` mirrors the `Listen` family of APIs from `KestrelServerOptions` as `Endpoint` overloads, so code and config endpoints may be configured in the same place.</span></span> <span data-ttu-id="2fe10-1210">Tato přetížení nepoužívají názvy a využívají pouze výchozí nastavení z konfigurace.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1210">These overloads don't use names and only consume default settings from configuration.</span></span>

<span data-ttu-id="2fe10-1211">*Změna výchozích hodnot v kódu*</span><span class="sxs-lookup"><span data-stu-id="2fe10-1211">*Change the defaults in code*</span></span>

<span data-ttu-id="2fe10-1212">`ConfigureEndpointDefaults`a `ConfigureHttpsDefaults` dá se použít ke změně výchozího nastavení pro `ListenOptions` a `HttpsConnectionAdapterOptions` , včetně přepsání výchozího certifikátu zadaného v předchozím scénáři.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1212">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` can be used to change default settings for `ListenOptions` and `HttpsConnectionAdapterOptions`, including overriding the default certificate specified in the prior scenario.</span></span> <span data-ttu-id="2fe10-1213">`ConfigureEndpointDefaults`a `ConfigureHttpsDefaults` měla by být volána před konfigurací koncových bodů.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1213">`ConfigureEndpointDefaults` and `ConfigureHttpsDefaults` should be called before any endpoints are configured.</span></span>

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

<span data-ttu-id="2fe10-1214">*Podpora Kestrel pro SNI*</span><span class="sxs-lookup"><span data-stu-id="2fe10-1214">*Kestrel support for SNI*</span></span>

<span data-ttu-id="2fe10-1215">[Indikace názvu serveru (SNI)](https://tools.ietf.org/html/rfc6066#section-3) lze použít k hostování více domén na stejné IP adrese a portu.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1215">[Server Name Indication (SNI)](https://tools.ietf.org/html/rfc6066#section-3) can be used to host multiple domains on the same IP address and port.</span></span> <span data-ttu-id="2fe10-1216">Aby SNI fungovalo, klient pošle název hostitele pro zabezpečenou relaci serveru během metody handshake TLS, aby server mohl poskytovat správný certifikát.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1216">For SNI to function, the client sends the host name for the secure session to the server during the TLS handshake so that the server can provide the correct certificate.</span></span> <span data-ttu-id="2fe10-1217">Klient používá poskytnutý certifikát pro šifrovanou komunikaci se serverem během zabezpečené relace, která následuje za metodou handshake TLS.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1217">The client uses the furnished certificate for encrypted communication with the server during the secure session that follows the TLS handshake.</span></span>

<span data-ttu-id="2fe10-1218">Kestrel podporuje SNI prostřednictvím `ServerCertificateSelector` zpětného volání.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1218">Kestrel supports SNI via the `ServerCertificateSelector` callback.</span></span> <span data-ttu-id="2fe10-1219">Zpětné volání je vyvoláno jednou za připojení, které aplikaci umožní zkontrolovat název hostitele a vybrat příslušný certifikát.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1219">The callback is invoked once per connection to allow the app to inspect the host name and select the appropriate certificate.</span></span>

<span data-ttu-id="2fe10-1220">Podpora SNI vyžaduje:</span><span class="sxs-lookup"><span data-stu-id="2fe10-1220">SNI support requires:</span></span>

* <span data-ttu-id="2fe10-1221">Běží v cílovém rozhraní `netcoreapp2.1` nebo novějším.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1221">Running on target framework `netcoreapp2.1` or later.</span></span> <span data-ttu-id="2fe10-1222">V systému `net461` nebo novějším je zpětné volání vyvoláno, ale `name` je vždy `null` .</span><span class="sxs-lookup"><span data-stu-id="2fe10-1222">On `net461` or later, the callback is invoked but the `name` is always `null`.</span></span> <span data-ttu-id="2fe10-1223">`name`Je také v `null` případě, že klient v TLS handshake neposkytne parametr názvu hostitele.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1223">The `name` is also `null` if the client doesn't provide the host name parameter in the TLS handshake.</span></span>
* <span data-ttu-id="2fe10-1224">Všechny weby běží na stejné instanci Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1224">All websites run on the same Kestrel instance.</span></span> <span data-ttu-id="2fe10-1225">Kestrel nepodporuje sdílení IP adresy a portu napříč několika instancemi bez reverzního proxy serveru.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1225">Kestrel doesn't support sharing an IP address and port across multiple instances without a reverse proxy.</span></span>

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

### <a name="connection-logging"></a><span data-ttu-id="2fe10-1226">Protokolování připojení</span><span class="sxs-lookup"><span data-stu-id="2fe10-1226">Connection logging</span></span>

<span data-ttu-id="2fe10-1227">Volá <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> se, aby se vygenerovaly protokoly na úrovni ladění pro komunikaci na úrovni bajtového připojení.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1227">Call <xref:Microsoft.AspNetCore.Hosting.ListenOptionsConnectionLoggingExtensions.UseConnectionLogging*> to emit Debug level logs for byte-level communication on a connection.</span></span> <span data-ttu-id="2fe10-1228">Protokolování připojení je užitečné při řešení problémů v komunikaci nízké úrovně, například během šifrování TLS a za proxy servery.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1228">Connection logging is helpful for troubleshooting problems in low-level communication, such as during TLS encryption and behind proxies.</span></span> <span data-ttu-id="2fe10-1229">Pokud `UseConnectionLogging` je umístěn před `UseHttps` , zaprotokoluje se zašifrovaný provoz.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1229">If `UseConnectionLogging` is placed before `UseHttps`, encrypted traffic is logged.</span></span> <span data-ttu-id="2fe10-1230">Pokud `UseConnectionLogging` je umístěn po `UseHttps` , zaprotokoluje se dešifrovaný provoz.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1230">If `UseConnectionLogging` is placed after `UseHttps`, decrypted traffic is logged.</span></span>

```csharp
webBuilder.ConfigureKestrel(serverOptions =>
{
    serverOptions.Listen(IPAddress.Any, 8000, listenOptions =>
    {
        listenOptions.UseConnectionLogging();
    });
});
```

### <a name="bind-to-a-tcp-socket"></a><span data-ttu-id="2fe10-1231">Vytvoření vazby na soket TCP</span><span class="sxs-lookup"><span data-stu-id="2fe10-1231">Bind to a TCP socket</span></span>

<span data-ttu-id="2fe10-1232"><xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*>Metoda se váže k soketu TCP a pomocí lambda možností povolíte konfiguraci certifikátu X. 509:</span><span class="sxs-lookup"><span data-stu-id="2fe10-1232">The <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.Listen*> method binds to a TCP socket, and an options lambda permits X.509 certificate configuration:</span></span>

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

<span data-ttu-id="2fe10-1233">Příklad nakonfiguruje HTTPS pro koncový bod pomocí <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions> .</span><span class="sxs-lookup"><span data-stu-id="2fe10-1233">The example configures HTTPS for an endpoint with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.ListenOptions>.</span></span> <span data-ttu-id="2fe10-1234">Ke konfiguraci dalších nastavení Kestrel pro konkrétní koncové body použijte stejné rozhraní API.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1234">Use the same API to configure other Kestrel settings for specific endpoints.</span></span>

[!INCLUDE [How to make an X.509 cert](~/includes/make-x509-cert.md)]

### <a name="bind-to-a-unix-socket"></a><span data-ttu-id="2fe10-1235">Vytvoření vazby na soket UNIX</span><span class="sxs-lookup"><span data-stu-id="2fe10-1235">Bind to a Unix socket</span></span>

<span data-ttu-id="2fe10-1236">Naslouchat na soketu se systémem UNIX <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> , aby se zlepšil výkon pomocí Nginx, jak je znázorněno v tomto příkladu:</span><span class="sxs-lookup"><span data-stu-id="2fe10-1236">Listen on a Unix socket with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> for improved performance with Nginx, as shown in this example:</span></span>

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

* <span data-ttu-id="2fe10-1237">V souboru Nginx confiuguration nastavte `server`  >  `location`  >  `proxy_pass` položku na `http://unix:/tmp/{KESTREL SOCKET}:/;` .</span><span class="sxs-lookup"><span data-stu-id="2fe10-1237">In the Nginx confiuguration file, set the `server` > `location` > `proxy_pass` entry to `http://unix:/tmp/{KESTREL SOCKET}:/;`.</span></span> <span data-ttu-id="2fe10-1238">`{KESTREL SOCKET}`je název soketu poskytnutý <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (například `kestrel-test.sock` v předchozím příkladu).</span><span class="sxs-lookup"><span data-stu-id="2fe10-1238">`{KESTREL SOCKET}` is the name of the socket provided to <xref:Microsoft.AspNetCore.Server.Kestrel.Core.KestrelServerOptions.ListenUnixSocket*> (for example, `kestrel-test.sock` in the preceding example).</span></span>
* <span data-ttu-id="2fe10-1239">Zajistěte, aby byl soket zapisovatelný pomocí Nginx (například `chmod go+w /tmp/kestrel-test.sock` ).</span><span class="sxs-lookup"><span data-stu-id="2fe10-1239">Ensure that the socket is writeable by Nginx (for example, `chmod go+w /tmp/kestrel-test.sock`).</span></span> 

### <a name="port-0"></a><span data-ttu-id="2fe10-1240">Port 0</span><span class="sxs-lookup"><span data-stu-id="2fe10-1240">Port 0</span></span>

<span data-ttu-id="2fe10-1241">Po zadání čísla portu se `0` Kestrel dynamicky váže k dostupnému portu.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1241">When the port number `0` is specified, Kestrel dynamically binds to an available port.</span></span> <span data-ttu-id="2fe10-1242">Následující příklad ukazuje, jak určit, který port Kestrel je skutečně vázaný za běhu:</span><span class="sxs-lookup"><span data-stu-id="2fe10-1242">The following example shows how to determine which port Kestrel actually bound at runtime:</span></span>

[!code-csharp[](kestrel/samples/2.x/KestrelSample/Startup.cs?name=snippet_Configure&highlight=3-4,15-21)]

<span data-ttu-id="2fe10-1243">Když se aplikace spustí, výstup okna konzoly indikuje dynamický port, na který se aplikace dá získat:</span><span class="sxs-lookup"><span data-stu-id="2fe10-1243">When the app is run, the console window output indicates the dynamic port where the app can be reached:</span></span>

```console
Listening on the following addresses: http://127.0.0.1:48508
```

### <a name="limitations"></a><span data-ttu-id="2fe10-1244">Omezení</span><span class="sxs-lookup"><span data-stu-id="2fe10-1244">Limitations</span></span>

<span data-ttu-id="2fe10-1245">Konfigurace koncových bodů pomocí následujících přístupů:</span><span class="sxs-lookup"><span data-stu-id="2fe10-1245">Configure endpoints with the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Hosting.HostingAbstractionsWebHostBuilderExtensions.UseUrls*>
* <span data-ttu-id="2fe10-1246">`--urls`argument příkazového řádku</span><span class="sxs-lookup"><span data-stu-id="2fe10-1246">`--urls` command-line argument</span></span>
* <span data-ttu-id="2fe10-1247">`urls`konfigurační klíč hostitele</span><span class="sxs-lookup"><span data-stu-id="2fe10-1247">`urls` host configuration key</span></span>
* <span data-ttu-id="2fe10-1248">`ASPNETCORE_URLS`Proměnná prostředí</span><span class="sxs-lookup"><span data-stu-id="2fe10-1248">`ASPNETCORE_URLS` environment variable</span></span>

<span data-ttu-id="2fe10-1249">Tyto metody jsou užitečné, pokud chcete, aby kód pracoval na jiných serverech než Kestrel.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1249">These methods are useful for making code work with servers other than Kestrel.</span></span> <span data-ttu-id="2fe10-1250">Mějte ale na paměti následující omezení:</span><span class="sxs-lookup"><span data-stu-id="2fe10-1250">However, be aware of the following limitations:</span></span>

* <span data-ttu-id="2fe10-1251">Protokol HTTPS nelze s těmito přístupy použít, pokud není k dispozici výchozí certifikát v konfiguraci koncového bodu HTTPS (například pomocí `KestrelServerOptions` Konfigurace nebo konfiguračního souboru, jak je uvedeno výše v tomto tématu).</span><span class="sxs-lookup"><span data-stu-id="2fe10-1251">HTTPS can't be used with these approaches unless a default certificate is provided in the HTTPS endpoint configuration (for example, using `KestrelServerOptions` configuration or a configuration file as shown earlier in this topic).</span></span>
* <span data-ttu-id="2fe10-1252">Při `Listen` `UseUrls` současném použití obou přístupů a `Listen` koncových bodů se koncové body přepisují `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="2fe10-1252">When both the `Listen` and `UseUrls` approaches are used simultaneously, the `Listen` endpoints override the `UseUrls` endpoints.</span></span>

### <a name="iis-endpoint-configuration"></a><span data-ttu-id="2fe10-1253">Konfigurace koncového bodu služby IIS</span><span class="sxs-lookup"><span data-stu-id="2fe10-1253">IIS endpoint configuration</span></span>

<span data-ttu-id="2fe10-1254">Při použití služby IIS jsou vazby adresy URL pro přepsání vazby služby IIS nastaveny buď `Listen` nebo `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="2fe10-1254">When using IIS, the URL bindings for IIS override bindings are set by either `Listen` or `UseUrls`.</span></span> <span data-ttu-id="2fe10-1255">Další informace najdete v tématu [ASP.NET Core modulu](xref:host-and-deploy/aspnet-core-module) .</span><span class="sxs-lookup"><span data-stu-id="2fe10-1255">For more information, see the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) topic.</span></span>

## <a name="transport-configuration"></a><span data-ttu-id="2fe10-1256">Konfigurace přenosu</span><span class="sxs-lookup"><span data-stu-id="2fe10-1256">Transport configuration</span></span>

<span data-ttu-id="2fe10-1257">S vydáním ASP.NET Core 2,1 není výchozí přenos Kestrel založen na Libuv, ale na základě spravovaných soketů.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1257">With the release of ASP.NET Core 2.1, Kestrel's default transport is no longer based on Libuv but instead based on managed sockets.</span></span> <span data-ttu-id="2fe10-1258">Jedná se o zásadní změnu pro aplikace ASP.NET Core 2,0 upgradované na 2,1, které volá <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> a závisí na jednom z následujících balíčků:</span><span class="sxs-lookup"><span data-stu-id="2fe10-1258">This is a breaking change for ASP.NET Core 2.0 apps upgrading to 2.1 that call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> and depend on either of the following packages:</span></span>

* <span data-ttu-id="2fe10-1259">[Microsoft. AspNetCore. Server. Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (přímý odkaz na balíček)</span><span class="sxs-lookup"><span data-stu-id="2fe10-1259">[Microsoft.AspNetCore.Server.Kestrel](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel/) (direct package reference)</span></span>
* [<span data-ttu-id="2fe10-1260">Microsoft. AspNetCore. app</span><span class="sxs-lookup"><span data-stu-id="2fe10-1260">Microsoft.AspNetCore.App</span></span>](https://www.nuget.org/packages/Microsoft.AspNetCore.App/)

<span data-ttu-id="2fe10-1261">Pro projekty, které vyžadují použití Libuv:</span><span class="sxs-lookup"><span data-stu-id="2fe10-1261">For projects that require the use of Libuv:</span></span>

* <span data-ttu-id="2fe10-1262">Přidejte do souboru projektu aplikace závislost pro balíček [Microsoft. AspNetCore. Server. Kestrel. Transport. Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) :</span><span class="sxs-lookup"><span data-stu-id="2fe10-1262">Add a dependency for the [Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv](https://www.nuget.org/packages/Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv/) package to the app's project file:</span></span>

  ```xml
  <PackageReference Include="Microsoft.AspNetCore.Server.Kestrel.Transport.Libuv"
                    Version="{VERSION}" />
  ```

* <span data-ttu-id="2fe10-1263">Volání <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*> :</span><span class="sxs-lookup"><span data-stu-id="2fe10-1263">Call <xref:Microsoft.AspNetCore.Hosting.WebHostBuilderLibuvExtensions.UseLibuv*>:</span></span>

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

### <a name="url-prefixes"></a><span data-ttu-id="2fe10-1264">Předpony adresy URL</span><span class="sxs-lookup"><span data-stu-id="2fe10-1264">URL prefixes</span></span>

<span data-ttu-id="2fe10-1265">Při použití `UseUrls` , `--urls` argumentu příkazového řádku, `urls` konfiguračního klíče hostitele nebo `ASPNETCORE_URLS` proměnné prostředí můžou být předpony adresy URL v některém z následujících formátů.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1265">When using `UseUrls`, `--urls` command-line argument, `urls` host configuration key, or `ASPNETCORE_URLS` environment variable, the URL prefixes can be in any of the following formats.</span></span>

<span data-ttu-id="2fe10-1266">Platné jsou pouze předpony adresy URL protokolu HTTP.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1266">Only HTTP URL prefixes are valid.</span></span> <span data-ttu-id="2fe10-1267">Kestrel nepodporuje protokol HTTPS při konfiguraci vazeb URL pomocí `UseUrls` .</span><span class="sxs-lookup"><span data-stu-id="2fe10-1267">Kestrel doesn't support HTTPS when configuring URL bindings using `UseUrls`.</span></span>

* <span data-ttu-id="2fe10-1268">Adresa IPv4 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="2fe10-1268">IPv4 address with port number</span></span>

  ```
  http://65.55.39.10:80/
  ```

  <span data-ttu-id="2fe10-1269">`0.0.0.0`je zvláštní případ, který se váže ke všem adresám IPv4.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1269">`0.0.0.0` is a special case that binds to all IPv4 addresses.</span></span>

* <span data-ttu-id="2fe10-1270">Adresa IPv6 s číslem portu</span><span class="sxs-lookup"><span data-stu-id="2fe10-1270">IPv6 address with port number</span></span>

  ```
  http://[0:0:0:0:0:ffff:4137:270a]:80/
  ```

  <span data-ttu-id="2fe10-1271">`[::]`je ekvivalentem protokolu IPv4 pro protokol IPv6 `0.0.0.0` .</span><span class="sxs-lookup"><span data-stu-id="2fe10-1271">`[::]` is the IPv6 equivalent of IPv4 `0.0.0.0`.</span></span>

* <span data-ttu-id="2fe10-1272">Název hostitele s číslem portu</span><span class="sxs-lookup"><span data-stu-id="2fe10-1272">Host name with port number</span></span>

  ```
  http://contoso.com:80/
  http://*:80/
  ```

  <span data-ttu-id="2fe10-1273">Názvy hostitelů, `*` a `+` nejsou speciální.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1273">Host names, `*`, and `+`, aren't special.</span></span> <span data-ttu-id="2fe10-1274">Cokoli se nerozpoznalo jako platná IP adresa nebo `localhost` se váže ke všem IP adresám IPv4 a IPv6.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1274">Anything not recognized as a valid IP address or `localhost` binds to all IPv4 and IPv6 IPs.</span></span> <span data-ttu-id="2fe10-1275">Pokud chcete navazovat různé názvy hostitelů na různé ASP.NET Core aplikace na stejném portu, použijte [http. sys](xref:fundamentals/servers/httpsys) nebo reverzní proxy server, třeba IIS, Nginx nebo Apache.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1275">To bind different host names to different ASP.NET Core apps on the same port, use [HTTP.sys](xref:fundamentals/servers/httpsys) or a reverse proxy server, such as IIS, Nginx, or Apache.</span></span>

  > [!WARNING]
  > <span data-ttu-id="2fe10-1276">Hostování v konfiguraci reverzního proxy serveru vyžaduje [filtrování hostitele](#host-filtering).</span><span class="sxs-lookup"><span data-stu-id="2fe10-1276">Hosting in a reverse proxy configuration requires [host filtering](#host-filtering).</span></span>

* <span data-ttu-id="2fe10-1277">`localhost`Název hostitele s číslem portu nebo IP adresou zpětné smyčky s číslem portu</span><span class="sxs-lookup"><span data-stu-id="2fe10-1277">Host `localhost` name with port number or loopback IP with port number</span></span>

  ```
  http://localhost:5000/
  http://127.0.0.1:5000/
  http://[::1]:5000/
  ```

  <span data-ttu-id="2fe10-1278">Když `localhost` je zadaný, Kestrel se pokusí vytvořit propojení s rozhraními zpětné smyčky IPv4 i IPv6.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1278">When `localhost` is specified, Kestrel attempts to bind to both IPv4 and IPv6 loopback interfaces.</span></span> <span data-ttu-id="2fe10-1279">Pokud je požadovaný port používán jinou službou na rozhraní zpětné smyčky, spuštění Kestrel se nezdařilo.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1279">If the requested port is in use by another service on either loopback interface, Kestrel fails to start.</span></span> <span data-ttu-id="2fe10-1280">Pokud je rozhraní zpětné smyčky v žádném jiném případě nedostupné (většinou kvůli tomu, že protokol IPv6 není podporovaný), Kestrel zaznamená upozornění.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1280">If either loopback interface is unavailable for any other reason (most commonly because IPv6 isn't supported), Kestrel logs a warning.</span></span>

## <a name="host-filtering"></a><span data-ttu-id="2fe10-1281">Filtrování hostitele</span><span class="sxs-lookup"><span data-stu-id="2fe10-1281">Host filtering</span></span>

<span data-ttu-id="2fe10-1282">I když Kestrel podporuje konfiguraci na základě prefixů `http://example.com:5000` , jako je, Kestrel převážně ignoruje název hostitele.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1282">While Kestrel supports configuration based on prefixes such as `http://example.com:5000`, Kestrel largely ignores the host name.</span></span> <span data-ttu-id="2fe10-1283">Hostitel `localhost` je zvláštní případ, který se používá pro vazbu na adresy zpětné smyčky.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1283">Host `localhost` is a special case used for binding to loopback addresses.</span></span> <span data-ttu-id="2fe10-1284">Každý hostitel, který je jiný než explicitní IP adresa, se váže na všechny veřejné IP adresy.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1284">Any host other than an explicit IP address binds to all public IP addresses.</span></span> <span data-ttu-id="2fe10-1285">`Host`hlavičky nejsou ověřené.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1285">`Host` headers aren't validated.</span></span>

<span data-ttu-id="2fe10-1286">Jako alternativní řešení použijte middleware pro filtrování hostitele.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1286">As a workaround, use Host Filtering Middleware.</span></span> <span data-ttu-id="2fe10-1287">Middleware pro filtrování hostitele poskytuje balíček [Microsoft. AspNetCore. HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) , který je součástí [Microsoft. AspNetCore. App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2,1 nebo 2,2).</span><span class="sxs-lookup"><span data-stu-id="2fe10-1287">Host Filtering Middleware is provided by the [Microsoft.AspNetCore.HostFiltering](https://www.nuget.org/packages/Microsoft.AspNetCore.HostFiltering) package, which is included in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) (ASP.NET Core 2.1 or 2.2).</span></span> <span data-ttu-id="2fe10-1288">Middleware je přidána pomocí <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*> , který volá <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*> :</span><span class="sxs-lookup"><span data-stu-id="2fe10-1288">The middleware is added by <xref:Microsoft.AspNetCore.WebHost.CreateDefaultBuilder*>, which calls <xref:Microsoft.AspNetCore.Builder.HostFilteringServicesExtensions.AddHostFiltering*>:</span></span>

[!code-csharp[](kestrel/samples-snapshot/2.x/KestrelSample/Program.cs?name=snippet_Program&highlight=9)]

<span data-ttu-id="2fe10-1289">Middleware pro filtrování hostitele je ve výchozím nastavení zakázáno.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1289">Host Filtering Middleware is disabled by default.</span></span> <span data-ttu-id="2fe10-1290">Chcete-li povolit middleware, definujte `AllowedHosts` klíč v souboru *appSettings. JSON* / *appSettings. \<EnvironmentName> JSON*.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1290">To enable the middleware, define an `AllowedHosts` key in *appsettings.json*/*appsettings.\<EnvironmentName>.json*.</span></span> <span data-ttu-id="2fe10-1291">Hodnota je seznam názvů hostitelů oddělených středníkem bez čísel portů:</span><span class="sxs-lookup"><span data-stu-id="2fe10-1291">The value is a semicolon-delimited list of host names without port numbers:</span></span>

<span data-ttu-id="2fe10-1292">*appSettings. JSON*:</span><span class="sxs-lookup"><span data-stu-id="2fe10-1292">*appsettings.json*:</span></span>

```json
{
  "AllowedHosts": "example.com;localhost"
}
```

> [!NOTE]
> <span data-ttu-id="2fe10-1293">[Middleware předávaných hlaviček](xref:host-and-deploy/proxy-load-balancer) má také <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> možnost.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1293">[Forwarded Headers Middleware](xref:host-and-deploy/proxy-load-balancer) also has an <xref:Microsoft.AspNetCore.Builder.ForwardedHeadersOptions.AllowedHosts> option.</span></span> <span data-ttu-id="2fe10-1294">Middleware předávaných hlaviček a middleware pro filtrování hostitele mají podobné funkce pro různé scénáře.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1294">Forwarded Headers Middleware and Host Filtering Middleware have similar functionality for different scenarios.</span></span> <span data-ttu-id="2fe10-1295">Nastavení `AllowedHosts` pomocí předávaných middlewarových hlaviček je vhodné v případě, že `Host` Hlavička není zachována při předávání požadavků pomocí reverzního proxy server nebo nástroje pro vyrovnávání zatížení.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1295">Setting `AllowedHosts` with Forwarded Headers Middleware is appropriate when the `Host` header isn't preserved while forwarding requests with a reverse proxy server or load balancer.</span></span> <span data-ttu-id="2fe10-1296">Nastavení `AllowedHosts` pomocí middlewaru pro filtrování hostitele je vhodné, když se Kestrel používá jako veřejný hraniční Server nebo když se `Host` Hlavička přímo přepošle.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1296">Setting `AllowedHosts` with Host Filtering Middleware is appropriate when Kestrel is used as a public-facing edge server or when the `Host` header is directly forwarded.</span></span>
>
> <span data-ttu-id="2fe10-1297">Další informace o middlewaru předávaných hlaviček najdete v tématu <xref:host-and-deploy/proxy-load-balancer> .</span><span class="sxs-lookup"><span data-stu-id="2fe10-1297">For more information on Forwarded Headers Middleware, see <xref:host-and-deploy/proxy-load-balancer>.</span></span>

::: moniker-end

## <a name="http11-request-draining"></a><span data-ttu-id="2fe10-1298">Vyprazdňování požadavku HTTP/1.1</span><span class="sxs-lookup"><span data-stu-id="2fe10-1298">HTTP/1.1 request draining</span></span>

<span data-ttu-id="2fe10-1299">Otevírání připojení HTTP je časově náročné.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1299">Opening HTTP connections is time consuming.</span></span> <span data-ttu-id="2fe10-1300">V případě protokolu HTTPS je také náročný na prostředky.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1300">For HTTPS, it's also resource intensive.</span></span> <span data-ttu-id="2fe10-1301">Proto se Kestrel pokusí znovu použít připojení na protokol HTTP/1.1.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1301">Therefore, Kestrel tries to reuse connections per the HTTP/1.1 protocol.</span></span> <span data-ttu-id="2fe10-1302">Aby bylo možné připojení znovu použít, musí být text žádosti plně spotřebovaný.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1302">A request body must be fully consumed to allow the connection to be reused.</span></span> <span data-ttu-id="2fe10-1303">Aplikace nespotřebovává text žádosti vždycky, například `POST` žádosti, kde server vrací přesměrování nebo odpověď 404.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1303">The app doesn't always consume the request body, such as a `POST` requests where the server returns a redirect or 404 response.</span></span> <span data-ttu-id="2fe10-1304">V `POST` případě případu přesměrování:</span><span class="sxs-lookup"><span data-stu-id="2fe10-1304">In the `POST`-redirect case:</span></span>

* <span data-ttu-id="2fe10-1305">Klient již mohl odeslat část `POST` dat.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1305">The client may already have sent part of the `POST` data.</span></span>
* <span data-ttu-id="2fe10-1306">Server zapisuje odpověď 301.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1306">The server writes the 301 response.</span></span>
* <span data-ttu-id="2fe10-1307">Připojení nelze použít pro novou žádost, dokud nebudou `POST` data z předchozího textu požadavku plně přečtena.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1307">The connection can't be used for a new request until the `POST` data from the previous request body has been fully read.</span></span>
* <span data-ttu-id="2fe10-1308">Kestrel se pokusí vyprázdnit tělo žádosti.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1308">Kestrel tries to drain the request body.</span></span> <span data-ttu-id="2fe10-1309">Vyprázdnit tělo žádosti znamená čtení a zahození dat bez jejich zpracování.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1309">Draining the request body means reading and discarding the data without processing it.</span></span>

<span data-ttu-id="2fe10-1310">Proces vyprazdňování vytváří tradoff mezi povolením opětovného použití připojení a časem potřebným k vyprázdnění zbývajících dat:</span><span class="sxs-lookup"><span data-stu-id="2fe10-1310">The draining process makes a tradoff between allowing the connection to be reused and the time it takes to drain any remaining data:</span></span>

* <span data-ttu-id="2fe10-1311">Vyprázdnění má časový limit pět sekund, což není konfigurovatelné.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1311">Draining has a timeout of five seconds, which isn't configurable.</span></span>
* <span data-ttu-id="2fe10-1312">Pokud nebyla všechna data zadaná v `Content-Length` `Transfer-Encoding` hlavičce nebo přečtena před časovým limitem, připojení je zavřeno.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1312">If all of the data specified by the `Content-Length` or `Transfer-Encoding` header hasn't been read before the timeout, the connection is closed.</span></span>

<span data-ttu-id="2fe10-1313">Někdy je možné, že budete chtít žádost ukončit hned nebo po zápisu odpovědi.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1313">Sometimes you may want to terminate the request immediately, before or after writing the response.</span></span> <span data-ttu-id="2fe10-1314">Například klienti mohou mít omezující data, takže omezení nahraných dat může být prioritou.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1314">For example, clients may have restrictive data caps, so limiting uploaded data might be a priority.</span></span> <span data-ttu-id="2fe10-1315">V takových případech pro ukončení žádosti zavolejte [HttpContext. Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) z kontroleru, Razor stránky nebo middlewaru.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1315">In such cases to terminate a request, call [HttpContext.Abort](xref:Microsoft.AspNetCore.Http.HttpContext.Abort%2A) from a controller, Razor Page, or middleware.</span></span>

<span data-ttu-id="2fe10-1316">Existují upozornění pro volání `Abort` :</span><span class="sxs-lookup"><span data-stu-id="2fe10-1316">There are caveats to calling `Abort`:</span></span>

* <span data-ttu-id="2fe10-1317">Vytváření nových připojení může být pomalé a nákladné.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1317">Creating new connections can be slow and expensive.</span></span>
* <span data-ttu-id="2fe10-1318">Není zaručeno, že klient před zavřením připojení přečetl odpověď.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1318">There's no guarantee that the client has read the response before the connection closes.</span></span>
* <span data-ttu-id="2fe10-1319">Volání `Abort` by mělo být vzácné a vyhrazené pro závažné chybové případy, ne běžné chyby.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1319">Calling `Abort` should be rare and reserved for severe error cases, not common errors.</span></span>
  * <span data-ttu-id="2fe10-1320">Volá se jenom v případě, že je `Abort` potřeba vyřešit konkrétní problém.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1320">Only call `Abort` when a specific problem needs to be solved.</span></span> <span data-ttu-id="2fe10-1321">Například volejte, `Abort` Pokud se zlomyslní klienti pokoušejí `POST` data nebo když dojde k chybě v kódu klienta, která způsobuje velké nebo velké požadavky.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1321">For example, call `Abort` if malicious clients are trying to `POST` data or when there's a bug in client code that causes large or numerous requests.</span></span>
  * <span data-ttu-id="2fe10-1322">Nevolejte `Abort` pro běžné chybové situace, například HTTP 404 (Nenalezeno).</span><span class="sxs-lookup"><span data-stu-id="2fe10-1322">Don't call `Abort` for common error situations, such as HTTP 404 (Not Found).</span></span>

<span data-ttu-id="2fe10-1323">Volání [HttpResponse. CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) před voláním `Abort` zajistí, že server dokončil zápis odpovědi.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1323">Calling [HttpResponse.CompleteAsync](xref:Microsoft.AspNetCore.Http.HttpResponse.CompleteAsync%2A) before calling `Abort` ensures that the server has completed writing the response.</span></span> <span data-ttu-id="2fe10-1324">Nicméně chování klienta není předvídatelné a nemusí před přerušením připojení číst odpověď.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1324">However, client behavior isn't predictable and they may not read the response before the connection is aborted.</span></span>

<span data-ttu-id="2fe10-1325">Tento proces se liší od protokolu HTTP/2, protože protokol podporuje přerušování jednotlivých datových proudů požadavků bez ukončení připojení.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1325">This process is different for HTTP/2 because the protocol supports aborting individual request streams without closing the connection.</span></span> <span data-ttu-id="2fe10-1326">Nedá se použít 5 sekundový časový limit vyprázdnění.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1326">The five second drain timeout doesn't apply.</span></span> <span data-ttu-id="2fe10-1327">Pokud po dokončení odpovědi dojde k nepřečteným datům textu žádosti, server odešle snímek HTTP/2 RST.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1327">If there's any unread request body data after completing a response, then the server sends an HTTP/2 RST frame.</span></span> <span data-ttu-id="2fe10-1328">Další datové snímky požadavku jsou ignorovány.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1328">Additional request body data frames are ignored.</span></span>

<span data-ttu-id="2fe10-1329">Pokud je to možné, je lepší, aby klienti využili hlavičku žádosti [očekávat: 100-pokračovat](https://developer.mozilla.org/docs/Web/HTTP/Status/100) a čekali na odpověď serveru před zahájením odesílání textu žádosti.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1329">If possible, it's better for clients to utilize the [Expect: 100-continue](https://developer.mozilla.org/docs/Web/HTTP/Status/100) request header and wait for the server to respond before starting to send the request body.</span></span> <span data-ttu-id="2fe10-1330">Díky tomu může klient příležitost před odesláním nepotřebných dat ověřit odpověď a zrušit jejich přerušení.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1330">That gives the client an opportunity to examine the response and abort before sending unneeded data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2fe10-1331">Další zdroje</span><span class="sxs-lookup"><span data-stu-id="2fe10-1331">Additional resources</span></span>

* <span data-ttu-id="2fe10-1332">Při použití soketů UNIX v systému Linux se soket při vypnutí aplikace automaticky neodstraní.</span><span class="sxs-lookup"><span data-stu-id="2fe10-1332">When using UNIX sockets on Linux, the socket is not automatically deleted on app shut down.</span></span> <span data-ttu-id="2fe10-1333">Další informace najdete v [tomto problému GitHubu](https://github.com/dotnet/aspnetcore/issues/14134).</span><span class="sxs-lookup"><span data-stu-id="2fe10-1333">For more information, see [this GitHub issue](https://github.com/dotnet/aspnetcore/issues/14134).</span></span>
* <xref:test/troubleshoot>
* <xref:security/enforcing-ssl>
* <xref:host-and-deploy/proxy-load-balancer>
* [<span data-ttu-id="2fe10-1334">RFC 7230: syntaxe zprávy a směrování (oddíl 5,4: hostitel)</span><span class="sxs-lookup"><span data-stu-id="2fe10-1334">RFC 7230: Message Syntax and Routing (Section 5.4: Host)</span></span>](https://tools.ietf.org/html/rfc7230#section-5.4)
